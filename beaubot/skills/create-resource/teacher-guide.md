---
audience: [teacher, admin]
title: Teacher Guide
note: >-
  Human UI documentation for the Beau dashboard, for teachers (and admins).
  Useful background for the agent, but NOT authoring rules — see
  resource-creation-guide.md for those.
---

# Teacher Guide

This guide covers how to create resources, build courses, manage student enrollments, and monitor progress.

## Your Dashboard

Your teacher dashboard provides quick access to common tasks and recent activity.

### Quick Actions

The dashboard includes shortcuts to:
- **Create Resource**: Start building new learning content
- **Create Course**: Set up a new course structure
- **Invite Student**: Add a new student to the platform

### Recent Activity

Your dashboard displays:
- **Recently Updated Resources**: Your latest resource changes with tags and update times
- **Recently Updated Courses**: Your latest course changes with progression types
- **Recent Student Activity**: Student completions showing scores and timestamps

## Managing Resources

Resources are individual learning units that students complete. Navigate to **Teaching > Resources** to manage them.

### Creating a Resource

1. Click **New Resource**
2. Enter a **Name** for the resource
3. Select a **Delivery Mode**:
   - **Conversation**: Two-way voice interaction (requires student microphone)
   - **Presentation**: One-way narration (student clicks to advance)
   - **Worksheet**: No bot — self-paced screens of info + quizzes the student reads and completes (results recorded); also printable as a take-home
4. Add **Content** using the rich text editor
5. Apply **Tags** to organize the resource
6. Click **Save**

### Choosing a Delivery Mode

**Conversation Mode** is best for:
- Interactive tutoring with Q&A
- Personalized learning experiences
- Building on student responses
- Lessons that benefit from discussion

**Presentation Mode** is best for:
- Lecture-style content delivery
- Students who can't use a microphone
- Information-heavy content
- Consistent delivery across all students

**Worksheet Mode** is best for:
- Independent practice and homework
- Printable handouts / take-home sheets
- Assessments where you want a written record
- Any time a voice session isn't wanted — the content is written directly to the student, who reads the screens and completes the quizzes

**Narrated worksheets.** A worksheet can also be read aloud, like a slideshow with a voice. Click **Narration** beside the delivery mode, turn on **Read this worksheet aloud**, pick a voice (and, if you like, how it should speak, e.g. "warm British English accent, calm pace"), then press **Generate narration**. Beau reads each page as the student sees it: the text, and each question with its choices (it never reads out answers). Students can pause, replay, let it turn the pages automatically, or switch the sound off. After you edit the worksheet or a question, open Narration again and press **Update narration** — only the pages you changed are recorded again, and a changed page stays silent until you do. Each page fits the student's screen, with the picture shrinking to the space the words leave, so keep pages short (add a heading to split a long one). Narration suits young or early readers, students who benefit from hearing the text, and guided walkthroughs.

### Resource List Features

The resource list provides:
- **Search**: Find resources by name
- **Tag Filter**: Filter by one or more tags
- **Sorting**: Sort by name, update date, or other fields
- **Export**: Download the list as CSV or Excel

### Resource Actions

For each resource, you can:
- **Edit**: Click the resource name to modify it
- **Download ZIP**: Export the resource as a self-contained ZIP file (includes all media and quizzes)
- **Test Resource**: Preview the resource with an AI tutor before assigning
- **Assign to Student**: Set this resource as one student's homework (with a due date), or enroll them in a class course that has it
- **Delete**: Remove the resource (use with caution)

### Exporting and Importing Resources

You can export resources as self-contained ZIP files and import them back to create new resources. This is useful for sharing resources between organizations, backing up content, or migrating resources between environments.

#### Exporting a Resource

There are two ways to export:

**From the Resource List:**
1. Click the actions menu (three dots) on a resource
2. Select **Download ZIP**

**From the Resource Editor:**
1. Open the resource for editing
2. Click **Download ZIP** in the right panel

The exported ZIP contains everything needed to recreate the resource:
- Resource metadata (name, tags, delivery mode)
- Markdown content
- All media files (images, videos, PDFs)
- All quiz definitions

#### Importing a Resource

1. Go to **Teaching > Resources**
2. Click **Import Resource** (next to New Resource)
3. Select a `.zip` file exported from Beau
4. The system creates a new resource with all media and quizzes
5. You're redirected to the new resource's edit page

**Notes:**
- Each import creates a new, separate resource (no deduplication)
- Videos are re-uploaded to your organization's storage
- The imported resource is fully independent — editing it won't affect the original
- ZIP files can be imported into a different organization than they were exported from

### Testing a Resource

Before assigning a resource to students, you can test it to verify the AI tutor delivers the content correctly:

1. Open the resource editor
2. Click **Test Resource** in the toolbar
3. Select which **Bot** to use for testing (if your organization has multiple bots)
4. Optionally select a **Student** profile to simulate (defaults to yourself)
5. Click **Start Test** to open the player

During testing, you'll experience the lesson exactly as a student would - including voice interaction with the AI tutor. Test sessions are automatically marked as "tests" and won't appear in the student's activity log by default. They run in your own **Test Sandbox** course, which Beau manages for you and which is not shown on the Courses page.

**What to check when testing:**
- Does the bot introduce the topic clearly?
- Are images displayed at the right moments?
- Are quizzes presented at appropriate points in the lesson?
- Does the bot ask appropriate questions?
- Is the difficulty level appropriate?
- Does the lesson flow naturally?

### The Outline View

While editing, click the **outline** icon at the left edge of the editor to open a navigable outline built from your headings (H1/H2/H3). It updates as you type, and clicking any entry scrolls straight to that heading — handy for longer lessons. Click the hide icon to collapse it.

