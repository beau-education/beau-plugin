---
audience: [admin]
title: Admin Guide
note: >-
  Human UI documentation for the Beau admin dashboard, for administrators.
  NOT authoring instructions for the agent.
---

# Admin Guide

This guide covers administrative functions including organization settings, user management, bot configuration, and tag management. Admins also have access to all teacher capabilities.

## Organization Settings

Navigate to **Admin > Settings** to configure your organization. Settings are organized into these tabs:

| Tab | Contents |
|-----|----------|
| **General** | Organization details, logo, contact info, business address, privacy policy |
| **Students** | Student self-registration, parental consent form configuration |
| **OpenAI** | The three OpenAI API keys (primary, share links, AI assistant) |
| **Stripe** | Stripe connection status and platform fee (read-only) |
| **Sharing** | Share-link kill switch and revoke-all panic button |

If the primary OpenAI API key is not configured, a warning banner appears across all tabs linking to the OpenAI tab.

### General Tab

#### Organization Details

**Organization Name** is displayed throughout the platform and cannot be changed directly. Contact support if you need to update it.

**URL Slug** determines your organization's custom dashboard URL. For example, if your slug is `bright-minds`, your dashboard URL will be:

```
https://beau.bot/bright-minds/dashboard
```

**Slug requirements:**
- 3-64 characters
- Lowercase letters, numbers, and hyphens only
- Must start and end with a letter or number
- Cannot contain consecutive hyphens
- Certain slugs are reserved (auth, api, admin, etc.)

The system validates slug availability in real-time as you type. If you change the slug, you'll be redirected to the new URL after saving.

#### Organization Logo

Upload your organization's logo to brand the platform. The logo appears:
- At the top of student and teacher dashboards
- In the course catalog alongside your courses
- On the Auth0 login and signup pages for your organization

When you upload or change the logo, it is automatically synced to Auth0 so your login page displays your organization's branding. This sync requires the API to be served over HTTPS (production environments).

**Logo requirements:**
- Square image (1:1 aspect ratio)
- Minimum: 200×200 pixels
- Maximum: 512×512 pixels
- Only PNG and JPEG formats are supported (PNG recommended for transparency)
- Maximum file size: 500KB

To upload a logo:
1. Click or drag an image into the upload area
2. The image is validated against the requirements
3. If valid, the logo uploads and displays immediately

#### Contact Information

| Field | Description |
|-------|-------------|
| **Website URL** | Your organization's public website. Appears in course catalog listings and on the consent form. |
| **Admin Email** | Primary administrative contact. Shown on the consent form so parents can reach you. |
| **Phone Number** | Select a country code from the dropdown and enter the number in national format. Shown on the consent form. |

The country code dropdown automatically suggests a code when you select a country in the address section.

**Tip**: Contact information (website, email, phone) is displayed in a "Contact Us" section at the bottom of the parental consent form when consent is enabled. Make sure these details are filled in so parents know how to reach you.

#### Business Address

Enter your organization's physical address. All fields are optional but help establish trust with potential students:

| Field | Description |
|-------|-------------|
| **Address Line 1** | Street address, P.O. box, company name |
| **Address Line 2** | Apartment, suite, unit, building, floor (optional) |
| **City** | City or locality |
| **State/Province/Region** | State, province, county, or region |
| **Postal Code** | ZIP or postal code |
| **Country** | Select from the dropdown (ISO country codes) |

#### Privacy Policy

If your organization has a privacy policy for AI-assisted learning, enter the full URL. When configured:
- A "Privacy Policy" link appears in the student Help panel
- A shield icon appears on course cards linking to the policy
- A required "I have read and agree to the privacy policy" checkbox is automatically added to the consent form (if consent is enabled)

This is optional but recommended for transparency about how AI and student data are used.

### Students Tab

#### Student Registration

Toggle **Allow student self-registration** to let students create their own accounts using your organization's sign-up link. The sign-up link is displayed on this page and can be shared with students.

#### Parental Consent

For organizations that require explicit consent before students access the platform (e.g., parental consent for minors), enable the built-in consent form.

##### How Consent Works

