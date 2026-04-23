# Beau Plugin for Claude Code and Cowork

A plugin for creating educational resources and evaluating student performance on the [Beau](https://beau.bot) voice tutoring platform. Gives Claude the knowledge and tools to author resources, upload images, create quizzes, organize courses, and analyse student progress.

## What This Plugin Provides

- **MCP Server Connection**: Connects to the Beau API via OAuth 2.1 for resource and student management
- **Create Resource Skill** (`/beaubot:create-resource`): Guides Claude through authoring structured educational resources with images, quizzes, and courses
- **Evaluate Student Skill** (`/beaubot:evaluate-student`): Analyses student performance across courses using transcripts, scores, and progress data

## Installation

### Claude Code (CLI)

```
/install-plugin dselman/tutorbot-plugin
```

### Claude Cowork (Desktop App)

1. Open **Customize** panel
2. Click **+** next to Personal plugins
3. Select **Add from marketplace**
4. Paste: `dselman/tutorbot-plugin`

### Claude Web (MCP Connector only)

Claude web doesn't support plugins, but you can add the MCP server as a remote Connector to get access to all tools (without skills):

**MCP Server URL**: `https://api.beau.bot/mcp`

Add this as a remote MCP connector. You'll be prompted to authenticate via OAuth with your Beau account. Web users get all 25 tools but not the guided skill workflows.

## Usage

Once installed, invoke the skills in any chat:

```
/beaubot:create-resource    — Author a new educational resource
/beaubot:evaluate-student   — Analyse student performance
```

**Note**: The MCP server must be connected before using tools. If you get tool errors, check your MCP connection and re-authenticate if needed.

## MCP Tools (25)

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
| `generate_image` | Generate an AI image from a text prompt |
| `create_text_image` | Render formatted text into a PNG image |

### Quizzes

| Tool | Description |
|------|-------------|
| `create_quiz` | Create a quiz (single/multiple/freetext/ordered_list/matching/fill_in_blank) |

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

## Authentication

The MCP server uses OAuth 2.1 with Auth0. When you first use a tool, you'll be prompted to authenticate with your Beau account. Your organization's data is scoped to your Auth0 organization.

## License

MIT
