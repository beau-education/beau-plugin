---
name: create-resource
description: Create educational content (resources and courses) for the Beau platform. Use when the user wants to create resources, courses, quizzes, or upload images/PDFs for voice delivery.
user_invocable: true
---

# Create Resource Skill

This skill helps you create educational resources for the Beau platform. Resources are the building blocks of your curriculum - each is a self-contained learning unit delivered to students as a voice conversation, a narrated presentation, or a no-bot worksheet (self-paced screens / printable handout written for the student). Resources are grouped into courses.

## Instructions for Claude

When this skill is invoked, follow these steps:

0. **Preflight Check**: Verify the beaubot MCP server is connected by calling `list_tags(pageSize: 1)`. If this fails with a tool error, tell the user: "The Beau MCP server is not connected. Please check your MCP connection and authenticate if prompted." Then stop.

1. **Tag Governance** (before creating anything):
   a. Call `list_tags(pageSize: 50)` to fetch the full tag catalog
   b. Reuse existing tags where possible — do not create near-duplicates (e.g. "maths" vs "math")
   c. For genuinely new tags: ask the user to confirm, then call `create_tag(name)` to add them
   d. Only use tags that exist in the catalog when calling `create_resource`

1. **Gather Requirements**: Ask the user about:
   - Topic/subject for the resource
   - Target audience (age, skill level)
   - Delivery mode preference: conversation (two-way voice), presentation (one-way narration), or worksheet (no bot — self-paced screens / printable, written FOR THE STUDENT). This changes who the content is written for: conversation/presentation are written for the bot; a worksheet is written directly to the student.
   - Desired length (default: 5-10 minutes)
   - Any specific learning objectives
   - Whether they want AI-generated images or have URLs to images/PDFs

2. **Design the Resource**: Based on the guides, create a structure with:
   - Clear learning objectives
   - Logical sections with headings
   - **A quiz after every major concept or section** — aim for at least 2-3 quizzes per resource. Quizzes are the primary way students actively engage with the material, and lessons without them feel passive and text-heavy.
   - **At least 1-2 images per resource** — visuals break up text, illustrate concepts, and give the bot something concrete to discuss with the student. A resource with no images is almost always too text-heavy.
   - Mix quiz types for variety (single choice, multiple choice, open answer, fraction, ordered list, matching)

3. **Create Content**: Draft the markdown content following best practices:
   - Write instructions for the bot, not a script
   - Use "the student" with they/them pronouns
   - Keep sections digestible
   - **IMPORTANT: Avoid text-heavy resources.** Every section should either have a quiz, an image, or both. If a section is just text with no interactivity, consider adding a quick quiz to check understanding or an image to illustrate the concept. Students learn best when they actively participate, not passively listen.
   - **If a quiz refers to a figure, attach that figure to the quiz** (its `image` field), don't just place it inline before `::quiz{#id}`. Then the diagram and the question are on screen together while the student answers. See *Quiz Illustrations*.

4. **Source Media**: Find or create visuals for the resource. **Every resource should have images** — they are essential for engagement, not optional decoration.
   - **Do NOT generate images via Python/code** — this is too slow and produces poor results
   - **Use the `text` visual** (`create_visual` with `kind: "text"`) for vocabulary cards, sight words, labels and key terms, and the **`math` visual** for equations — these are instant, vector (crisp at any size), and teacher-editable. **`create_text_image` is deprecated — never use it.**
   - **Use `generate_image`** only for custom educational illustrations / photoreal scenes — server-side AI generation; slower, so reserve it for when a real raster picture is genuinely needed
   - **Use `upload_image_from_url`** for existing public images (Wikimedia, educational sites)
   - **Use `create_image` (base64) only as a last resort** for user-provided local files
   - **Verify every image**: After generation/upload, view the image to confirm it matches intent
   - Write description/question/answer/hint based on what the image ACTUALLY shows, not what you intended
   - For PDFs: description is critical since the bot cannot read PDF content

5. **Execute Creation**: Use MCP tools to:
   - Create the resource via `create_resource`
   - Upload any media files via `upload_image_from_url` or `create_image`
   - Create quizzes via `create_quiz`
   - **CRITICAL**: Update resource content via `update_resource` to embed image and quiz references using the correct markdown syntax (see below)
   - **Set a cover image**: pick the most representative image for the resource and set it via `update_resource(id, coverImage: imageId)`. Cover images are shown to students when the lesson starts and make the catalog feel less like a wall of text. Do this in the same `update_resource` call as the content update.
   - Optionally create a course and add the resource to it

6. **Provide Testing Instructions**: Tell the user how to test their resource using the Test Resource button in the admin UI

## MCP Tools

Use the `beaubot` MCP server tools:

### Available Tools

