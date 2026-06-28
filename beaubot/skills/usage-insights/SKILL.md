---
name: usage-insights
description: Generate business-intelligence reports on how students and teachers use the Beau platform — engagement, content authoring, and commerce — from the org-scoped analytics event log via MCP. For org admins. Produces periodic, evidence-based usage summaries.
audience: [admin]
user_invocable: true
---

# Usage Insights Skill

This skill helps an **organization admin** understand how their org uses Beau, by
reporting over the platform's append-only analytics event log. It produces
periodic, evidence-based summaries: student **engagement**, content **authoring**
(including human-vs-AI), and **commerce** (enrollments + revenue), plus answers to
ad-hoc questions.

It is **read-only** and **org-scoped**: every tool only ever sees the caller's own
organization. All tools require the **`read:analytics`** permission (admin-only) —
teachers and students cannot use them.

## Instructions for Claude

### 0. Preflight check

Confirm access by calling `get_engagement_report` with no arguments (defaults to
the last 30 days).

- If it returns a tool error mentioning authentication, tell the user: "The Beau
  MCP server is not connected — please check your MCP connection and authenticate."
- If it returns a **403 / permission** error, tell the user: "Your account
  doesn't have the `read:analytics` permission. An org admin must grant it (it's
  admin-only)." Then stop.

### 1. Determine scope

Ask the user (unless they already said):
- **Report type** — Engagement, Content, Commerce, an Overview (all three), or a
  specific ad-hoc question.
- **Time window** — e.g. last 7/30/90 days, a specific month, or a custom range.
  Convert to ISO dates (`from`/`to`). Default is the trailing 30 days.

### 2. Pull the data

Use the **curated report tools** for standard reports (one call each, pass
`from`/`to`):

| Tool | Returns |
|------|---------|
| `get_engagement_report` | lessons started/completed, completion rate, active students, avg score, daily completions (test runs excluded) |
| `get_content_report` | resources created (human vs AI), AI-authored, quizzes, visuals vs media, courses |
| `get_commerce_report` | enrollments, payments, revenue (cents), platform fees, refunds |

For **ad-hoc questions**, use `aggregate_events`:
- `groupBy` ∈ `event | actorType | subjectType | day | week | month`
- `metric` ∈ `count | sum | avg` (sum/avg need a `field` ∈ `amountCents | platformFeeCents | score | durationSec`)
- optional `events` (names to filter), `from`, `to`
- Examples: lessons completed per week → `{events:['lesson.completed'], groupBy:'week', metric:'count'}`; revenue per month → `{events:['payment.completed'], groupBy:'month', metric:'sum', field:'amountCents'}`.

For **drill-down** (inspect individual rows after an aggregate looks surprising),
use `query_events` (filter by `events`, `from`/`to`, `actorId`, `subjectType`,
`subjectId`; paginate with `limit`/`offset`).

### 3. Interpret correctly

- **Money is in cents** — divide by 100 and show the currency.
- **Completion rate** = completed ÷ started; call out low rates.
- **Test runs are already excluded** from engagement (teacher previews don't
  count as student activity).
- **Human vs AI authoring** — `resourcesByAgent` / `aiAuthored` are content made
  via the AI assistant or MCP; `resourcesByHuman` are dashboard edits.
- **Empty windows** are normal for new orgs — say so rather than implying a problem.

### 4. Produce the report

Write a concise markdown report:
- A short headline summary (2–3 sentences).
- One section per requested area with the key numbers and a trend note (use the
  daily/weekly series).
- 2–4 concrete observations or recommendations grounded in the numbers (e.g. "37%
  of started lessons go uncompleted — consider shorter lessons or a nudge").
- Note the window and that figures are for this organization only.

Do **not** invent numbers or fill gaps — if a tool returned zero or null, report
it as such.

## Event reference (for `aggregate_events` / `query_events`)

The log records these event families (org-scoped). Names are stable:

- **Learning**: `lesson.started`, `lesson.completed`, `feedback.submitted`, `contact_teacher.triggered`
- **Authoring** (CRUD): `resource.created/updated/deleted`, `bot.*`, `course.*`, `image.*`, `quiz.created`, `course.resource_added`, `resource.snapshot_saved`, `resource.exported`, `ai_assistant.resource_authored`
- **Enrollment/commerce**: `enrollment.created/paused/resumed/completed`, `payment.initiated/completed/refunded`
- **Identity/org**: `user.invited/invitation_accepted/login`, `consent.submitted/verified`, `org.settings_changed/stripe_onboarded`
- **Sharing**: `share_link.created/consumed/revoked`
- **Comms**: `notification.sent` (every outbound email)

Useful properties for sum/avg: `amountCents`, `platformFeeCents` (payments),
`score`, `durationSec` (lesson.completed).