1. Toggle **Require parental consent for students** to on
2. Configure the consent form (introduction text, custom questions)
3. When a new student logs in for the first time, they see the consent form as part of their onboarding flow
4. A parent or guardian completes the form on the student's device
5. On submission, consent is **automatically verified** and the student can proceed to complete their profile and access the dashboard

The consent form is built into the platform — no external forms or manual verification needed.

##### Configuring the Consent Form

When consent is enabled, a two-column layout appears with configuration on the left and a live preview on the right:

**Introduction Text** (optional): Custom text displayed at the top of the consent form, above the checkboxes. Use this to explain what the platform does and why consent is needed.

**Built-in fields** (always shown when consent is enabled):
- Parent/Guardian Full Name (required)
- "I consent to my child using voice tutoring with Beau" checkbox (required)
- "I have read and agree to the privacy policy" checkbox (shown only when a Privacy Policy URL is set on the General tab — required when shown)

**Custom Questions**: Add up to 10 custom questions to gather additional information from parents. Each question has:

| Setting | Description |
|---------|-------------|
| **Question text** | The label shown to parents |
| **Type** | Text (single line), Textarea (multi-line), or Checkbox |
| **Required** | Whether the parent must answer to submit the form |

##### Consent Form Preview

The right side of the Students tab shows a live preview that updates as you configure the form. This shows exactly what parents will see, including:
- Introduction text
- Parent name field
- Consent checkboxes
- Privacy policy checkbox (if configured)
- Custom questions
- Contact Us section (populated from your contact info on the General tab)

##### What Parents See

When a student first logs in and consent is required, they are guided through an onboarding flow:

1. **Consent step**: The parent/guardian fills in their name, checks the required consent boxes, answers any custom questions, and submits
2. **Profile step**: Basic information about the child (name, date of birth, optional details like school, hobbies, learning goals)

The consent form displays your organization's logo and name, and includes a "Contact Us" section at the bottom showing your website, email, and phone number (if configured on the General tab).

##### Consent Statuses

Students have one of these consent statuses:

| Status | Description |
|--------|-------------|
| **N/A** | Consent not required (consent not enabled, or non-student role) |
| **Pending** | Consent required but not yet submitted |
| **Submitted** | Parent has submitted the consent form (auto-verified) |
| **Verified** | Consent verified — student has full access |

**Note**: Consent is automatically verified on submission. The Submitted and Verified statuses are functionally equivalent for access purposes. Admins can manually verify or revoke consent from the student list if needed.

##### Viewing Consent Responses

After a student's consent has been submitted, you can review the response:

1. Go to **Teaching > Students**
2. Find the student with **Verified** consent status
3. Click the action menu (three dots)
4. Select **View Consent**

The dialog shows the complete consent response including parent name, all checkbox answers, custom question responses, and the submission timestamp.

##### Managing Consent After Submission

From the student list action menu:

- **Verify Consent**: Manually verify consent for students showing Pending or N/A status (useful for paper-based consent received outside the platform)
- **View Consent**: View the submitted consent response details
- **Resend Welcome Email**: Re-send the welcome email to a verified student
- **Revoke Consent**: Block a student's access by resetting their consent to Pending. The student will need to re-submit the consent form to regain access.

### OpenAI Tab

Beau uses OpenAI for three separate workloads. You can use **one key for everything**, or set a **dedicated key per workload** so you can monitor usage and apply separate spend limits in the OpenAI dashboard. The tab shows a status table — what each key powers and whether it's configured — with a pencil **edit** action per row that opens a dialog to set, replace, or clear the key. Keys are masked after saving and never shown again.

| Key | What it powers | Required? |
|-----|----------------|-----------|
| **Primary** | Student lessons (voice), freetext grading, image generation | Yes |
| **Share links** | Public / anonymous shared lessons | Optional — falls back to the primary key |
| **AI assistant** | Teachers authoring resources with the in-dashboard AI assistant | Optional — falls back to the primary key |

Setting the optional keys lets you meter and cap share-link and content-authoring spend independently of student lesson traffic. Clearing an optional key (via its edit dialog) makes that workload fall back to the primary key.

**Important**: Without a primary OpenAI API key, resource testing and student lessons will not work.