### Version History

Beau keeps a history of a resource's content so you can look back and recover earlier work. Open it with the **clock icon** in the editor toolbar. There are two kinds of version:

- **🕐 Auto-saves** — recorded automatically as you edit (about one every 5 minutes), tagged with your name and the time. The most recent **50** are kept; older auto-saves are pruned automatically.
- **📷 Snapshots** — explicit, **named** checkpoints you create yourself with **"Save snapshot"** (e.g. "Before big revision"). Snapshots capture the **whole resource** — content **plus its quizzes and media** — and are **never auto-pruned**.

In the dialog you can:

- **Preview** any version on the right — it renders like the lesson (headings, images, and visuals), with quizzes shown as placeholders.
- **Restore an auto-save** — replaces the current **content** with that version's. This is content-only: your quizzes and media are left as they are now. It's reversible — the current state is saved as a new version first, so you can restore back.
- **Download a snapshot** as a `.zip` — the full portable bundle (the same format as Export). To fully roll back to a snapshot, download it and re-import it (see *Exporting and Importing*).
- **Delete** any version you don't want to keep.

**Good to know:** auto-saves track your *text* edits — they don't snapshot quizzes or media (those are separate and can change independently). When you want a complete, faithful checkpoint of a resource (content + quizzes + media together), **save a snapshot**.

### Printing a Resource (Take-Home Worksheet)

Click **Print** in the editor toolbar to produce a clean, printable version of the resource — useful for take-home exercises or a paper backup. The printout includes the cover image, your text and headings, all images and visual tools (number lines, fractions, charts, etc.), and a header with the lesson title plus Name/Date lines.

Quizzes print as **blank worksheet questions** for students to answer on paper:
- multiple-choice options become tick boxes,
- open-answer questions get ruled answer lines,
- fill-in-the-blank shows the sentence with blanks (plus a word bank where relevant),
- "put in order" and matching questions are shown scrambled, with space to answer.

Use your browser's print dialog to print directly or **Save as PDF** (the file is named after the resource).

**Students can print it too.** In **Worksheet** mode the lesson screen has a **Print** button in the header, so a student who prefers paper can print the same blank worksheet and work on it offline. (They can still complete it on screen — printing is just an option.)

### Sharing a Resource Publicly

Use share links to let people **without** a platform account take a lesson — useful for demos to prospective schools, parent showcases, or sending a resource for off-platform feedback.

1. Open the resource editor
2. Click **Share** in the toolbar
3. Click **Create share link**
4. (Optional) Add a **label** to remember which audience the link is for (e.g. "Year 6 parents", "Investor demo")
5. Choose the **bot** that will deliver the lesson (if you have multiple)
6. Click **Create link** — the share URL appears in the list above
7. Click the copy icon next to the link to copy it to your clipboard

Anyone with that URL can open the lesson and start a voice conversation with the bot, no sign-in required. You can revoke any link instantly by clicking the trash icon next to it; revoked links return 404 immediately.

**What happens during a share-link session:**
- The visitor sees a voice-driven lesson identical to what a student would see
- The bot is told this is an anonymous demo — it won't ask for personal details
- **Nothing is saved**: no transcript, no enrollment, no progress, no quiz scores
- The visitor's session ends when they close the tab; nothing carries over

**Cost**: share-link sessions consume your organization's OpenAI quota. Your admin can configure a separate OpenAI API key for share traffic with its own spend cap (Admin → Settings → OpenAI → Share-link key).

**If a link leaks**: revoke it from this dialog, or ask your admin to use the org-wide kill switch ("Allow share links" toggle in Settings → Sharing) to disable every share URL at once.

## Managing Courses

Courses are collections of resources organized for students to complete. Navigate to **Teaching > Courses**.

### Creating a Course

1. Click **New Course**
2. Enter a **Name** and **Description**
   - Optionally upload a **Course picture**. It's shown on the course's cards for students and parents, and on your organization's public homepage. Use a landscape JPG, PNG or WebP picture, 1200×600px (2:1) or larger and up to 2 MB. Cards crop it to fill, so keep the subject in the middle. Without one, the card shows a colour.
3. Select a default **Bot** for interactive lessons
4. Choose a **Progression Type**:
   - **Flexible**: Students complete resources in any order
   - **Sequential**: Students must follow your specified order
   - **Random**: The system selects the next resource
5. Apply **Tags** for organization
6. Click **Save**

### Adding Resources to a Course

1. Open the course details page
2. Click **Add Resource**
3. Select resources from your library
4. **Drag and drop** resources to set the order (important for sequential courses)

### Removing a Resource from a Course

Click the bin icon on the resource's row.

- If **no student has started it**, it is simply removed.
- If **students have started it**, it is **archived** instead: it leaves the course, so it no longer counts towards anyone's progress or completion, but every attempt, score and transcript is kept. Archived resources are listed under **Archived** at the bottom of the course's resources, with **Restore** to put one back at the end of the course. Adding the same resource again also restores it.

Removing or restoring a resource re-checks who has completed the course: removing the last unfinished resource can complete it for a student, and restoring one can reopen it.

### Overriding the Bot for Specific Resources

By default, all resources in a course use the course's default bot. However, you can override the bot for individual resources if needed:

1. Open the course details page
2. In the **Resources** list, find the resource you want to modify
3. Use the **Bot dropdown** on the right side of the resource row
4. Select a specific bot, or choose "Course default" to use the course's bot

This is useful when:
- A specific resource topic requires a different AI persona
- You want to test content with different bots
- Certain resources benefit from a bot with specialized knowledge

### Open Enrollment Courses

Open enrollment allows students to self-enroll in courses without teacher intervention. This is ideal for self-paced learning, large-scale courses, or when you want students to browse and join courses independently.

