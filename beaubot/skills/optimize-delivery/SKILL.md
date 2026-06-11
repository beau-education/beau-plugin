---
name: optimize-delivery
description: Platform/superadmin workflow for hill-climbing voice-lesson DELIVERY quality. Reads per-lesson delivery config, responsiveness telemetry, transcripts, and an LLM quality judge to find what to improve and to compare experiment arms. Not a teacher tool.
audience: [superadmin]
user_invocable: true
---

# Optimize Delivery Skill

This skill drives the **lesson-delivery optimization loop** for the Beau platform: measure how responsive/clear voice lessons are, tie each lesson to the exact config (model, voice, turn-detection, prompt version, git commit) that produced it, surface soft signals of trouble (student confusion, mistimed/missing media, pacing), and compare configurations over time.

**Audience: platform operators (superadmin) only.** This is about *us* improving the tutor's delivery — not a teacher evaluating a student (that's `evaluate-student`). Every tool here is **cross-org and superadmin-gated**: a non-superadmin session gets 403.

## Key concepts

- **Attempt = one lesson run**, identified by its `courseProgress` id. Everything joins on this id.
- **Stamp**: each attempt records its `deliveryConfig` (resolved model/voice/turn-detection/`promptTemplateVersion`, the actual `botId`/`botVoice`, and the `gitCommit` of the prompt-assembly code) plus an `experimentArm` (today a single `none:default`; Phase 2 will vary arms).
- **Primary metric = first-audio latency**: ms from the server VAD marking the student's turn over (`speech_stopped`) to the bot's first audio. It **excludes** the fixed VAD silence window (~1500 ms) — it measures server+model+network responsiveness, not the full human-perceived gap.
- **Guardrails**: interruption rate, nudge count, cost, completion, quiz accuracy.
- **Test runs count.** Teacher test runs are captured (`isTest=true`) and are valid samples for the responsiveness metric (it's a property of the system, not the learner), so you have data before real-student volume. Segment them out for learning-outcome reads.

## Instructions for Claude

### 0. Preflight
Confirm a **superadmin** MCP session by calling `get_delivery_baseline`. If it returns 403/permission denied, tell the user this skill requires a platform superadmin session and stop. If the MCP server isn't connected, say so and stop.

### 1. Read the current baseline
Call **`get_delivery_baseline`** (optionally `{ organization }` to scope to one org). It returns, segmented **pooled / real / test**, and **per arm** and **per org**:
- sample count `n`, median first-audio latency (p50/p95, ms), avg interruptions, nudges, turns, cost, duration.

Interpret:
- Read the **responsiveness** numbers on the **pooled** set (test runs add power).
- Read learning-outcome guardrails on **real** only (test population is biased).
- Flag low `n` — don't over-read thin samples.
- With one arm (`none:default`) this is simply "current state / the baseline to beat." Once arms vary, compare `byArm`.

### 2. Drill into specific lessons
For an attempt id, call **`get_lesson_delivery <progressId>`** — returns the stamped config + bot/voice, telemetry, `promptLength`, and the **latest stored judge result** (`quality`) inline. Use it to ask "what config ran here, and how did it score?"
- Need the verbatim prompt? **`get_lesson_prompt <progressId>`** (chunked; page with `offset`/`limit`).
- Need the full evaluation record (config + telemetry + **transcript** + plan)? **`get_evaluation_bundle <progressId>`** — this is the join surface for reasoning about *timing* (e.g. media shown vs. a confused message).

### 3. Judge delivery quality
- To **(re)run** the LLM judge on an attempt: **`judge_lesson <progressId>`** (costs an OpenAI call on the platform key; persists the result). It returns rubric scores 1–5 (`pacing`, `responsiveness`, `clarity`, `pedagogy`, `errorRecovery`), an `overall`, and **flags** with evidence quotes — including `confusion`, `display_timing`, `missing_media`, `pacing`.
- To **view** an already-judged attempt without re-judging: **`get_lesson_quality <progressId>`** (returns all `judgeVersion`s, newest first). Prefer this for reading; use `judge_lesson` only to score or re-score.

### 4. Synthesize
Tie findings back to **config**: which `experimentArm` / model / voice / turn-detection / `promptTemplateVersion` correlates with worse latency or more confusion/display flags? Use the `gitCommit` to inspect the exact prompt-assembly code (`git show <sha>:src/app/utils/promptBuilder.ts`). Recommend a concrete next change (e.g. a turn-detection or prompt-template variant) and the metric + sample size needed to detect it.

## Workflow at a glance
1. `get_delivery_baseline` → find the weak metric / arm / org.
2. `get_lesson_delivery` / `get_lesson_quality` → drill into representative attempts.
3. `judge_lesson` (if not yet judged) → soft-signal flags with evidence.
4. `get_evaluation_bundle` → reason about media/quiz timing vs. the transcript.
5. Recommend a config/prompt change to test next.

## Act — create + launch an experiment (Insight → Action)
When a finding suggests a delivery change, turn it into a live A/B — **as data, no code deploy**. Suggest first; only create/launch on the user's explicit approval.

1. **Propose** control vs variant: the hypothesis, the section/rule to change, the **one** primary metric, and the split.
2. **`create_prompt_template`** — author the variant's prompt change. Prefer **`appendRules`** to *add* a rule (e.g. "When showing media, say the task in one sentence, then display it, then ask the student what to do"); use **`sections`** (`conversationFlow` / `toolGuidance` / `verbosity`) to fully replace a section. Safety and tool registration can't be overridden.
3. **`create_experiment`** (optionally `targetDeliveryMode` / `targetResource`) — starts in `draft`.
4. **`add_experiment_arm`** at least twice: a **control** arm (no template, default config) and the **variant** arm (the template + any `turnDetection`/`temperature` changes), with `weight`s (e.g. 50/50).
5. **`set_experiment_status(active)`** to launch. Runs are then assigned to arms — **sticky** for real students, **per-attempt** for tests — and stamped on each transcript.
6. **QA one arm** by opening the player with **`?armId=<id>`** (forced; excluded from analysis).

Always include a **control** arm; pre-register ONE primary metric; **never auto-launch** — confirm with the user. To stop: `set_experiment_status(done)`.

## Evaluate — compare the arms
Once arms have runs, **`compare_experiment_arms(experimentId)`** returns per-arm metrics segmented **pooled / real / test**: n, first-audio latency p50/p95, interruption/nudge/turn averages, completion rate, cost, and the judge summary (avg `overall`, avg per-dimension scores, flag counts like `display_timing`/`confusion`).
- Read the **responsiveness primary metric (first-audio latency) on POOLED** — it's population-independent, so teacher tests add power.
- Read **learning-outcome guardrails (completion) on REAL only** — the test population is biased.
- **Gate on n + a confidence flag**; report against the ONE pre-registered primary metric — don't fish across metrics with thin samples.
- **Promotion is manual** — no tool changes the default; recommend promote/keep/iterate and let the user act. Stop a finished experiment with `set_experiment_status(done)`.

## Current limitations
- **Model arms don't apply yet:** prompt overrides + session config (turn detection / transcription / temperature) take effect; an arm that sets `realtimeModel` is stamped but not yet wired to the session mint — so vary **prompt + turn-detection** for now. (Voice is a per-bot setting, not an arm dimension.)
- `compare_experiment_arms` doesn't yet aggregate **quiz eventual-correct**, and `latencyMeasuredRatio` is a proxy (sessions with p50>0) until per-sample counts are captured.
- The judge uses the platform OpenAI key (gpt-4o); `judge_lesson` costs a few cents per lesson — judge deliberately, not in bulk.
- Bump `JUDGE_VERSION` in the API when the rubric changes so old/new judgements stay distinct (`get_lesson_quality` returns per-version).