### Sharing Tab

Public lesson URLs let anyone — including people without an account on your platform — take a specific lesson by clicking a link. Teachers create the links from the resource editor; this tab is where you manage them at the organization level. (The dedicated share-link OpenAI key lives on the **OpenAI** tab.)

**Allow share links**: master kill switch (saves immediately). When off, every public share URL returns 404 immediately, regardless of how recently it was created. Use this if you suspect a link has leaked or to disable the feature outright.

**Revoke all share links** (panic button): soft-deletes every active share link across the organization in one click. Confirmation required. After revoking, teachers can create new links any time — only the existing ones become invalid.

**What share-link traffic does not record**:
- No enrollment, no transcript, no quiz scores
- No student profile, no user row
- The visitor's interaction is in-memory only and disappears when they close the tab

### Stripe Tab

Displays your Stripe Connect account status and platform fee percentage (read-only). Manage your Stripe connection from the **Admin > Payments** page.

For detailed Stripe setup instructions, see the **Payments and Stripe Integration** section below.

## User Management

Navigate to **Admin > Users** to manage all users in your organization.

### User List

The user list displays:
- Name and email
- Role (admin, teacher, or student)
- Tags assigned to the user
- Invitation status
- Consent status (if consent forms are configured)

Use search and filtering to find specific users.

### Managing Student Consent

If parental consent is enabled (see **Settings > Students** tab), consent is automatically verified when the parent submits the built-in form. No manual verification is needed for the normal flow.

However, you can manage consent manually from the student list:

- **Verify Consent**: Grant access to students showing "N/A" or "Pending" — useful when you've received consent through other means (e.g., paper forms)
- **View Consent**: Review the submitted consent response (parent name, checkbox answers, custom questions)
- **Resend Welcome Email**: Re-send the sign-in link to a verified student
- **Revoke Consent**: Block access by resetting consent to Pending. The student must re-submit the form to regain access.

### Creating Users

1. Click **New User** or **Invite User**
2. Enter the user's email address
3. Select their role (admin, teacher, or student)
4. The system sends an invitation email

### Invitation Status

Users display one of these statuses:
- **Pending**: Invitation sent, awaiting acceptance
- **Accepted**: User has activated their account
- **Legacy**: Pre-existing user from before the invitation system

You can resend invitations to users with Legacy status.

### Editing Users

Click on any user to:
- Update their name or email
- Modify their teacher profile description
- Modify assigned tags
- Upload a profile photo

### Managing User Roles

When editing an existing user, admins can manage their roles using the **Roles** multi-select field. The available roles are:

| Role | Access |
|------|--------|
| **Student** | View enrolled courses, complete resources, track own progress |
| **Teacher** | Create resources and courses, manage enrollments, invite students, monitor student progress |
| **Admin** | All teacher capabilities plus user management, bot management, and tag management |

**How role changes work:**

- Select one or more roles from the dropdown (Admin, Teacher, Student)
- Click **Save Changes** to apply
- For users who have accepted their invitation, role changes are synced to Auth0 immediately
- For pending or legacy users, roles are stored locally and synced when they accept their invitation
- Non-manageable roles (e.g., superadmin) are preserved and cannot be changed through this interface

**Safety guards:**

- **Only admins** can modify user roles (server-enforced)
- **Last admin protection**: You cannot remove the admin role from the only admin in the organization
- **Self-role change**: If you change your own roles, the page will reload automatically to apply the new permissions. You may need to log in again if your access level changed significantly.

**Note**: Only admins can invite teachers. Teachers can invite students but cannot create other teacher accounts.

### Why Profile Information Matters

When the AI bot delivers a lesson, it receives context that includes:
- The bot's system prompt
- The teacher's profile description
- The student's profile description
- The resource content

This allows the bot to personalize delivery based on teaching style and student background. As an admin:
- Ensure teacher profiles include relevant expertise and teaching approach
- Add relevant details to student profiles (students cannot edit their own profiles)
- Create bot descriptions that complement your teachers' styles

**Example student profile:**
```
Lily is 11 years old. She loves dinosaurs, swimming and
drawing. She has a pet hamster named Biscuit.
```