#### Enabling Open Enrollment

1. Create or edit a course
2. Scroll to the **Open Enrollment** section
3. Toggle on **Enable Open Enrollment**
4. Select a **Default Teacher** (required) - this teacher will be assigned to all self-enrolled students
5. Optionally set **Maximum Enrollments** to limit capacity (leave empty for unlimited)
6. Save the course

#### How Open Enrollment Works

When open enrollment is enabled:
- The course appears in the **Course Catalog** on the student dashboard
- Students can click "Enroll Now" to join the course immediately
- The selected default teacher is automatically assigned to manage these enrollments
- You receive an email notification each time a student self-enrolls
- If a maximum enrollment limit is set, the course shows as "Full" when capacity is reached

#### Default Teacher Requirements

The default teacher field is required for open enrollment courses because:
- Self-enrolled students need a teacher contact for questions
- Someone needs to monitor progress and provide support
- Enrollment notifications are sent to this teacher

You can select yourself or another teacher in your organization as the default teacher.

#### Capacity Limits

If you set a maximum enrollment limit:
- Students can only enroll if spaces are available
- The Course Catalog shows remaining capacity (e.g., "8/10 enrolled")
- Once full, the "Enroll Now" button is disabled and shows "Course Full"
- You can increase the limit later to allow more enrollments

#### Managing Self-Enrolled Students

Self-enrolled students appear on **Class progress** and on the course's **Enrollments** tab, like any other student. You can:
- View their progress in the course details
- Contact them through the platform
- Pause or remove their enrollment if needed
- Change their assigned teacher to a different person

#### Paid Courses

If your organization has Stripe Connect set up (see Admin Guide), you can create paid courses:

1. Enable **Open Enrollment** (required for paid courses)
2. Toggle on **Paid Course**
3. Select a **Currency** from the dropdown (GBP, USD, EUR, and others)
4. Enter the **Price** as a decimal amount (e.g., 29.99)
5. Save the course

Students will see the price with the appropriate currency symbol (£, $, €, etc.) in the Course Catalog. Clicking "Purchase Course" takes them through secure Stripe checkout, and they're automatically enrolled after payment.

**Note**: Paid course setup requires your organization to have completed Stripe Connect onboarding. Contact your administrator if the Paid Course option is disabled.

#### Teacher Preview Mode

Before releasing a course to students, you can enable **Teacher Preview Only** mode to test everything—including the purchase flow for paid courses—without students seeing the course in their catalog.

**Enabling Teacher Preview:**

1. Create or edit a course with Open Enrollment enabled
2. Toggle on **Teacher Preview Only**
3. Save the course

**How Teacher Preview Works:**

- The course only appears in the Course Catalog for users with the **teacher role**
- Students cannot see or enroll in the course
- A yellow "Teacher Preview" badge appears on the course card and in course lists
- Teachers can test the full enrollment process, including payment for paid courses
- Once testing is complete, toggle off Teacher Preview to release the course to students

**When to use Teacher Preview:**

- Testing a new course before public release
- Verifying paid course pricing and checkout flow
- Reviewing content organization and progression
- Training other teachers on course content before student rollout

**Tip:** Combine Teacher Preview with the Test Course feature to experience the course exactly as students will, while keeping it hidden from the student catalog.

### Managing Course Enrollments

In the course details, the **Enrollments** tab shows all enrolled students:
- Student name and email
- Assigned teacher
- Enrollment status
- Start and completion dates

For each enrollment, you can:
- **Change status**: Set to Active, Paused, or Completed
- **Play**: Open the enrollment in player mode (student view)
- **Test Course**: Open in test mode for verification
- **Copy URL**: Get the player URL for external sharing
- **Delete**: Remove the student from the course. If they had started it, their lesson history is kept and enrolling them again brings it back.

### Testing a Course

You can test a course enrollment to experience it as a student would:

1. Go to the course details page
2. Open the **Enrollments** tab
3. Find the enrollment and click the actions menu (three dots)
4. Select **Test Course**

This opens the player in test mode where you can:
- Complete lessons and interact with the AI tutor
- See how resources flow in sequence (for sequential courses)
- Verify the overall learning experience

**Test mode differences:**
- Sessions are marked as "tests" in the activity log
- Progress doesn't affect the student's actual completion status
- Test sessions are hidden by default when viewing student activity
- Transcripts are still captured for review

**Tip:** Always test a course before sharing the enrollment URL with students to ensure the content flows correctly and the AI tutor behaves as expected.

## In-Person Tutoring

If your organization sells in-person 1:1 lessons, you teach them through Beau too, and set Beau homework between them.