| Tool | Description |
|------|-------------|
| `list_resources` | List existing resources with optional tag filtering |
| `get_resource` | Fetch a resource by ID |
| `create_resource` | Create a new resource |
| `update_resource` | Update an existing resource |
| `get_image` | Download an image by ID — returns the image visually (for inspection) plus metadata |
| `generate_image` | Generate an AI image from a text prompt and attach to a resource (uses org's OpenAI key, preferred) |
| `create_text_image` | **DEPRECATED — do not use.** For words/phrases/labels use the `text` visual (`create_visual` with `kind: "text"`) — vector, instant, editable. |
| `create_visual` | Create a teacher-authored visual tool — number line, fraction, grid, timeline, math equation, word/text card, or counters — from a small JSON config (no AI, renders as crisp SVG). Embed with `::visual{#id}` |
| `update_visual` | Update an existing visual tool's config or metadata |
| `create_svg` | Draw a scalable SVG image from raw markup (crisp, responsive, no AI). Best when no `create_visual` kind fits but it can be drawn with shapes/lines/text. Embed with `![id](…)` |
| `upload_image_from_url` | Upload an image or PDF from a URL to a resource |
| `prepare_image_upload` | Mint a single-use multipart upload URL for a local file (preferred over `create_image` for files on disk) |
| `create_image` | Upload an image or PDF (base64 data) to a resource (last resort) |
| `create_quiz` | Create a quiz for a resource |
| `update_quiz` | Update an existing quiz (fix typos, attach an illustration image, etc.) |
| `list_tags` | List available tags in the organization's catalog |
| `create_tag` | Create a new tag in the organization's tag catalog |
| `list_bots` | List the org's bots with their voice, avatar, and persona — call to pick a bot whose persona suits the subject |
| `get_bot` | Fetch a single bot's full config (including system prompt) by ID |
| `export_resource` | Export a resource as a base64-encoded ZIP (includes all media and quizzes) |
| `import_resource` | Import a resource from a base64-encoded ZIP (creates a new resource) |
| `create_course` | Create a new course (collection of resources) |
| `list_courses` | List existing courses |
| `add_resource_to_course` | Add a resource to a course |
| `list_course_resources` | List resources already in a course (with details) |

### Choosing how to show something visual

When a lesson needs a picture or diagram, pick the **most specific** option — in this order. Going higher up the list gives crisper, more responsive, more editable results, so don't drop to a raster image out of habit.

1. **A dedicated visual tool — `create_visual` (best).** If the concept maps to a built-in kind, use it: number line, fraction, grid, timeline, math (KaTeX), word/text, counters, clock, ten frame, bar chart, base-ten blocks, money, phoneme frame, place value, **Chart.js chart**, function graph, geometry, coordinate grid, box plot, probability tree, atom (Bohr), pH scale, syllable split, onset/rime. These are purpose-built, teacher-editable, and render responsively. For arbitrary data charts the `chart` kind (Chart.js) covers bar/line/pie/scatter/etc.
2. **A hand-drawn SVG — `create_svg` (good fallback).** If **no** visual kind fits but the thing can be expressed with shapes, lines and text — a labelled diagram, a custom illustration, a flowchart, a simple map, a process diagram — write an SVG. It stays sharp at any size and is lightweight. **Prefer this over a raster image.**
3. **A raster image (last resort).** Only when you truly need a *photograph* or a richly detailed picture that can't be vector-drawn: `generate_image` (AI) or `prepare_image_upload` / `create_image` (an existing file). Supported raster uploads: PNG and JPEG. (SVG uploads are supported too, but use `create_svg` rather than uploading SVG by hand.)

All of these embed the same way in the content — `![ID](/api/v1/images/ID/data)` for images and SVGs, `::visual{#ID}` for visual tools.

### Workflow

```
0. list_tags(pageSize: 50) → Fetch org's tag catalog
   create_tag(name) → Create new tags (after user confirmation)

1. create_resource(name, content, tags, deliveryMode)
   → Returns resource with ID (tags must come from the catalog)

2. create_visual(resourceId, kind, config)
   → Vector visual — use kind "text" for words/labels/vocabulary, "math" for equations,
     or any other kind for diagrams. Instant, editable. PREFERRED for words. Embed with ::visual{#id}.
   OR
   generate_image(resourceId, prompt, description, question, answer, hint, botVisible)
   → AI-generated image — only for photoreal illustrations/scenes
   OR
   upload_image_from_url(resourceId, url, description, question, answer, hint, botVisible)
   → Downloads and attaches image from URL
   OR (for a file on local disk)
   prepare_image_upload(resourceId) → { uploadUrl }; then `curl -F file=@/path "$uploadUrl"`
   → Single-use multipart upload, no base64 round-trip
   OR (last resort)
   create_image(resourceId, name, mimeType, data, description, question, answer, hint, botVisible)
   → Uploads base64-encoded image

2b. create_visual(resourceId, kind, config, ...)
   → Authored visual tool (number line, fraction, grid, timeline, math, text, counters)
   → Renders as crisp SVG; embed in content with ::visual{#id}. Prefer this over an image
     whenever the content is structured data the platform can draw.

3. create_quiz(resourceId, question, questionType, answers, image?, ...)
   → Returns quiz with ID
   → Pass image: <imageId> to attach an illustration (image must be on the same resource)
   OR
   update_quiz(resourceId, id, image?, ...)
   → Update an existing quiz — fix typos or attach an illustration after generating it

4. update_resource(id, content, coverImage?)
   → CRITICAL: Update content to include image and quiz references
   → Optionally set coverImage to an image ID to give the resource a cover

5. (Optional) create_course(name, description, tags, progressionType)
   → Returns course with ID

6. (Optional) list_course_resources(courseId)
   → View existing resources in a course before adding

7. (Optional) add_resource_to_course(courseId, resourceId, order)
   → Adds resource to the course

8. (Optional) export_resource(resourceId)
   → Returns base64-encoded ZIP for backup or sharing

9. (Optional) import_resource(zipData)
   → Creates a new resource from a previously exported ZIP
```

### Tool Parameters

**get_image:**
- `imageId` (required): The image ID to download
- Returns: For images (`image/png`, `image/jpeg`), returns the image visually plus a text label. For PDFs/videos, returns metadata only:
  ```json
  {
    "id": 42,
    "name": "dog-breeds.png",
    "mimeType": "image/png",
    "description": "Common dog breeds",
    "question": "Can you identify these breeds?",
    "answer": "Labrador, Poodle, German Shepherd",
    "hint": "Look at the ear shapes",
    "botVisible": true,
    "resource": 15,
    "organization": 3,
    "createdAt": "2026-03-15T10:30:00.000Z",
    "updatedAt": "2026-03-15T10:30:00.000Z"
  }
  ```

**create_resource:**
- `name` (required): Display name for the resource
- `content` (required): Markdown content
- `tags` (optional): Array of tags — must come from the tag catalog (call `list_tags` first, use `create_tag` for new ones)
- `deliveryMode` (optional): `"conversation"`, `"presentation"`, or `"worksheet"`

**update_resource:**
- `id` (required): The resource ID to update
- `name` (optional): New name
- `content` (optional): New markdown content
- `tags` (optional): New tags (replaces existing — must come from the catalog)
- `deliveryMode` (optional): `"conversation"`, `"presentation"`, or `"worksheet"`
- `coverImage` (optional): Image ID to use as the resource cover (shown to students when the lesson starts). The image must already be attached to the resource (use `create_visual`, `generate_image`, `upload_image_from_url`, `prepare_image_upload` + curl, or `create_image` first, then pass the returned ID here). Pass `null` to remove the cover.

**generate_image** (preferred for custom illustrations):
- `resourceId` (required): Resource to attach the image to
- `prompt` (required): Text prompt describing the educational image to generate (max 4000 chars)
- `description` (optional): What the image shows (important for bot)
- `question` (optional): Question to ask about the image
- `answer` (optional): Expected answer
- `hint` (optional): Help for students
- `botVisible` (optional): If true, bot can see the image

**create_text_image** — **DEPRECATED. Do not use.** To render a word, phrase, label or key
term, use the `text` visual instead: `create_visual(resourceId, "text", { text: "...", … })`. It
renders the same inline formatting (`_underline_`, `*italic*`, `**bold**`, `[highlight]`,
`{red:colored}`), is vector instead of a raster PNG, is teacher-editable, and supports the org logo.

**upload_image_from_url** (preferred for remote use):
- `resourceId` (required): Resource to attach image to
- `url` (required): Public URL of the image to download
- `name` (required): Image filename
- `description` (optional): What the image shows (important for bot)
- `question` (optional): Question to ask about the image
- `answer` (optional): Expected answer
- `hint` (optional): Help for students
- `botVisible` (optional): If true, bot can see the image

**create_image:**
- `resourceId` (required): Resource to attach image to
- `name` (required): Image filename
- `mimeType` (required): `"image/png"`, `"image/jpeg"`, `"image/svg+xml"`, or `"application/pdf"`. For SVG, prefer the dedicated `create_svg` tool (it takes raw markup, no base64).
- `data` (required): Base64-encoded image data
- `description` (optional): What the image shows (important for bot)
- `question` (optional): Question to ask about the image
- `answer` (optional): Expected answer
- `hint` (optional): Help for students
- `botVisible` (optional): If true, bot can see the image

**create_visual** (authored visual tool — renders as crisp SVG, no AI):
- `resourceId` (required): Resource to attach the visual to
- `kind` (required): One of `"number_line"`, `"fraction"`, `"grid"`, `"timeline"`, `"math"`, `"text"`, `"counters"`, `"clock"`, `"ten_frame"`, `"bar_chart"`, `"base_ten"`, `"money"`, `"phoneme_frame"`, `"place_value"`, `"chart"`, `"function_graph"`, `"geometry"`, `"coordinate_grid"`, `"box_plot"`, `"probability_tree"`, `"atom"`, `"ph_scale"`, `"syllable_split"`, `"onset_rime"`, `"writing_area"`
- `config` (required): Kind-specific JSON object (shapes below). Add `"logo": true` to render the org logo above the visual.
- `question`, `answer`, `hint` (optional): let the bot quiz the student on the visual, exactly like an image. **Do not set a description** — the bot is given one regenerated from the `config` every lesson, so it always matches what's drawn; and there is no `botVisible` (a vector/text visual is always legible).
- Returns an image ID — **embed it in the content with `::visual{#id}`** (mirrors `::quiz{#id}`). A visual ID can also be passed as a `create_quiz` `image` or a `update_resource` `coverImage`.

Per-`kind` `config` shapes:
```jsonc
// number_line — marks/highlight may be numbers or strings
{ "from": 0, "to": 10, "marks": [0, 2, 4, 6, 8, 10], "highlight": 6 }
// fraction — style: "bar" | "circle"
{ "num": 3, "den": 4, "style": "bar" }
// grid — rows of cells; optional header per row; optional highlighted cells
{ "cols": ["1", "2", "3"], "rows": [{ "header": "x2", "cells": [2, 4, 6] }], "highlight": [{ "row": 0, "col": 1 }] }
// timeline
{ "events": [{ "date": "1969", "label": "Moon landing" }, { "date": "1989", "label": "Web invented" }] }
// math — KaTeX / LaTeX
{ "tex": "\\frac{1}{2} + \\frac{1}{3}" }
// text — font: "beginner" | "comic" | "standard"; inline formatting _u_ *i* **both** [hl] {red:color}; \n for line breaks
{ "text": "cat", "color": "#333333", "font": "beginner" }
// counters — a single emoji, count 1-99
{ "emoji": "🍎", "count": 5 }
// clock — 12-hour face; hours 0-23, minutes 0-59; showMinuteTicks optional (default true)
{ "hours": 3, "minutes": 15, "showMinuteTicks": true }
// ten_frame — 0-20 counters in a 2x5 frame (two frames above 10); color optional
{ "count": 7, "color": "#E53935" }
// bar_chart — simple guided bars; unit + color optional
{ "bars": [{ "label": "Cats", "value": 4 }, { "label": "Dogs", "value": 6 }], "unit": "children" }
// base_ten — Dienes blocks for place value, 0-999
{ "value": 124 }
// money — coins/notes in MINOR units (pence/cents); currency GBP|USD|EUR (default GBP)
{ "coins": [200, 200, 20, 20, 5], "currency": "GBP" }
// phoneme_frame — one grapheme per sound (phonics sound buttons)
{ "graphemes": ["sh", "i", "p"] }
// place_value — digits in place columns; columns optional (derived if omitted)
{ "value": 3406, "columns": ["Th", "H", "T", "O"] }
// chart — a full Chart.js spec (the most flexible kind). type one of
//   bar|line|pie|doughnut|radar|polarArea|scatter|bubble. Pure data only: no URLs, max 50KB.
{ "spec": { "type": "bar", "data": { "labels": ["Jan", "Feb"], "datasets": [{ "label": "Rain", "data": [42, 35] }] }, "options": {} } }
// function_graph — plot y=f(x); explicit * (2*x); funcs sin cos tan sqrt abs exp ln log; consts pi e
{ "functions": ["x^2", "2*x+1"], "xMin": -5, "xMax": 5 }
// geometry — labelled polygon; coords auto-scale; rightAngles = vertex indices
{ "vertices": [{ "x": 0, "y": 0, "label": "A" }, { "x": 4, "y": 0, "label": "B" }, { "x": 0, "y": 3, "label": "C" }], "sideLabels": ["4 cm", "5 cm", "3 cm"], "rightAngles": [0] }
// coordinate_grid — plot points/segments; xMin/xMax/yMin/yMax optional
{ "points": [{ "x": 2, "y": 3, "label": "A" }], "segments": [{ "from": [0, 0], "to": [2, 3] }] }
// box_plot — five-number summary
{ "min": 2, "q1": 5, "median": 7, "q3": 10, "max": 14, "label": "Scores" }
// probability_tree — recursive branches with probabilities
{ "branches": [{ "label": "Red", "prob": "1/2", "branches": [{ "label": "Red", "prob": "1/2" }, { "label": "Blue", "prob": "1/2" }] }] }
// atom — Bohr model; electrons per shell
{ "protons": 11, "neutrons": 12, "electrons": [2, 8, 1] }
// ph_scale — 0-14
{ "value": 7 }
// syllable_split — phonics: one chunk per syllable
{ "syllables": ["but", "ter", "fly"] }
// onset_rime — phonics: onset may be empty (e.g. "at")
{ "onset": "str", "rime": "ing" }
// writing_area — WORKSHEET blank answer space (size: "sentence" | "paragraph" | "multi_paragraph"; label optional). Print-first.
{ "size": "paragraph", "label": "Your answer" }
```

**update_visual:**
- `resourceId` (required), `imageId` (required)
- `config` (optional): replaces the whole config; must match the visual's existing `kind` (kind is immutable — delete and recreate to change it)
- `name`, `question`, `answer`, `hint` (optional). No `description`/`botVisible` — see create_visual.

**create_quiz:**
- `resourceId` (required): Resource to attach quiz to
- `question` (required): Question text
- `questionType` (required): `"single"`, `"multiple"`, `"freetext"`, `"ordered_list"`, `"matching"`, or `"fill_in_blank"`
- `answers` (for single/multiple/ordered_list): Array of `{id, text, isCorrect}`. For matching: `{id, text, isCorrect, matchText}`
- `expectedAnswer` (for freetext): Correct answer
- `inputRestriction` (for freetext): `"text"`, `"integer"`, `"decimal"`, or `"fraction"`
- `numericMin` (optional): Minimum allowed value for numeric inputs (integer, decimal, fraction)
- `numericMax` (optional): Maximum allowed value for numeric inputs (integer, decimal, fraction)
- `allowNegative` (optional): Whether negative values are accepted (default: true)
- `evaluationCriteria` (optional): AI grading guidelines
- `retryLimit` (optional): Max attempts
- `hint` (optional): Guidance for wrong answers
- `description` (optional): When the bot should present this quiz
- `image` (optional): Illustration image ID — see **Quiz Illustrations** below. The image must already be attached to the same resource.

**update_quiz:**
- `resourceId` (required): The resource the quiz belongs to
- `id` (required): The quiz ID to update
- All other fields from `create_quiz` are optional — omitted fields are left unchanged
- Use this to fix typos, change answers, or attach an illustration image after the image has been generated
- Pass `image: null` to remove an existing illustration image

**create_course:**
- `name` (required): Course name
- `description` (optional): Course description
- `tags` (optional): Array of tags
- `progressionType` (optional): `"flexible"`, `"sequential"`, or `"random"`
- `openEnrollment` (optional): If true, students can self-enroll from the course catalog
- `defaultTeacher` (optional): User ID of the teacher assigned to self-enrolled students (required when openEnrollment is true)
- `maxEnrollments` (optional): Maximum number of students who can enroll (null = unlimited)
- `priceCents` (optional): Price in cents for paid courses (requires Stripe Connect)
- `currency` (optional): Currency code for paid courses (e.g., `"GBP"`, `"USD"`, `"EUR"`)
- `teacherOnly` (optional): If true, course is only visible to teachers in the catalog (preview mode)

**export_resource:**
- `resourceId` (required): The resource ID to export
- Returns: Base64-encoded ZIP containing manifest, content, media, and quizzes

**import_resource:**
- `zipData` (required): Base64-encoded ZIP file data matching the format below
- `fileName` (optional): Filename for the ZIP (default: `"import.zip"`)
- Returns: The newly created resource (with ID and name)

### Resource Archive Format (for import/export)

The ZIP file **must** have all files at the root level (no subdirectory wrapper) with this structure:

```
manifest.json        # Required: metadata and file references
content.md           # Required: markdown content with portable tokens
media/               # Media files (images, videos, PDFs)
  media-001.png
  media-002.jpeg
quizzes/             # Quiz definitions
  quiz-001.json
  quiz-002.json
```

**CRITICAL**: Files must be at the ZIP root — NOT inside a subdirectory. A ZIP containing `my-resource/manifest.json` will fail; it must be just `manifest.json`.

#### manifest.json format

```json
{
  "version": 1,
  "exportedAt": "2026-01-01T00:00:00.000Z",
  "resource": {
    "name": "Subtraction on a Number Line",
    "tags": ["maths", "subtraction"],
    "deliveryMode": "conversation"
  },
  "media": [
    {
      "ref": "media-001",
      "filename": "media/media-001.png",
      "name": "Number Line Diagram",
      "mimeType": "image/png",
      "description": "A number line showing 15 - 3 = 12",
      "question": "Where do we land after 3 hops back from 15?",
      "answer": "We land on 12",
      "hint": "Count the arrows backwards",
      "order": null,
      "botVisible": true
    }
  ],
  "quizzes": [
    {
      "ref": "quiz-001",
      "filename": "quizzes/quiz-001.json"
    }
  ]
}
```

Key rules:
- `version` must be `1`
- `media` and `quizzes` are **arrays** (not objects)
- Each media entry must have a `ref` field (e.g. `"media-001"`) matching its portable token in content.md
- Each quiz entry must have a `ref` field (e.g. `"quiz-001"`) matching its portable token in content.md
- Media filenames must match their `mimeType` extension (e.g. `media-001.png` for `image/png`)
- Only `image/png`, `image/jpeg`, and `application/pdf` are supported for images

#### content.md portable tokens

Content must use portable `media://` and `quiz` tokens (NOT database IDs):

```markdown
![media-001](media://media-001)

::quiz{#quiz-001}
```

The importer replaces these tokens with real database IDs after creating the records.

#### Quiz JSON format

Each quiz file (e.g. `quizzes/quiz-001.json`) contains:

```json
{
  "question": "What is 15 - 3?",
  "questionType": "freetext",
  "inputRestriction": "integer",
  "expectedAnswer": "12",
  "numericMin": 0,
  "numericMax": 20,
  "allowNegative": false,
  "evaluationCriteria": "Accept only the number 12.",
  "retryLimit": 3,
  "hint": "Start at 15 and count back 3 hops.",
  "description": "Quiz after the number line example."
}
```

**create_tag:**
- `name` (required): Tag name to create (max 128 chars, lowercase recommended)

**list_course_resources:**
- `courseId` (required): The course ID to list resources for
- Returns: Ordered list of resources with name, tags, order, and bot details (content stripped to save tokens)

**add_resource_to_course:**
- `courseId` (required): Course ID
- `resourceId` (required): Resource ID to add
- `order` (optional): Position in the course (important for sequential courses)
- `bot` (optional): Override the course's default bot for this specific resource
- `deliveryMode` (optional): Override the resource's default delivery mode within this course

## Organizing Resources into Courses

Courses group related resources together for students to complete. When creating a course, consider:

### Progression Types

| Type | Behavior | Best For |
|------|----------|----------|
| **Flexible** | Students complete resources in any order | Independent topics, reference materials |
| **Sequential** | Students must follow the specified order | Prerequisite content, building-block skills |
| **Random** | System selects the next resource | Practice drills, varied review |

### Course Design Tips

- **Use sequential progression** when earlier resources teach concepts needed for later ones
- **Use flexible progression** when resources cover independent topics within a theme
- **Set resource order** carefully for sequential courses - use the `order` parameter in `add_resource_to_course`
- **Override the bot** for specific resources if a different AI persona or expertise is needed
- **Override delivery mode** per resource if some topics work better as conversation, presentation, or worksheet
- **Keep courses focused** - 3-8 resources per course is a good range
- **Use consistent tags** across resources and courses for easy organization

### Open Enrollment

Enable open enrollment to let students self-enroll from the course catalog:
- Requires a `defaultTeacher` to be assigned for managing self-enrolled students
- Optionally set `maxEnrollments` to cap capacity
- Set `teacherOnly: true` to preview the course before releasing to students

### Paid Courses

If the organization has Stripe Connect configured:
- Set `priceCents` and `currency` to create a paid course
- Open enrollment must be enabled for paid courses
- Students go through Stripe checkout and are automatically enrolled after payment

## CRITICAL: Embedding Media and Quizzes in Resource Content

After creating images and quizzes, you **MUST** update the resource content to include references to them. Without these references, the bot will NOT display the media or quizzes during delivery.

### Image Reference Syntax

```markdown
![IMAGE_ID](/api/v1/images/IMAGE_ID/data)
```

Example: If `create_image` returns `{ id: 156 }`, insert:
```markdown
![156](/api/v1/images/156/data)
```

### Quiz Reference Syntax

```markdown
::quiz{#QUIZ_ID}
```

Example: If `create_quiz` returns `{ id: 28 }`, insert:
```markdown
::quiz{#28}
```

### Visual Reference Syntax

```markdown
::visual{#VISUAL_ID}
```

Example: If `create_visual` returns `{ id: 91 }`, insert:
```markdown
::visual{#91}
```

The bot displays the visual via `display_media` when it reaches this point in the prose — exactly like images and quizzes.

### Complete Example

```
# Step 1: Create resource
create_resource(
  name: "Introduction to Photosynthesis",
  content: "# Learning Objectives\n\nPlaceholder content...",
  tags: ["science", "biology"],
  deliveryMode: "conversation"
)
# Response: { id: 42, ... }

# Step 2: Upload image from URL
upload_image_from_url(
  resourceId: 42,
  url: "https://example.com/plant-cell.png",
  name: "Plant Cell Diagram",
  description: "Diagram of a plant cell showing chloroplasts",
  question: "Where does photosynthesis occur?",
  answer: "In the chloroplasts",
  hint: "Look for the green organelles",
  botVisible: true
)
# Response: { id: 156, ... }

# Step 3: Create quiz
create_quiz(
  resourceId: 42,
  description: "Test understanding of photosynthesis location",
  question: "In which organelle does photosynthesis take place?",
  questionType: "single",
  answers: [
    { id: "a", text: "Mitochondria", isCorrect: false },
    { id: "b", text: "Chloroplast", isCorrect: true },
    { id: "c", text: "Nucleus", isCorrect: false }
  ],
  retryLimit: 2,
  hint: "It contains chlorophyll, which is green"
)
# Response: { id: 28, ... }

# Step 4: CRITICAL - Update resource with image and quiz references
update_resource(
  id: 42,
  content: "# Learning Objectives\n\nBy the end of this resource, the student should understand photosynthesis.\n\n## What is Photosynthesis?\n\nExplain that plants convert sunlight into energy. Show the diagram:\n\n![156](/api/v1/images/156/data)\n\nDiscuss the diagram with the student.\n\n## Check Understanding\n\nNow test the student's knowledge:\n\n::quiz{#28}\n\n## Summary\n\nReview the key points."
)
```

## Quiz Types

### Single Choice
```json
{
  "question": "What gas do plants absorb?",
  "questionType": "single",
  "answers": [
    { "id": "a", "text": "Oxygen", "isCorrect": false },
    { "id": "b", "text": "Carbon dioxide", "isCorrect": true }
  ],
  "hint": "Think about what humans breathe out"
}
```

### Multiple Choice
```json
{
  "question": "Select ALL ingredients for photosynthesis",
  "questionType": "multiple",
  "answers": [
    { "id": "a", "text": "Sunlight", "isCorrect": true },
    { "id": "b", "text": "Water", "isCorrect": true },
    { "id": "c", "text": "Oxygen", "isCorrect": false }
  ]
}
```

### Open Answer
```json
{
  "question": "Calculate 15% of 80",
  "questionType": "freetext",
  "inputRestriction": "decimal",
  "expectedAnswer": "12",
  "evaluationCriteria": "Accept 12, 12.0, or 12.00"
}
```

### Fraction
```json
{
  "question": "What is 1/2 + 1/6?",
  "questionType": "freetext",
  "inputRestriction": "fraction",
  "expectedAnswer": "2/3",
  "numericMin": 0,
  "numericMax": 1,
  "allowNegative": false,
  "evaluationCriteria": "Accept equivalent fractions (e.g. 4/6, 8/12)"
}
```

### Ordered List
Students drag and drop items into the correct sequence. The answers array order defines the correct sequence — students see items shuffled.
```json
{
  "question": "Put these in order from smallest to largest",
  "questionType": "ordered_list",
  "answers": [
    { "id": "a", "text": "1/2", "isCorrect": false },
    { "id": "b", "text": "0.55", "isCorrect": false },
    { "id": "c", "text": "3/5", "isCorrect": false },
    { "id": "d", "text": "62%", "isCorrect": false }
  ],
  "hint": "Convert everything to decimals to compare"
}
```
Note: `isCorrect` is ignored for ordered_list — the array order IS the correct answer. Minimum 2 items, maximum 10.

### Matching
Students match items from two columns. The left column shows fixed terms, the right column shows shuffled matches that students drag to align correctly.
```json
{
  "question": "Match each part of speech to its example",
  "questionType": "matching",
  "answers": [
    { "id": "a", "text": "verb", "isCorrect": false, "matchText": "jump" },
    { "id": "b", "text": "noun", "isCorrect": false, "matchText": "book" },
    { "id": "c", "text": "adverb", "isCorrect": false, "matchText": "quickly" }
  ],
  "hint": "Think about what each word does in a sentence"
}
```
Note: `isCorrect` is ignored for matching — correct state is when each answer's `matchText` is aligned next to its `text`. Each answer needs both `text` (term) and `matchText` (match). Minimum 2 pairs, maximum 10.

### Fill in the Blank
Students fill in missing words within a sentence. Supports free text input or word bank (dropdown) mode.
```json
{
  "question": "Water freezes at [] degrees Celsius and boils at [] degrees Celsius",
  "questionType": "fill_in_blank",
  "answers": [
    { "id": "a", "text": "0", "isCorrect": true },
    { "id": "b", "text": "100", "isCorrect": true }
  ],
  "hint": "Think about the states of water"
}
```
For word bank mode (dropdown with distractors), set `inputRestriction` to `"word_bank"` and add distractor answers with `isCorrect: false`:
```json
{
  "question": "The [] sat on the []",
  "questionType": "fill_in_blank",
  "inputRestriction": "word_bank",
  "answers": [
    { "id": "a", "text": "cat", "isCorrect": true },
    { "id": "b", "text": "mat", "isCorrect": true },
    { "id": "c", "text": "dog", "isCorrect": false },
    { "id": "d", "text": "hat", "isCorrect": false }
  ]
}
```
Note: Use `[]` in the question to mark blank positions. Correct answers (`isCorrect: true`) map in order to blanks. Distractors (`isCorrect: false`) appear as extra options in word bank mode. Minimum 1 blank.

## Quiz Illustrations

A quiz can have an **illustration image** attached to it (separate from the resource's inline images and cover image). The illustration appears **alongside the question, on the same screen**, when the quiz is shown to the student.

**This is the preferred way to give a quiz a supporting figure.** If a question refers to a diagram — "find the missing side of *this* triangle", "label the parts of *this* cell", "which angle is *x*?" — attach that figure to the quiz via its `image` field. Because the picture and the question render together and stay together, the student can read the diagram while they answer. **Do not** instead drop the figure inline in the content just before `::quiz{#id}`: the bot shows an inline image earlier, as it narrates that part of the lesson, so by the time the student is answering the quiz the picture may no longer be on screen. A quiz that depends on a figure should *own* that figure.

A quiz illustration can be any image kind — an uploaded/AI image, a `create_svg` drawing, or a `create_visual` visual tool (pass the visual's `id` as the quiz `image`). For maths/science diagrams, prefer a `create_visual` (e.g. `geometry`, `coordinate_grid`, `function_graph`) or a `create_svg` so it stays crisp.

Quiz images are **not** embedded in the resource markdown — they are linked directly to the quiz record via the `image` field. The resource content does NOT need a `![ID](/api/v1/images/ID/data)` reference for quiz illustrations.

### Attaching an illustration

The image must already be attached to the same resource as the quiz. The normal flow is:

```
# Step 1: Generate or upload the illustration, attached to the resource
generate_image(resourceId: 42, prompt: "A cross-section of a leaf showing chloroplasts")
# → { id: 177, ... }

# Step 2a: Create the quiz with the image attached
create_quiz(
  resourceId: 42,
  question: "Which labelled part contains the chloroplasts?",
  questionType: "freetext",
  expectedAnswer: "The palisade mesophyll",
  image: 177
)

# OR Step 2b: Create the quiz first, then attach the image later
create_quiz(resourceId: 42, question: "...", questionType: "single", answers: [...])
# → { id: 99, ... }
update_quiz(resourceId: 42, id: 99, image: 177)
```

### When to use a quiz illustration vs. an inline image

- **Quiz illustration (`image` on the quiz)** — when the student needs to *see the figure while answering*. The image renders next to the question and stays with it, so the diagram and the question are on screen together. Use this for diagram-based maths/science questions ("find the missing side of this triangle"), "identify the part", "which one is correct", or "describe what you see". **When in doubt, if a quiz mentions a specific figure, attach it here.**
- **Inline image (`![ID](/api/v1/images/ID/data)` in the resource content)** — when the visual is part of the lesson *narrative* (a worked example the bot talks through), not tied to one specific question the student must answer against it. The bot displays it at the point you place it in the markdown, then moves on.
- **Cover image (`coverImage` on the resource)** — a single hero image shown when the lesson starts.

Worked example — a Pythagoras quiz with its own triangle:
```
# 1. Draw the triangle as a visual on the resource
create_visual(resourceId: 42, kind: "geometry", config: { vertices: [...], sideLabels: ["6 cm","?","8 cm"], rightAngles: [0] })
# → { id: 201 }
# 2. Attach it to the quiz — NOT inline before ::quiz
create_quiz(resourceId: 42, question: "Find the hypotenuse of the triangle shown.", questionType: "freetext", expectedAnswer: "10", image: 201)
```
The student sees the triangle and the question side by side. Had the triangle instead been placed inline before `::quiz{#id}`, the bot would have shown it earlier and the student would answer with no picture in view.

To remove an illustration, call `update_quiz(resourceId, id, image: null)`.

## Visual Tools

**Visual tools** are crisp, lightweight graphics you author directly into a resource — number lines, fractions, grids, timelines, math equations, styled word cards, and counters. Create them with `create_visual` (they're stored as image rows with no binary data, just a small JSON config) and embed them with `::visual{#id}`. The bot shows the visual via `display_media` when it reaches that point in the prose. There is nothing to enable per bot — **any bot can display them**.

Prefer a visual tool over an AI or word image whenever the content is structured data the platform can draw:

| Kind | What it draws | Reach for it when |
|---|---|---|
| `number_line` | Labelled axis with optional highlight | Position, ordering, decimals, distance |
| `fraction` | Bar or circle split into parts | Introducing or comparing fractions |
| `grid` | Rows/cols table with optional highlighted cells | Multiplication grids, periodic tables, conjugation |
| `timeline` | Horizontal time axis with events | History, story arcs, multi-step processes |
| `math` | KaTeX equation | Formal expressions, sums, fractions in formula form |
| `text` | Styled word/phrase (`**bold**` `*italic*` `_underline_` `[highlight]` `{color:text}`, `\n` line breaks) | Vocabulary, key terms, one-word answers, labels |
| `counters` | A scatter of one repeated emoji (count 1-99) | Counting, "how many?", early number sense |
| `clock` | Analog clock face set to hours:minutes | Telling the time; reading hours and minutes |
| `ten_frame` | A 2×5 frame filled with counters | Number bonds, "make 10", counting to 20 |
| `bar_chart` | Simple guided bar chart | Data handling, comparing amounts |
| `base_ten` | Hundreds/tens/ones Dienes blocks | Place value, partitioning |
| `money` | Coins/notes (minor units) to count | Counting money, change |
| `phoneme_frame` | Sound buttons per grapheme | Phonics — segmenting words into sounds |
| `place_value` | Digits in labelled place columns | Place value, reading large numbers |
| `chart` | Any Chart.js chart from a JSON spec | Richer data viz (line/pie/scatter…) — the flexible power tool |
| `function_graph` | Plot y = f(x) on Cartesian axes | Graphing functions (KS3–A-level) |
| `geometry` | Labelled polygon (angles/sides/right angles) | Pythagoras, trig, angle rules |
| `coordinate_grid` | Points & lines on x/y axes | Coordinates, plotting |
| `box_plot` | Box-and-whisker plot | GCSE statistics |
| `probability_tree` | Branching tree with probabilities | GCSE probability |
| `atom` | Bohr model: nucleus + electron shells | Atomic structure |
| `ph_scale` | Coloured 0–14 scale with a marker | Acids and alkalis |
| `syllable_split` | Word split into syllables with arcs | Phonics — syllable counting |
| `onset_rime` | Word split into onset + rime | Phonics — rhyming, word families |
| `writing_area` | Blank ruled lines for a written answer (size: sentence/paragraph/multi_paragraph) | Worksheets — space for the student to write; print-first |

Workflow: `create_visual(resourceId, kind, config)` → get an `id` → put `::visual{#id}` where it belongs in the content → `update_resource`. See **create_visual** under *Tool Parameters* for the per-`kind` `config` shapes and the `"logo": true` option. A visual id can also serve as a quiz illustration (`create_quiz` `image`) or a resource `coverImage`.

### Picking a bot's persona

Call `list_bots` (or `get_bot(id)`) to read each bot's persona before authoring, so the prose matches the tutor's voice. `BotResponse` returns:

```
{ id, name, voice, avatarUrl,
  personality,                 // # Personality and Tone (e.g. "warm, patient")
  languagePolicy,              // # Language (e.g. "British English; light Yorkshire")
  referencePronunciations,     // [{ word, say }, ...] phonetic respellings
  content,                     // # Additional Instructions (catch-all)
  ... }
```

Each of `personality / languagePolicy / referencePronunciations / content` maps to a labelled section of the bot's realtime system prompt. Use them to:

- **Match tone**: a bot with `personality: "playful, silly, makes maths jokes"` should not get formal academic prose.
- **Respect language/accent**: if `languagePolicy` says "British English", spell colour with a "u", say "maths" not "math".
- **Know specialist words the bot mispronounces**: if `referencePronunciations` lists `{word: "Pythagoras", say: "pie-THAG-oh-russ"}`, you can rely on the bot to say it correctly even though the markdown spells it "Pythagoras".

### What this does NOT change

- **Quizzes still capture input.** Visual tools are display-only — keep using quizzes to check understanding.
- **No per-bot setup.** Unlike the retired live "display" tools, authored visuals work on any bot with no flags to enable.

## Image Creation Speed

**NEVER generate images via Python/code execution** (matplotlib, PIL, etc.) — this is extremely slow, produces poor results, and sends huge base64 payloads through MCP.

Instead, use these tools in order of preference:

1. **`create_visual`** (MCP) — the first choice for almost everything drawable: the `text` visual for words/phrases/vocabulary/labels, the `math` visual for equations, and the many other kinds for diagrams (number line, fraction, geometry, chart, …). Instant, vector, teacher-editable. (`create_text_image` is **deprecated — do not use**; the `text` visual replaces it.)
2. **`generate_image`** (MCP) — only for photoreal illustrations / scenes a vector can't express. Uses the org's OpenAI API key for server-side AI generation; slower, so reach for it last.
3. **`upload_image_from_url`** (MCP) — for publicly hosted images from Wikimedia, educational sites, etc.
4. **`prepare_image_upload`** (MCP) + `curl` (HTTP, see next section) — for files you already have on disk. Much faster than `create_image` because no base64 round-trip through the LLM. Works in any MCP client (Claude Code CLI, Claude Cowork).
5. **`create_image` (base64)** (MCP) — last resort only, for tiny inline blobs (≤ 1 KB). Avoid for larger files; the LLM has to type out the base64 string which can take many seconds per kilobyte.

### Uploading a local file via `prepare_image_upload` (preferred over `create_image`)

If you have a local file path (e.g. an image you just produced with another tool), upload it through the two-step `prepare_image_upload` flow. This avoids the LLM having to encode the bytes as base64, and works without needing the MCP bearer token in your shell environment (the upload URL itself is the credential).

```
# Step 1 (MCP): mint a single-use upload URL for the target resource.
prepare_image_upload(resourceId: 123)
  → { uploadUrl: "https://api.beau.bot/api/v1/uploads/images/<token>",
      expiresAt: "..." }

# Step 2 (Bash): POST the file to that URL. NO Authorization header — the
# token is in the URL path. Single-use, expires in 5 minutes.
curl -F "file=@/absolute/path/to/image.png" \
     -F "name=optional-display-name.png" \
     -F "description=Roots of y = x² − 4x − 5" \
     -F "question=What are the roots of this quadratic?" \
     -F "answer=x = -1 and x = 5" \
     -F "hint=Look where the curve crosses the x-axis" \
     -F "botVisible=true" \
     "<uploadUrl from step 1>"
```

Form fields:

- `file` (required) — the binary file. Accepted MIME types: `image/png`, `image/jpeg`, `image/webp`, `image/gif`, `application/pdf`. Max size: 5 MB for images, 20 MB for PDFs.
- `name` (optional) — display name; defaults to the uploaded filename.
- `description`, `question`, `answer`, `hint` (optional) — same semantics as `create_image`.
- `botVisible` (optional, `"true"`/`"false"`) — same as `create_image`.

Returns the same response shape as `create_image` (id, mimeType, description, etc.), minus the binary `data` field. Use the returned `id` exactly as you would from `create_image` — embed it in the resource content as `![ID](/api/v1/images/ID/data)`.

**Notes**:
- Tokens are single-use and expire 5 minutes after issuance. If `curl` returns 401, mint a fresh URL via `prepare_image_upload` and retry.
- The legacy `POST /api/v1/resources/:id/images/multipart` endpoint (with `Authorization: Bearer`) still works for local Claude Code sessions, but `prepare_image_upload` is preferred because it works identically in hosted clients (Claude Cowork) where the bearer token isn't exposed to the shell.

## Image Metadata Accuracy

**Use `get_image(imageId)` to inspect every image before setting metadata.** AI-generated images may not match your prompt exactly.

- Write `description`, `question`, `answer`, and `hint` based on what the image ACTUALLY shows, not what you intended
- If the image doesn't match your intent, regenerate it or adjust the metadata to match reality

**Bad example** (metadata written from intent, not inspection):
```
generate_image(prompt: "A number line showing 5 + 3 = 8")
# Sets metadata WITHOUT viewing the result:
description: "Number line showing 5 + 3 = 8"  # ← May not match actual image
```

**Good example** (metadata written after inspection):
```
generate_image(prompt: "A number line showing 5 + 3 = 8")
# Views the returned image, sees it actually shows a number line 0-10 with an arrow from 5 to 8
description: "A number line from 0 to 10 with an arrow jumping from 5 to 8"
question: "What number does the arrow land on?"
answer: "8"
```

## Tag Governance

**Always call `list_tags` before assigning tags to resources.** This prevents tag sprawl and keeps the organization's tag catalog clean.

### Rules
- Reuse existing tags — don't create near-duplicates ("maths" vs "math", "Year 1" vs "year-1")
- Ask the user before creating new tags via `create_tag`
- Naming conventions: lowercase, hyphens for multi-word (e.g. "year-3", "key-stage-2", "number-bonds")
- Prefer broader tags over hyper-specific ones ("fractions" not "adding-unit-fractions-year-4")

### Example flow
```
# Step 1: Check existing tags
list_tags(pageSize: 50)
# → Items: [{ name: "maths" }, { name: "year-3" }, { name: "fractions" }]

# Step 2: User wants a resource tagged "maths", "year-3", "number-line"
# "maths" and "year-3" exist. "number-line" is new.
# → Ask user: "The tag 'number-line' doesn't exist yet. Shall I create it?"

# Step 3: User confirms
create_tag(name: "number-line")

# Step 4: Create resource with all tags from catalog
create_resource(name: "...", content: "...", tags: ["maths", "year-3", "number-line"])
```

## Best Practices

1. **Write for the bot, not the student** - The bot interprets your instructions and delivers them conversationally
2. **Use clear headings** - Structure content with `#`, `##`, `###` for pacing
3. **Keep resources short** - 5-10 minutes is ideal per resource; split longer content into multiple resources within a course
4. **Include plenty of quizzes** - Aim for **at least 2-3 quizzes per resource**, placed after each major concept. Quizzes are the primary way to keep students actively engaged. A resource with fewer than 2 quizzes almost always feels too passive. Use a variety of quiz types (single choice, multiple choice, open answer, fraction, ordered list, matching) to keep things interesting.
5. **Include images in every resource** - Aim for **at least 1-2 images per resource**. Visuals break up text, illustrate concepts, and give the bot concrete material to discuss. Text-only resources feel dry and lecture-like.
6. **Provide accurate media metadata** - View every image before setting description, question, answer, and hint. Write metadata for what the image actually shows, not what you intended.
7. **Enable botVisible for important images** - Let the bot see diagrams and charts
8. **Always embed references** - Images need `![ID](/api/v1/images/ID/data)`, quizzes need `::quiz{#ID}`, and visuals need `::visual{#ID}` in the content
   - **Exception: a figure a quiz tests against belongs on the quiz**, via its `image` field — not inline before `::quiz{#ID}`. That keeps the diagram and the question on screen together. See *Quiz Illustrations*.
9. **Design resources to be self-contained** - Each resource should make sense on its own and be reusable across courses
10. **Avoid text-heavy content** - If any section of your resource is more than a few sentences without a quiz or image, it's probably too text-heavy. Add interactivity.
11. **Test your resources** - Use the Test Resource feature in the admin UI before assigning to students
12. **Always call `list_tags` before assigning tags** - Reuse existing tags and only create new ones (via `create_tag`) with user confirmation.
13. **Verify image content before writing metadata** - AI-generated images may not match your prompt exactly. View the image first.
14. **Never run Python or other code to generate images** - Use the `text` visual (`create_visual`) for words/phrases, other `create_visual` kinds for diagrams, `generate_image` for photoreal illustrations, `upload_image_from_url` for public images. (`create_text_image` is deprecated — do not use.)

## Additional Resources

- [resource-creation-guide.md](resource-creation-guide.md) - Detailed resource structuring guidance
- [teacher-guide.md](teacher-guide.md) - Teacher workflow and course management