This simple profile helps the bot personalize examples and make connections to the student's interests during lessons.

**Example teacher profile:**
```
Mrs Johnson is a professional and kind teacher. She has a
degree in education and is an expert primary school teacher.
She lives in Boston, Massachusetts.

If the pupil disengages or interacts in an unsafe or confused
manner please say, "If you do not engage with the lesson I
will contact Mrs Johnson."
```

A good teacher profile includes qualifications, teaching style, and can even include instructions for how the bot should handle specific situations.

## Bot Management

Bots are AI tutors that provide interactive guidance during lessons. Only admins can create and manage bots. Navigate to **Admin > Bots**.

### Creating a Bot

1. Click **New Bot**
2. Enter a **Name** that describes the bot's purpose
3. Write the **System Prompt** that defines the bot's behavior and teaching style
4. Select a **Voice** for the bot's speech
5. Click **Save**
6. After saving, you can upload an optional **Bot Image** that will be displayed to students during lessons

### Bot Voice

The voice setting controls how the bot sounds when speaking to students during lessons. These are OpenAI Realtime API voices, each with a distinct character:

- **alloy** - Neutral and balanced
- **ash** - Clear and precise
- **ballad** - Melodic and smooth
- **cedar** - Friendly and natural (Recommended)
- **coral** - Warm and friendly
- **echo** - Resonant and deep
- **marin** - Confident and engaging (Recommended)
- **sage** - Calm and thoughtful
- **shimmer** - Bright and energetic
- **verse** - Versatile and expressive

OpenAI recommends **marin** or **cedar** for best quality. Choose a voice that matches the bot's personality and your target audience. For example, a bot designed for young children might work well with a warm voice like "coral", while a more formal academic bot might suit "sage".

### 3D Avatar

Bots can have an optional 3D avatar that appears during lessons with real-time lip-sync to the bot's speech. This creates a more engaging and lifelike experience for students.

#### Uploading a 3D Avatar

1. Create or edit a bot
2. In the **3D Avatar** section, click **Upload Avatar**
3. Select a GLB file (up to 20MB)
4. The avatar will be uploaded and displayed in the bot editor

#### Creating 3D Avatars with Avaturn

