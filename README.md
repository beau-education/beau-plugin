# Beau Plugin for Claude Code and Cowork

A plugin for creating educational resources and evaluating student performance on the [Beau](https://beau.bot) voice tutoring platform. Gives Claude the knowledge and tools to author resources, upload images, create quizzes, organize courses, and analyse student progress.

This repo is the **single source of truth** for Beau's skills **and** user guides. The same skills run in two places, so the experiences stay in lockstep:

- **Claude Code / Cowork** — installs this plugin and runs the skills directly.
- **The Beau dashboard's built-in AI assistant** — an **OpenAI-backed** agent (not Claude) that loads `SKILL.md` from this repo at runtime. The dashboard also fetches the human user guides (below) from this repo at runtime instead of bundling them.

## What This Plugin Provides

- **MCP Server Connection**: Connects to the Beau API via OAuth 2.1 for resource and student management
- **Create Resource Skill** (`/beaubot:create-resource`): Guides Claude through authoring structured educational resources with images, quizzes, and courses
- **Evaluate Student Skill** (`/beaubot:evaluate-student`): Analyses student performance across courses using transcripts, scores, and progress data

## Installation

### Claude Code (CLI)

```
/install-plugin beau-education/beau-plugin
```

### Claude Cowork (Desktop App)

1. Open **Customize** panel
2. Click **+** next to Personal plugins
3. Select **Add from marketplace**
4. Paste: `beau-education/beau-plugin`

### Claude Web (MCP Connector only)

Claude web doesn't support plugins, but you can add the MCP server as a remote Connector to get access to all tools (without skills):

**MCP Server URL**: `https://api.beau.bot/mcp`

Add this as a remote MCP connector. You'll be prompted to authenticate via OAuth with your Beau account. Web users get all 32 tools but not the guided skill workflows.

## Usage

Once installed, invoke the skills in any chat:

```
/beaubot:create-resource    — Author a new educational resource
/beaubot:evaluate-student   — Analyse student performance
```

**Note**: The MCP server must be connected before using tools. If you get tool errors, check your MCP connection and re-authenticate if needed.

## MCP Tools (32)

### Resource Management

| Tool | Description |
|------|-------------|
| `list_resources` | List resources with optional tag filtering |
| `get_resource` | Fetch a resource by ID |
| `create_resource` | Create a new resource |
| `update_resource` | Update an existing resource |
| `export_resource` | Export a resource as a ZIP archive |
| `import_resource` | Import a resource from a ZIP archive |

### Media

| Tool | Description |
|------|-------------|
| `get_image` | Download an image by ID (returns image visually + metadata) |
| `create_image` | Upload an image (base64) to a resource |
| `upload_image_from_url` | Upload an image from a public URL |
| `prepare_image_upload` | Mint a single-use multipart upload URL for a local file (preferred over `create_image` for files on disk) |
| `generate_image` | Generate an AI image from a text prompt |
| `create_svg` | Add a hand-authored SVG diagram to a resource |
| `create_text_image` | _(Deprecated)_ Render text into a PNG — prefer `create_visual` kind `text` |

### Visuals

Teacher-authored diagrams rendered as crisp SVG/HTML (stored as a media type, shown via `display_media`).

| Tool | Description |
|------|-------------|
| `create_visual` | Create a visual (number line, fraction, grid, chart, geometry, clock, … 24+ kinds) |
| `update_visual` | Update an existing visual's config (kind is immutable) |

### Quizzes

| Tool | Description |
|------|-------------|
| `create_quiz` | Create a quiz (single/multiple/freetext/ordered_list/matching/fill_in_blank) |
| `update_quiz` | Update an existing quiz |

### Tags

| Tool | Description |
|------|-------------|
| `list_tags` | List available tags in the organization |
| `create_tag` | Create a new tag |

### Courses

| Tool | Description |
|------|-------------|
| `create_course` | Create a course |
| `list_courses` | List courses |
| `add_resource_to_course` | Add a resource to a course |
| `list_course_resources` | List resources in a course with order and bot details |

### Bots

| Tool | Description |
|------|-------------|
| `list_bots` | List the org's AI tutor bots (persona, voice, avatar) |
| `get_bot` | Get a bot's details |

### Users & Students

| Tool | Description |
|------|-------------|
| `list_users` | List users in the organization |
| `get_user` | Get user details |
| `list_enrollments` | List student enrollments |
| `get_enrollment_progress` | Get detailed progress for an enrollment |
| `get_student_stats` | Get aggregated statistics for a student |
| `get_student_activity` | Get recent activity log for a student |
| `get_transcript` | Get the conversation transcript for a lesson |

## User guides (source of truth)

The Beau dashboard's in-app help and role dashboards fetch these guides from this repo at runtime (via the API), so there are no duplicated copies in the app — **edit them here**.

| Guide | Path | `audience` |
|-------|------|------------|
| Admin | `guides/admin-guide.md` | `[admin]` (human) |
| Student | `guides/student-guide.md` | `[student]` (human) |
| Teacher | `beaubot/skills/create-resource/teacher-guide.md` | `[teacher, admin]` (human) |
| Resource (authoring) | `beaubot/skills/create-resource/resource-creation-guide.md` | `[teacher, admin, agent]` (human + AI) |

Each guide carries `audience` / `title` frontmatter (stripped before display). `audience` lists the dashboard roles a guide is for, plus `agent` for guides the AI authoring assistant should treat as reference. Human-UI guides (`guides/`) live outside `skills/` so skill discovery ignores them.

**Keep in sync:** the guides, the skill instructions (`SKILL.md`), and the API's MCP tool descriptions all describe the same capabilities — when a tool gains a capability (e.g. a new visual kind or quiz type), update all of them together.

## Authentication

The MCP server uses OAuth 2.1 with Auth0. When you first use a tool, you'll be prompted to authenticate with your Beau account. Your organization's data is scoped to your Auth0 organization.

## License

MIT