### Your teaching profile
In **Account › Teacher**, write a short bio, add your subjects, set a **price for each lesson length** you offer (30, 45 or 60 minutes; leave a length empty if you don't offer it) and the **notice** you need to reschedule a lesson (14 days by default). Parents see these, with your photo, when they choose a teacher; if your organization has a public homepage that shows its teachers, your photo, subjects and bio appear there too. New prices only apply to new bookings. To see your lessons in Apple Calendar, Google Calendar or Outlook, use **Add to my calendar app** on the **Calendar** page.

### Scheduling a new booking
Parents buy a whole half term (a length and lessons a week) and leave a note on the times that suit them. Their booking appears at the top of your **Calendar** under **To schedule**, with the note and their contact details.
1. Agree times with the family (by phone, email or in person).
2. Click **Schedule**. You see your week with your other lessons and time off; click the agreed time to add it as a weekly lesson (once per lesson a week).
3. Check the preview: every lesson it makes for the half term, and any week that clashes. Click **Schedule lessons**. Cover a clashing week with **Or add a single lesson**.

The family is emailed every date. If a lesson is cancelled later, the booking comes back under To schedule so you can rearrange it.

### Your lesson calendar
Open **Calendar**.
- **Click a lesson** to mark it attended, a no-show or cancelled, and to keep private notes on what you covered.
- **Move lesson** moves it to any date and time you choose, weekends included, as long as it doesn't clash with your other lessons or blocked-out time. For a cancelled or missed lesson the button is **Book a catch-up**: the lesson is scheduled again at no extra charge. The family is emailed the new time.
- **Block out time** for days or hours you can't teach: press the button, then drag across the hours (or select days in the month view). If lessons are already booked then, you'll see them and can cancel them; they come back under To schedule to rearrange. Click a grey block to add or change its note (only you and admins see it), or to unblock it.

### Creating a tutoring course
Create a course and choose **Course type: In-person lessons**. Set the lesson lengths parents can choose (30, 45 and/or 60 minutes), the most lessons a week, the location, subject, age range and what's included, then the **teachers** and **half terms** it's offered in. Turn on **Open Enrollment** to let parents book it. If only one teacher is listed, parents skip choosing.

To offer an existing in-person course yourself, edit it and add yourself under **Teachers parents can choose**; parents only see you once you've set a price for one of its lesson lengths in Account › Teacher.

An in-person course can also have **AI lessons**: choose a bot and add resources on its **Resources** tab, as for any course. Every student who books it gets them, to do between sessions. (For one student only, set homework instead.)

### Homework between lessons
Assign homework as usual from the student's page. For a student with in-person lessons, the due date defaults to the day before their next lesson.

## Managing Students

Navigate to **Teaching > Students** to view and manage your students.

### Inviting Students

1. Click **Invite Student**
2. Enter the student's email address
3. The system sends an invitation

Students appear with invitation status:
- **Pending**: Invitation sent, waiting for acceptance
- **Accepted**: Student has activated their account
- **Legacy**: Pre-existing user from before the invitation system

### Student Consent

If your organization has enabled parental consent (configured by your admin in **Settings > Students**), students must complete an on-platform consent form before accessing the dashboard. Consent is **automatically verified** when the parent submits the form — no manual approval is needed.

You'll see a **Consent** column in the student list with these statuses:

| Status | Description |
|--------|-------------|
| **N/A** | Consent not required (consent not enabled, or non-student role) |
| **Pending** | Consent required but parent hasn't submitted yet |
| **Submitted** | Parent has submitted the form (auto-verified) |
| **Verified** | Consent verified — student has full access |

**Managing consent:**

From the student list action menu (three dots), you can:

- **Verify Consent** (for Pending or N/A students): Manually grant access — useful when consent was received through other means (e.g., paper forms).

- **View Consent** (for Verified students): Review the submitted consent response, including parent name, checkbox answers, and custom question responses.

- **Resend Welcome Email** (for Verified students): Re-send the sign-in link if a student didn't receive or lost their welcome email.

- **Revoke Consent** (for Submitted or Verified students): Block the student's access by resetting consent to Pending. They must re-submit the consent form to regain access.

**Until consent is Verified, the student cannot start a lesson.** Every time they sign in to the dashboard they are taken to the consent form, and if they open a lesson link in the player they see a screen asking a parent or guardian to complete it, with a link to the form. Once a parent has submitted it, they are never asked again. If consent is switched on for an organization that already has students, those students are asked on their next sign-in. Contact your administrator if you need help with consent form configuration.

### The Student Page

Click a student's name — in the student list or on **Class progress** — to open their page. It is one screen, read top to bottom:

- **Header**: age, email, when they were last active, how many lessons they have done in the last 30 days, and when they joined.
- **Needs attention** (only shown when something does): an unacknowledged welfare or conduct flag (click **Review** to open that lesson's transcript), no lessons for 14 days while lessons are still to do, or parental consent outstanding.
- **Four numbers**: lessons completed (and how many this week), average score with its trend against the previous five lessons (shown once they have done ten), active weeks out of the last six, and unfinished attempts.
- **Homework**: the lessons you've set this student individually, with due dates — see **Homework** below.
- **Courses**: one row per class course. Each lesson is a dot — filled in its score colour when completed, an amber ring when started but not finished, a grey ring when not started (hover for the lesson name and score). The row shows the next lesson, or when the course was completed, and the course's average score. The menu (three dots) has **Play** and **Copy Play URL**. Completed courses are folded away under **Completed**.
- **From recent lessons**: **Working on** — the misconceptions the lesson grader noted in their last five lessons — and **Strengths** — the lesson objectives they met. Each item links to the lesson it came from.
- **Recent lessons**: the last five completed lessons with score and the grader's summary. Click one to open its transcript. **All activity** opens the full activity log.

Teachers see only the courses they teach a student in; administrators see all of them.

**Score colours** are the same everywhere in the dashboard: green from 80%, amber from 60%, red below 60%. A lesson with no score yet is never red.

### Homework

Homework is a lesson you set for **one student**, usually with a due date. Use it for individual practice, catch-up or extension work. For something the whole class is doing, put it in a class course instead.

#### Setting homework

Open the **Assign a lesson** dialog — from **Assign homework** on the student's Homework card, or from the resource list (**Assign to Student**). Choose the student and lesson (the teacher defaults to you, and the bot to the one their homework already uses), then one of:

- **Homework for <student>** (the default): choose a **due date** (a week from today unless you change it; clear it for no due date) and the bot. The student is emailed.
- **Enroll in class course "…"**: offered when a class course already contains that lesson and has other students in it. Best when the whole class is doing it.

If the student already has the lesson, the dialog says so. Assigning a lesson that is already their homework only changes its due date.

#### Due dates and overdue homework

A due date means "by the end of that day". Once it passes and the lesson isn't done, the homework is **overdue**:
- the student's page shows it in **Needs attention**, and the Homework card marks it **Overdue by N days**;
- on **Class progress** the row shows the **Overdue** state, near the top;
- the student sees it in red under **Your homework** on their dashboard, and as **Overdue** in the lesson list when they start.

Homework due within two days is shown in amber.

#### Managing homework

The **Homework** card on the student's page lists everything you've set them: overdue first, then by due date, then lessons with no due date, then completed ones with their score. For each:
- **Change due date** (calendar icon) — or clear it;
- **Remove** (bin icon) — takes the lesson out of their homework. If they have already attempted it, their attempts and transcripts are kept (and still show in their Activity Log); assigning the same lesson again brings it back.

Click a completed lesson to open its transcript.

#### Homework is not a course

Behind the scenes, each student's homework lives in a personal homework course, but you never manage it as one. It doesn't appear in **Courses**, it can't be renamed, opened for enrollment or priced, and no other student can be added to it. Opening it from a link takes you to the student's page.

**Several teachers, one student:** any teacher can set homework for a student; it all goes into that student's one homework list, and each lesson shows who set it ("Assigned 12 Oct by …"). Every teacher who has set a student homework sees that student's homework — on the student's page and on their own Class progress — even if they teach the student nothing else.

## Class Progress

Navigate to **Teaching > Class progress** to see, in one place, every student in every course you teach — sorted so the students who need you come first. Administrators see every teacher's students, and can narrow the view to one teacher.

### What each row shows

- **Student** (click to open their page)
- **Course**, and what's next: the next lesson, or for homework when it's due (or how many are overdue). Homework shows as **Homework · Assigned lessons**
- **Lessons**: the same lesson dots as the student page, with how many are done
- **Score**: their average across completed lessons, in the score colours
- **Last active**: how long ago they last worked on the course
- **State**: one of the states below

### States

| State | Meaning |
|-------|---------|
| **Needs attention** | The lesson grader raised a welfare or conduct flag that hasn't been acknowledged. Click **Welfare** or **Conduct** to open the lesson's transcript. |
| **Overdue** | Homework past its due date and not done (the row shows how many) |
| **Gone quiet** | The student has started, has lessons left, but hasn't done anything for 14 days |
| **Not started** | Enrolled, but hasn't attempted a lesson yet |
| **On track** | Working through the course |
| **Completed** | Every lesson done, or the enrollment is marked completed |
| **Paused** | You paused the enrollment |

The counts across the top are also filters — click **gone quiet** to see just those students, and click it again to go back. The page opens on everything still in play (completed and paused are hidden); **Show all** includes them. You can also filter by course.

### Changing enrollments

Tick one or more rows, then:
- **Pause**: the student cannot continue the course until resumed
- **Resume**: reactivate a paused enrollment
- **Remove**: take the student off the course. It asks you to confirm. Their lesson history and transcripts are kept, and enrolling them in the course again brings it back.

**Assign course** (top right) enrolls a student in a course or assigns a single resource. Test runs and the teacher-testing sandbox course never appear here.

## Monitoring Progress

### Activity Feed

Your dashboard shows recent student activity including:
- Student name
- Course and resource completed
- Score achieved (color-coded by performance)
- Completion timestamp

### Student Progress

For one student, use **the student's page** (above). For everyone at once, use **Class progress**.

### Session Transcripts

Every conversation between a student and the AI tutor is automatically transcribed and saved. This gives you valuable insight into how students engage with your content and how the AI delivers your lessons.

**What is transcription?**

During a lesson, both the student and AI tutor communicate via voice. The platform uses speech recognition to convert these spoken conversations into text, creating a complete written record of the session. This transcript is saved alongside other session data like scores and summaries.

**Why transcripts are valuable:**

- **Understand student struggles**: See exactly where students had difficulty or asked for clarification
- **Evaluate AI delivery**: Review how the bot presented your content and whether it stayed on topic
- **Identify content improvements**: Spot areas where your resource content could be clearer
- **Support student follow-up**: Reference specific moments when discussing progress with students
- **Quality assurance**: Verify that the AI tutor is behaving appropriately

**Viewing Transcripts:**

- On the student's page, click a lesson under **Recent lessons**, a **Working on** / **Strengths** item, or **Review** next to a flag.
- For older lessons, click **All activity** on the student's page and use the chat icon in the **Transcript** column.
- On **Class progress**, the **Welfare** / **Conduct** button opens the flagged lesson's transcript.

**Transcript Contents:**

The transcript viewer displays the conversation in a chat-style format:
- **Student messages** (right side): What the student said, transcribed from their speech
- **Bot messages** (left side): What the AI tutor said, transcribed from its speech
- **Images shown** (center): Thumbnails of images displayed during the lesson with timestamps
- **Quiz answers** (right side): What the student answered, whether it was correct, and the score

Each message shows the timestamp so you can follow the conversation flow.

**Spoken quiz answers:**

When a question used the **Voice** input type, its entry is labelled "Spoken Answer" and includes a **play button** — press it to hear the student's actual recording. Underneath, you'll see the transcription that the AI graded, marked "Transcribed from the recording".

This is worth listening to even when the score looks fine: the grade is based on *meaning*, so a student can score 100% while mispronouncing a key term or sounding hesitant — things only the audio will tell you. Conversely, a low score sometimes turns out to be a transcription slip rather than a wrong answer.

Downloading a transcript as text keeps the transcription (marked "Spoken Answer, transcribed") but not the audio — the recordings stay in the dashboard.

**Filtering the Activity Log:**

- Use the **Hide tests** toggle to exclude test sessions (on by default)
- Test sessions are marked with a blue "Test" badge when visible
- Only sessions with meaningful data (score, summary, or transcript) are shown

**Who can see a transcript:** administrators can see every transcript in the organisation; teachers can see transcripts for their own enrollments; and a student can see their own — their dashboard Activity Log has the same transcript viewer, including playback of their own spoken answers. No student can see another student's transcript or recordings.

## Safeguarding

Beau watches lessons for signs that a student may need your attention, and tells you when it
finds something. This section explains what to expect so you know how much to rely on it — and
where your own judgement still has to do the work.

### What Beau looks for

Two different things, treated separately:

- **Welfare** — a student disclosing self-harm, abuse, something frightening at home, or
  distress worth a second look. This includes indirect hints, not just explicit statements.
- **Conduct** — sustained rudeness or abuse aimed at the tutor. Not wrong answers, silliness,
  or a student going off-topic.

Neither ever affects a student's score. A child who is upset or disruptive is not marked down
for it.

### How you find out

**By email.** You will get a message about the lesson, with the student's words quoted exactly
as they said them. You get one email per lesson per severity level — so you are not emailed
repeatedly about the same thing, but if a lesson is re-reviewed and judged **more** serious, you
will hear about that.

**In the dashboard.** Open the lesson's transcript (Activity Log → transcript icon) and the
assessment appears at the top, above the score: how serious it is, the quotes, and why each was
flagged. There is a **Mark as reviewed** button that records that you have seen it and dealt with
it.

**Your organisation's admins see every concern.** **Admin ▸ Safeguarding** lists every welfare
and conduct concern for every student, whoever teaches them, with what is still waiting for
review — so a concern is never left unseen just because a teacher missed an email. When you mark
a concern as reviewed, it shows as reviewed there too, with your name.

### Two checks, not one

Beau can raise a concern **during** a lesson — within seconds of a student saying something — and
again **after** the lesson, when the full conversation is reviewed. The second check reads the
saved transcript, so it still works if the first one missed something, or if the lesson ended
unexpectedly.

Occasionally a lesson is ended automatically by the underlying voice provider's own safety
systems. If that happens the student is told the lesson has ended, that you will be able to see
what happened, and that they have not done anything wrong. A note is saved in the transcript so
you can see it was not a technical fault.

### What this is, and what it is not

Beau is a **prompt to look**, not a judgement. The flags are generated automatically from the
transcript, so:

- **It will sometimes flag things that turn out to be nothing.** A child writing a dramatic
  sentence for a creative task can read like a disclosure. That is deliberate — it is set to err
  towards telling you, because a wasted minute of your time costs less than a missed child. The
  quotes are there so you can dismiss a false alarm in seconds.
- **It will not catch everything.** Do not treat a lesson with no flag as evidence that nothing
  happened.
- **It is not a safeguarding procedure.** If you believe a child is at risk, follow your school
  or organisation's safeguarding process. Do not reply to the alert email — it is not monitored.

### Checking in on a lesson yourself

The transcript is always available whether or not anything was flagged, and reading one is the
most reliable way to understand how a student is doing. If a student seems withdrawn, is scoring
unusually badly, or abandons lessons part-way, the transcript will often tell you more than the
score does.

## Working with Bots

Bots are AI tutors that guide students through interactive lessons. Only administrators can create and configure bots.

When assigning resources or creating enrollments:
1. Choose a bot whose teaching style matches your content
2. Consider the bot's subject matter expertise
3. Ask your admin about available bots and their specializations

Contact your admin if you need a new bot created for specific content.

For details on how bots deliver lessons and how to write effective content for AI delivery, see the **Resource Guide**.

## Working with Images in Resources

**Every resource should include at least 1-2 images.** Images are essential for engagement — they break up text, illustrate concepts, and give the bot concrete material to discuss with the student. A resource with no images will feel text-heavy and lecture-like, even with great written content. **PNG**, **JPEG**, and **SVG** image formats are supported (max 5 MB). SVG is ideal for crisp, scalable diagrams.

When creating resources, you can add images to enhance learning. By default, the AI bot relies on text descriptions you provide for each image.

### Bot Vision Feature

For images where visual details matter, you can enable **"Bot can see this image"** in the image settings panel. This sends the actual image to the AI when it's displayed to the student, allowing the bot to see and discuss what's in the image.

**Best practices:**
- Enable Bot Vision for diagrams, charts, and complex visuals
- Leave it off for decorative images or simple visuals with good descriptions
- Always provide text descriptions even with Bot Vision enabled
- Note: Bot Vision only works for images, not videos or PDFs

### PDF Attachments

You can also attach PDF files to resources for worksheets, reference materials, and supplementary reading. PDFs are uploaded through the same media panel as images and videos (max 20 MB). Since the bot cannot read PDFs, always provide a thorough description so the bot knows what the document contains. During lessons, students see a download card and can save the PDF to their device.

For comprehensive guidance on writing effective content and working with media, see the **Resource Guide**.

## Working with Visual Tools in Resources

Alongside uploaded images, you can author **visual tools** — crisp, lightweight diagrams drawn from a few settings: number lines, fractions, grids, timelines, math equations, styled word cards, counters, analog clocks, ten frames, bar charts, base-ten blocks, money, phoneme frames, place-value charts, an advanced Chart.js chart, and — for older pupils — function graphs, geometry diagrams, coordinate grids, box plots, probability trees, Bohr atom models, pH scales, and phonics tools (syllable split, onset & rime). Insert one from the editor's **Insert ▸ Visual** menu (grouped by subject); it drops a `::visual{#id}` block that you edit in the right-hand panel (settings autosave, with a live preview), and the bot shows it at that point in the lesson. A visual can also serve as a cover image or a quiz illustration.

For a visual placed in the **lesson body**, you can optionally add a **Question**, **Answer**, and **Hint** in its settings panel — these let the bot quiz the student on the visual, exactly like an image. You never write a description for a visual: the bot is automatically given one based on the visual's settings, so it always matches what's on screen. (Cover images and quiz illustrations show only the settings.)

Reach for a visual tool whenever the content is structured data the platform can draw (a fraction, a number line, a clock face); use an uploaded or AI image for photos and rich illustrations. See the **Resource Guide** for the full list of kinds and when to use each.

## Working with Quizzes in Resources

**Quizzes are essential for interactive, engaging lessons.** Aim for **at least 2-3 quizzes per resource**, placed after each major concept. Quizzes keep students actively thinking and participating — without them, lessons feel passive and text-heavy. Use a variety of quiz types (single choice, multiple choice, open answer, ordered list, matching, voice) to keep things interesting.

The platform supports six question types: single choice, multiple choice, open answer, ordered list, matching, and fill in the blank.

### Quiz Question Types

**Single Choice**: Student selects one answer from a list
- Best for clear-cut facts with one correct answer

**Multiple Choice**: Student selects all correct answers from a list
- Best for "select all that apply" questions

**Open Answer**: Student types their answer, evaluated by AI
- Best for numerical answers, calculations, fractions, or short written responses
- Supports input restrictions: Text, Integer, Decimal, Fraction, or **Voice**
- Numeric types (Integer, Decimal, Fraction) show an on-screen keypad instead of the native keyboard
- Fraction input shows a visual numerator/denominator entry
- Optional min/max range and allow-negative constraints for numeric types
- **Exact match (spelling & dictation)**: turn on the **Exact match** switch under an Open Answer question to grade the answer *exactly* — capitalisation and punctuation count — with specific feedback ("Check your capital letters", "You forgot the full stop", "You got 2 letters wrong", "2 words are wrong"). This skips AI grading. Pair it with an **audio prompt** to make a spelling or dictation test: on the question's illustration, choose the **Audio** tab, type the word or sentence, pick a voice, and preview it (regenerate if a tricky word like "read" or "lead" sounds wrong). The student hears the clip with a play button and types what they hear — the text itself is never shown.
- **Voice (spoken answers)**: set Input Type to **Voice** and the student answers by speaking. Their words are transcribed and graded against your Expected Answer and Evaluation Criteria, exactly like a typed open answer — so meaning is marked, not pronunciation.

  What the student sees depends on the resource's delivery mode:

  | Mode | Student experience |
  |------|--------------------|
  | **Conversation** | No button — they just answer out loud, as they've been talking all lesson. The tutor stays quiet, then shows the words it heard with **Send answer** / **Say it again**. |
  | **Presentation** | A **Record** button. They can listen back and re-record as often as they like before submitting. |
  | **Worksheet** | No recorder — it prints as a "🗣️ Speak your answer aloud" prompt with ruled lines for you to mark by hand, and isn't auto-graded. |

  **Speak-then-type exercises:** if you pair a voice question ("say this phrase") with a typed one ("now type it"), turn **off** the "Show the student what they said" switch on the voice question. Otherwise the transcription appears on screen and hands them the spelling. With it off the tutor is also told not to repeat or spell the phrase — and you still see the transcript in the lesson record.

  Writing them well: say explicitly in Evaluation Criteria that fillers, false starts and self-corrections are fine and that exact wording isn't required — spoken answers don't read like written prose. Don't use Voice for spelling (transcription can't judge it; use the dictation recipe above), which is why the **Exact match** switch is hidden for voice questions. And **fill in the student's date of birth** on their profile: the transcription is held on screen for a reading time based on their age, and without a date of birth we assume 10.

**Ordered List**: Student arranges items in the correct order using drag and drop
- Best for sequencing steps, ordering events, ranking values, comparing numbers
- Items are shuffled for the student; they drag them into the correct position
- Partial credit scoring: score based on how many items are in the correct position
- Example: "Put these in order from smallest to largest: 3/5, 0.55, 62%, 1/2"

**Matching**: Student matches items from two columns by dragging right-side items to align with left-side terms
- Best for vocabulary pairing, definitions, cause-and-effect, categorization
- Left column shows fixed terms; right column shows shuffled matches that students drag to reorder
- Partial credit scoring: score based on how many pairs are correctly matched
- Example: "Match each part of speech to its example: verb → jump, noun → book, adverb → quickly"

**Fill in the Blank**: Student fills in missing words within a sentence
- Best for vocabulary in context, grammar, reading comprehension, factual recall
- Teacher writes a sentence using [] to mark blank positions (e.g., "The capital of [] is []")
- Two input modes: **Free text** (student types answers) or **Word bank** (student selects from a dropdown of options including distractors)
- Partial credit scoring: score based on how many blanks are filled correctly
- Example: "Water freezes at [] degrees Celsius and boils at [] degrees Celsius"

### How Quizzes Work

When the AI tutor displays a quiz during a lesson:
1. The student sees the question (with options or a text input)
2. The student selects their answer(s) or types their response
3. The system checks correctness (AI evaluates open answer answers)
4. The bot receives feedback about whether the student answered correctly
5. If incorrect and retries are allowed, the bot can encourage another attempt using the hint you provided

### Creating Quizzes

1. Open a resource in the editor
2. Click the **Quiz** button in the toolbar
3. Fill in the quiz details:
   - **Description**: Tells the bot when to use this quiz
   - **Question**: The question text shown to students
   - **Question Type**: Single choice, Multiple choice, Open answer, Ordered list, or Matching
   - **Answers**: The answer options with correct answers marked (choice questions)
   - **Expected Answer**: The correct answer to evaluate against (open answer)
   - **Input Restriction**: Text, Integer, Decimal, or Fraction (open answer)
   - **Min/Max Value**: Optional range constraints for numeric types (open answer)
   - **Allow Negative**: Whether negative values are accepted (open answer, numeric types)
   - **Evaluation Criteria**: Optional guidance for AI grading (open answer)
   - **Retry Limit**: How many attempts students get (leave empty for unlimited)
   - **Hint**: Guidance for the bot to help struggling students
4. Click **Save**

### Quiz vs. Conversational Questions

Use **quizzes** when you want:
- Structured assessment with automatic correctness checking
- Retry attempts with hints
- Clear right/wrong feedback
- Numerical, calculation-based, or fraction questions (open answer)

Use **conversational questions** (in your content) when you want:
- Open-ended discussion
- The bot to evaluate complex free-form responses
- More natural dialogue flow

### Viewing Quiz Results

Quiz responses are captured in session transcripts. When viewing a transcript:
- Quiz displays show when the bot presented each quiz
- Quiz responses show what the student selected/typed and whether it was correct
- Open answer responses include the AI's feedback and score
- You can see how many attempts were needed

For comprehensive guidance on creating effective quizzes, see the **Resource Guide**.

## Tips for Effective Teaching

1. **Use clear resource names** that describe what students will learn
2. **Apply tags consistently** to make resources easy to find
3. **Choose the right delivery mode** - conversation for interactive lessons, presentation for lecture-style narration, or worksheet for a no-bot, self-paced/printable handout written directly for the student
4. **Choose the right progression type** - sequential for prerequisite content, flexible for independent topics
5. **Include plenty of quizzes** - aim for at least 2-3 per resource. Quizzes are the primary way students actively engage with content. Lessons without enough quizzes feel passive and text-heavy.
6. **Include images in every resource** - aim for at least 1-2 images. Visuals break up text, illustrate concepts, and keep students interested. A text-only resource is almost always too dry.
7. **Avoid text-heavy sections** - if any section is more than a few sentences without a quiz or image, add interactivity
8. **Mix quiz types for variety** - use single choice for quick checks, multiple choice for "select all", open answer for calculations, ordered list for sequencing, matching for pairing concepts, fraction for math, and voice when you want the student to *say* the answer (reading aloud, language practice, explaining their reasoning). Variety keeps students engaged.
9. **Monitor the activity feed** to identify students who may need help
10. **Test resources and courses** before assigning to students to verify the AI delivers content correctly
11. **Review session transcripts** periodically to understand how students interact with your content
12. **Enable Bot Vision** for images where the AI needs to see visual details
13. **Provide helpful quiz hints** that guide students without giving away the answer

---

## The AI Assistant

Beau has an AI Assistant built into the dashboard. It can create resources, build courses,
review how a student is doing, and suggest improvements to lessons you have already published —
by having a conversation with it, rather than filling in forms.

Find it at **AI Assistant** in the left-hand navigation.

### What it can do

Pick a skill when you start a conversation. Teachers have three:

- **Create resource** — describe a topic and audience, and it drafts the content, writes quizzes,
  creates images and diagrams, and assembles a ready-to-use resource. It can build a course and
  add the resource to it as well.
- **Evaluate student** — analyses a student's progress, quiz scores and lesson transcripts, and
  produces a report on how they are getting on and where they may need help.
- **Optimize resource** — looks at how students actually experienced one of your resources —
  where they struggled, which questions they got wrong, what they said — and suggests specific
  edits. It only suggests; nothing changes until you approve it.

Administrators also see **Usage insights** for organisation-wide reporting.

### Having a conversation

Type what you want in plain language. The Assistant works through the task in steps and shows
you what it is doing as it goes — which resource it is reading, which quiz it is creating — so
you can follow along rather than waiting for a result to appear.

If it heads in the wrong direction, press **Stop** and tell it what you would rather it did.

Conversations are saved. You can leave one and come back to it, and start a **New conversation**
for an unrelated task. Give a conversation a name if you want to find it again easily.

### Examples

**Creating a resource:**
> "Create a Year 6 maths resource about adding fractions with different denominators. Use
> conversation mode, include diagrams and at least 3 quizzes."

It will reuse your existing tags, draft the content with learning objectives, create diagrams and
quizzes at sensible points, and leave you a resource you can preview and edit.

**Checking on a student:**
> "How is Harry doing in the Prepositions course? Are there any areas where he's struggling?"

It will look at his enrollments, scores and attempt counts, read the lesson transcripts, and give
you a report with specific recommendations.

**Improving a lesson that is not working:**
> "Students keep getting question 3 wrong in my Fractions lesson. What should I change?"

It will look at what students actually answered and said, and propose edits for you to approve.

### Getting good results

- Be specific about the **audience** — age, year group, ability. This shapes the language and
  difficulty more than anything else you say.
- Say which **delivery mode** you want: *conversation* for a two-way voice lesson, *presentation*
  for narration the student listens to, or *worksheet* for a self-paced written activity with no
  bot.
- **Review what it produces.** It drafts quickly and confidently, and it does not know your class.
  Read the content, check the quiz answers, and preview the lesson before assigning it.
- **Test the resource** afterwards using Test Resource, so you hear how it is actually delivered.

### Using Claude Code or Cowork instead

The same capabilities are available outside the dashboard if you already use Claude. Install the
Beau plugin with `/install-plugin beau-education/beau-plugin` and sign in with your Beau account,
then use `/beaubot:create-resource` or `/beaubot:evaluate-student`. Claude web can connect to
`https://api.beau.bot/mcp` as a remote connector.

This is the same underlying toolset; the dashboard Assistant is simply the version that needs no
setup.