3D avatars can be created using [Avaturn](https://avaturn.me), a web-based avatar creation tool. When creating and exporting your avatar from Avaturn:

1. Go to **Body type settings** in Avaturn
2. Set the avatar type to **Type 2**
3. Enable **Face blendshape support** (required for lip-sync)
4. When downloading, select **Avatar with animation**
5. Export the avatar in **GLB format**

The face blendshapes allow the player to animate the avatar's mouth in sync with the bot's speech, creating realistic lip movements.

#### Avatar vs Image

If a bot has both a 2D image and a 3D avatar:
- The player will use the **3D avatar** (the avatar takes priority)
- The 3D avatar will **lip-sync** to the bot's audio output
- The 2D image serves as a fallback if the avatar fails to load

If a bot only has a 2D image, that image will be displayed during lessons without lip-sync animation.

### Persona — what you control vs what we control

The realtime system prompt is built per lesson from two sources:

- **Server-controlled**: the role ("you are a voice tutor delivering this lesson"), the available tools, turn-taking rules, unclear-audio handling, verbosity discipline, and the safety/escalation rules. You don't see these in the dashboard — they're identical across every bot on the platform and we tune them centrally as OpenAI's prompting guidance evolves.
- **Admin-controlled (Persona card)**: how *this* tutor sounds, speaks, and pronounces specialist terms. Four free-form fields, all optional. Leave any of them blank and the server falls back to sensible defaults.

| Field | Maps to | What to put here |
|---|---|---|
| **Personality and tone** | `# Personality and Tone` section of the prompt | How the bot feels: "warm and patient", "playful and silly", "calm and precise". Avoid lesson-specific content here — that belongs on the resource. |
| **Language and accent** | `# Language` section | Language and accent rules: "Speak British English with a light Yorkshire accent. Keep the accent stable." Defaults to a neutral English accent if blank. |
| **Reference pronunciations** | `# Reference Pronunciations` section | Subject-specific words students hear wrong. One per line, `word -> pronunciation`. Use a phonetic respelling on the right that reads aloud naturally. Example: `algebra -> AL-juh-bruh`. |
| **Additional instructions** | `# Additional Instructions` section | Catch-all for anything specific to this tutor that doesn't fit above: signature closing line, special "do not say…" rules, niche behaviour. Optional. |

The bot also receives the teacher's profile, the student's profile, the current date/time, and the resource's lesson plan, all assembled server-side.

**Example Persona fields for a primary-school maths bot named "Spark":**

- **Personality and tone**: `Warm, friendly, and genuinely enthusiastic about maths. Age-appropriate for primary: clear language, concrete examples, playful tone without being childish. Encouraging and patient — celebrate effort, not just correct answers.`
- **Language and accent**: `Always respond in the same language the student is using, if clear. Default to English if unclear.`
- **Reference pronunciations**: `SATs -> sats`
- **Additional instructions**: `Begin every lesson by introducing yourself by name. When a quiz result comes back, briefly acknowledge before moving on. Use "Nice try" ONLY for incorrect quiz answers — never for correct ones. End every lesson with a short summary plus one or two things to work on next time. Close with exactly this line: "Thank you for learning with me today. The lesson is finished."`

You don't need to include identity ("you are a voice tutor for Bright Minds Learning" etc.) — the server already names the bot and frames it as a tutor. Just describe the persona.

**Migrating existing bots**: bots created before this split still work — their old `content` continues to feed the Additional Instructions section. You can paste pieces of it into the structured fields any time; nothing breaks if you leave it all in Additional Instructions.

### Visual Tools

Bots can show **visual tools** — number lines, fractions, grids, timelines, math equations, styled word cards, and counters — but these are now **authored into resources by teachers**, not configured per bot. A teacher inserts a visual into a lesson (it becomes a `::visual{#id}` block) and any bot displays it at the right point in the lesson. There are no per-bot toggles to manage: every bot can show every kind of visual.

There is nothing for an admin to enable here. If you want guidance on the twenty-five visual kinds and when to use them, see the **Working with Visual Tools** section of the Resource Creation Guide (Teaching).

### Bot Usage

- Bots are selected when creating courses (as a default)
- Bots are also selected when assigning individual resources to students
- The same bot can be used across multiple courses and enrollments
- Teachers cannot create bots; they select from available bots you've created

### Editing Bots

Click on any bot to modify its configuration. Changes affect all future interactions but don't alter past lesson history.

### Previewing Bots

You can preview a bot to test its personality, voice, and 3D avatar before using it in lessons:

1. Navigate to **Admin > Bots**
2. Click the menu icon (three dots) on any bot row
3. Select **Preview**
4. The player opens in a new tab with a test conversation

In preview mode, you can have a conversation with the bot to verify:
- The voice sounds appropriate for your audience
- The system prompt produces the desired behavior
- The 3D avatar (if configured) displays and lip-syncs correctly

Preview sessions are not saved and do not affect any student data.

## Tag Management

Tags help organize resources, courses, and users across the platform. Navigate to **Admin > Tags**.

### Creating Tags

1. Click **New Tag**
2. Enter the tag name
3. Click **Save**

### Tag Usage

Tags can be applied to:
- **Resources**: For categorizing learning content
- **Courses**: For organizing course offerings
- **Users**: For grouping teachers or students

Teachers and admins can apply tags when creating or editing items. Tags enable filtering in list views across the application.

### Tag Best Practices

- Use consistent naming conventions
- Create tags for subjects, difficulty levels, or content types
- Avoid creating too many overlapping tags
- Review and consolidate tags periodically

### Deleting Tags

Deleting a tag removes it from all items it was applied to. Use caution when removing commonly-used tags.

## Payments and Stripe Integration

Organizations can sell courses directly to students using Stripe Connect. Revenue is split 50/50 between your organization and the platform.

### Setting Up Stripe Connect

Before you can accept payments for courses, you must connect your organization to Stripe:

1. Navigate to **Admin > Payments**
2. Click **Connect with Stripe**
3. Complete the Stripe onboarding process:
   - Provide business information
   - Add bank account details for payouts
   - Verify your identity (required by Stripe for compliance)
4. Once verified, your account status will show as **Active**

The embedded onboarding flow is provided by Stripe and handles all KYC (Know Your Customer) requirements securely.

#### Account Status

Your Stripe account can have one of these statuses:

| Status | Description |
|--------|-------------|
| **Not Connected** | No Stripe account linked. Click "Connect with Stripe" to begin. |
| **Pending** | Onboarding started but not complete. Click to continue setup. |
| **Pending Verification** | Details submitted, awaiting Stripe verification. |
| **Active** | Fully verified and ready to accept payments. |

### Creating Paid Courses

Once Stripe is connected, you can create paid courses:

1. Create or edit a course
2. Enable **Open Enrollment** (required for paid courses)
3. Toggle **Paid Course** to on
4. Select the **Currency** (GBP, USD, EUR, and 7 other currencies supported)
5. Enter the **Price** as a decimal amount (e.g., 29.99)
6. Save the course

The system supports 10 currencies: GBP (default), USD, EUR, CAD, AUD, JPY, CHF, INR, MXN, and BRL. Prices are displayed with the appropriate currency symbol (£, $, €, ¥, etc.).

**Important**: Paid courses require open enrollment to be enabled. Students purchase access through the Course Catalog.

### How Students Purchase Courses

When a student browses the Course Catalog:

1. Free courses show an **Enroll Now** button
2. Paid courses display the price and a **Purchase Course** button
3. Clicking **Purchase Course** redirects to a secure Stripe Checkout page
4. After successful payment, the student is automatically enrolled
5. The course appears in their **My Courses** list

Students can pay using credit/debit cards. Stripe handles all payment processing securely.

### Revenue Split

Each course sale is automatically split:

| Recipient | Share |
|-----------|-------|
| Your Organization | 50% |
| Platform | 50% |

For example, a £29.99 course generates:
- £14.99 to your organization
- £15.00 to the platform

The split applies regardless of which currency you use. Stripe processing fees are absorbed by the platform.

### Viewing Earnings

Navigate to **Admin > Payments** to view your earnings dashboard:

#### Balance Overview

- **Available Balance**: Funds ready for payout to your bank account
- **Pending Balance**: Recent payments still being processed (typically 2-7 days)
- **Account Status**: Your Stripe account verification status

#### Transaction List

View all payment transactions including:
- Date and time
- Course name
- Student name and email
- Total amount paid
- Your organization's share
- Payment status

Use pagination to browse through historical transactions.

### Processing Refunds

Admins can refund payments when necessary:

1. Go to **Admin > Payments**
2. Find the transaction in the list
3. Click the **Refund** button
4. Confirm the refund in the dialog

When you process a refund:
- The full amount is returned to the student
- Your organization's share is reversed
- The platform's share is also reversed
- The student's enrollment remains active (but can be manually deactivated)

**Note**: Stripe's original processing fees are not refunded. The platform absorbs this cost.

### Free Courses

Courses without a price remain free and work as before:
- Students click **Enroll Now** in the Course Catalog
- No payment is required
- Enrollment is immediate

You can mix free and paid courses in your catalog.

### Troubleshooting Payments

#### "Connect with Stripe" Not Working

- Ensure you have admin permissions
- Check your browser's popup blocker
- If Stripe shows a security warning, log into your Stripe Dashboard to confirm the account creation

#### Student Can't Purchase a Course

- Verify your Stripe account status is **Active**
- Confirm the course has a price set and open enrollment enabled
- Check that the student isn't already enrolled

#### Payment Completed but No Enrollment

- Payments are processed via webhooks which may have a short delay
- Ask the student to refresh their dashboard
- Check the Payments list for the transaction status
- Contact support if the issue persists

#### Refund Failed

- Refunds can only be processed for completed payments
- Partial refunds are not currently supported
- Check your Stripe Dashboard for detailed error information

## Teacher Capabilities

As an admin, you have full access to all teacher functions:

### Resources
- Create and edit learning resources
- Apply tags for organization
- Assign resources directly to students

### Courses
- Create courses with resources
- Set progression types (Flexible, Sequential, Random)
- Manage student enrollments
- Monitor completion and scores

### Students
- Invite new students
- View student progress and activity
- Assign resources and courses

See the **Teacher Guide** for detailed documentation on these features.

## Common Administrative Tasks

### Setting Up Your Organization

1. Go to **Admin > Settings > General**
2. Upload your organization logo (square, 200-512px, max 500KB) — this also sets your Auth0 login page branding
3. Enter your website URL, admin email, and phone number
4. Add your business address
5. Configure your URL slug if desired
6. Optionally add a Privacy Policy URL
7. Click **Save Changes**

### Onboarding a New Teacher

1. Go to **Admin > Users**
2. Click **Invite User**
3. Enter their email and select **Teacher** role
4. Once they accept, they can create resources and manage students

### Setting Up a New Bot

1. Go to **Admin > Bots**
2. Click **New Bot**
3. Write a system prompt appropriate for the subject matter
4. Inform teachers the new bot is available

### Organizing Content with Tags

1. Go to **Admin > Tags**
2. Create tags for your organization's subjects or categories
3. Teachers can then apply these tags when creating resources and courses

### Setting Up Paid Courses

1. Go to **Admin > Payments** and complete Stripe Connect setup
2. Create a course with **Open Enrollment** enabled
3. Toggle **Paid Course**, select a currency, and enter the price
4. Students can purchase from the Course Catalog

### Viewing Earnings

1. Go to **Admin > Payments**
2. View available and pending balances
3. Browse transaction history
4. Process refunds if needed

### Setting Up Parental Consent

1. Go to **Admin > Settings > Students**
2. Toggle **Require parental consent for students** to on
3. Optionally add an introduction text explaining your AI tutoring programme
4. Add any custom questions (text, textarea, or checkbox — mark as required if needed)
5. Check the live preview on the right to see what parents will see
6. Click **Save Changes**
7. Ensure your contact info is filled in on the **General** tab (shown on the consent form)
8. If you have a privacy policy URL set on the **General** tab, a privacy policy checkbox is automatically added

### Configuring the OpenAI API Key

1. Go to **Admin > Settings > OpenAI**
2. Click the **edit** (pencil) action on the **Primary** key row
3. Paste your OpenAI API key (starts with `sk-proj-...`) and click **Save**
4. The warning banner disappears once the key is configured

## Troubleshooting

### User Can't Log In
- Verify their account is in the system
- Check if their invitation is still pending
- Resend the invitation if needed

### Student Stuck on Consent Screen
- The student (or their parent) needs to complete all required fields and submit the built-in consent form
- If consent was enabled after the student registered, their status may show "N/A" — use **Verify Consent** from the student list to manually grant access
- Check that all required fields are filled in (parent name, consent checkbox, privacy policy if configured, required custom questions)

### Student Didn't Receive Welcome Email
- Check spam/junk folders
- Use the **Resend Welcome Email** action in the student list
- Verify the student's email address is correct

### Student Not Seeing a Course
- Confirm the student is enrolled in the course
- Check the enrollment status (should be Active)
- Verify the student is looking at the correct dashboard

### Bot Not Working as Expected
- Review the system prompt for clarity
- Test the bot in a sample enrollment
- Adjust the prompt and test again

---

## AI-Assisted Content Creation

Teachers can use the **Beau plugin for Claude** to dramatically speed up resource creation and student evaluation. Instead of manually building resources in the dashboard, teachers can describe what they want in natural language and Claude handles the rest — drafting content, generating images, creating quizzes, and assembling courses.

### Key Benefits

- **Faster resource creation** — a complete resource with images and quizzes in minutes instead of hours
- **Consistent quality** — Claude follows best practices for structure, quiz variety, and image placement
- **Student evaluation** — generate detailed performance reports from progress data and transcripts
- **Available in Claude Code, Cowork, and Claude web** — teachers can use whichever tool they prefer

### Setup

Install the Beau plugin in Claude Code or Cowork:
```
/install-plugin beau-education/beau-plugin
```

For Claude web, add the MCP server as a Connector: `https://api.beau.bot/mcp`

See the **Teacher Guide** for detailed usage instructions and example workflows.
