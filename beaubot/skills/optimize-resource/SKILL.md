---
name: optimize-resource
description: Help a teacher improve a RESOURCE by analyzing how students actually experienced it — transcripts, quiz pass rates, common wrong answers, and feedback — then suggesting concrete content edits. Suggests first; only applies changes the teacher explicitly approves. Does not touch prompts or model config.
audience: [teacher, admin]
user_invocable: true
---

# Optimize Resource Skill

This skill helps a teacher make a specific resource **better for students**, based on evidence of how it actually played out: where students got confused, which quizzes they failed, what wrong answers they gave, and what feedback they left. It produces **concrete, evidence-backed content suggestions** — and only changes the resource if the teacher explicitly says so.

**Scope — read this carefully:**
- ✅ In scope: the resource's **content** — wording/clarity of sections, quiz questions/answers/hints, images and visuals, ordering, missing context, length/pacing of a step.
- ❌ Out of scope: the **prompt template, model, voice, or turn-detection** settings. Those are platform-level and a teacher can't change them. If a problem is clearly about *delivery* (e.g. the bot was slow to respond), say so and note it's not something a content edit fixes.
- 🚫 **Never auto-edit.** Always present suggestions first. Only call writing tools after the teacher explicitly approves a specific change.

## Instructions for Claude

### 0. Preflight
Verify the MCP server is connected by calling `list_resources(pageSize: 1)`. If it errors, tell the user the Beau MCP server isn't connected and stop.

### 1. Pick the resource
Ask which resource to optimize (name, or let them pick from `list_resources`). Resolve to a `resourceId`.

### 2. Gather the evidence
- **`get_resource(resourceId)`** — the current content (markdown, quizzes, media). You need this to ground every suggestion in what's actually there.
- **`get_resource_insights(resourceId)`** — aggregate signals across all attempts:
  - completion + score (avg/median),
  - per-quiz **first-attempt pass rate**, **ever-correct rate**, **avg attempts**, and **most common wrong answers**,
  - feedback themes (👍/👎, issue tags, comments),
  - `attemptsToReview`: progress ids (lowest scores first).
- **`get_transcript(progressId)`** on **a few** of the lowest-scoring `attemptsToReview` (e.g. 3–5) — read the qualitative story: where students hesitated, asked "what?", said they couldn't see something, or went off track. The transcript is a timestamped, interleaved log of bot/student messages, «MEDIA shown», «QUIZ shown», and answers — so you can see *when* confusion happened relative to a media/quiz.
  - Prefer **real** attempts for learning signals; test attempts are fine for spotting broken content (a confusing quiz is confusing regardless).

### 3. Diagnose (tie every finding to evidence)
Look for, and cite the signal for, each:
| Signal | Likely content problem |
|--------|------------------------|
| Quiz with low first-attempt pass rate + a clustered **common wrong answer** | Ambiguous question wording, a misleading/duplicate option, or a wrong/over-strict expected answer; weak or missing hint. |
| Students reference something they "can't see" / confusion right after a section | A concept needs an **image/visual**, or the explanation before it is unclear. |
| Repeated "I don't get it" / re-asking on the same step | That section needs rewording, a concrete example, or splitting into smaller steps. |
| 👎 feedback + issue tags / comments | Read them literally; they often name the problem. |
| Long section with drop-off / low completion | Trim or split; front-load the point. |

### 4. Suggest (always first)
Present a short, prioritized list. For each: **what** to change, **where** (section/quiz id), **why** (the evidence — a pass rate, a wrong-answer cluster, a transcript quote), and the **proposed new text/answer/hint or media**. Be specific enough that the teacher can say yes/no.

### 5. Offer to apply — only on explicit approval
Ask which suggestions to apply. For each approved one, use the authoring tools:
- Content/markdown → **`update_resource`**.
- Quiz wording/answers/hint → **`update_quiz`**.
- New illustration → **`create_visual`** / **`create_image`** / **`generate_image`**, then reference it in the content.
Apply **only** what was approved; never bundle in unapproved changes. After applying, summarize exactly what changed and suggest the teacher re-test the resource.

## Boundaries
- Suggest-then-apply, **never auto-apply**.
- Don't invent signals — if there are too few attempts (`attempts.total` is small), say the evidence is thin and suggest cautiously.
- Keep delivery/prompt/model concerns out; redirect those to a platform admin.
