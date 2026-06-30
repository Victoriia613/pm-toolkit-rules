# Inbox Sync — Classification & Operational Rules (Org-Wide)

Guidance for the `inbox-notion-sync` skill: what counts as actionable, how to write items to Notion, and how to handle emails that don't match any project.

---

## What counts as actionable

**Received emails** — actionable if the email contains a request, a question requiring a reply, a follow-up on existing work, or information that resolves an open to-do. Signals: direct questions, "can you", "please", deadlines mentioned, explicit asks for review/approval/input.

**Sent emails** — actionable (as a client/third-party obligation) if the PM's own email asks the recipient to do something and is now awaiting their action. Signals: "please review and approve", "please confirm", "please provide", "could you clarify", "let me know by [date]", "waiting on your sign-off".

## What is NOT actionable (skip these)

- Newsletters, marketing emails, automated digests
- Calendar notifications, meeting reminders (unless the body contains a genuine new ask)
- FYI-only emails with no requested action — informational updates, "just looping you in"
- Out-of-office autoreplies
- Threads that are purely social/administrative (e.g. scheduling a call) unless the scheduling itself is the open item
- Read receipts, delivery confirmations

## Borderline cases

- **A reply that just says "thanks" or "got it" with no new content** → not actionable, but check if it resolves an existing open item (Step 3 of the sync task) before discarding.
- **A forwarded email with no added commentary** → only actionable if the forwarded content itself contains a clear ask; otherwise treat as FYI.
- **A thread with multiple asks bundled together** → split into separate to-do items rather than one vague item, so each can be tracked/resolved independently.

---

## Notion field mapping — how to write items

When creating a new to-do item in Notion (Client To-Do's or PM To-Do's), always populate these fields if the data is available:

### Thread name (reference)
- Always include the email thread subject as a reference on the to-do item.
- For **Client To-Do's** (database items): set the "Reference" property (or the closest equivalent — check the schema) to the email thread subject. If no dedicated field exists, append it in parentheses to the task Name: `[SGD B2B] Confirm delivery date (re: "Q3 shipment follow-up")`.
- For **PM To-Do's** (page-based checklist items): add the thread subject in parentheses after the task text, e.g. `- [ ] [SGD B2B] Reply to Mette re pricing (re: "Updated price list request")`.

### Deadline
- Scan the email body for any explicit deadline or date commitment: "by Friday", "before EOD", "deadline is June 30", "please confirm by next Tuesday", specific dates, etc.
- If a deadline is found, set the **Deadline** field on the Client To-Do's database item to that date.
- For relative dates (e.g. "by Friday"), resolve to an absolute date using today's date.
- If no deadline is mentioned, leave the field empty — do not guess or set a default.

---

## Resolution language (Step 3 of the sync task)

Treat an item as resolved only when the email contains a clear signal of one of:
- Explicit confirmation the task is complete ("done", "approved", "all set", "confirmed")
- The actual requested deliverable/input itself (the file, the answer, the sign-off)
- An explicit statement that no further action is needed

Generic positive language ("sounds good", "great") without one of the above is **not** sufficient to mark an item resolved — when in doubt, leave it open rather than risk a false close.

---

---

## PM To-Do's page management

These rules apply every time the sync reads or writes the PM To-Do's page, whether adding new items or cleaning up completed ones.

### Priority ordering

Always maintain PM To-Do items in priority order — highest priority at the top, lowest at the bottom:

1. ⚠️ HIGH
2. Medium (no emoji)
3. Low (no emoji)
4. Unspecified priority — append at the bottom

When adding a new item, insert it at the correct position within its priority band, not simply at the end of the list. After adding, re-check the full list order and reorder if needed.

Indicate priority inline in the checklist text using `⚠️ HIGH`, `Medium`, or `Low` suffix — e.g.:
`- [ ] [SGD B2B] Confirm delivery date (re: "Q3 shipment follow-up") ⚠️ HIGH`

Derive priority from the email: explicit urgency language ("urgent", "ASAP", "blocking", "critical", deadline today/tomorrow) → HIGH; general requests with a stated deadline → Medium; FYI-style obligations with no urgency → Low. When unclear, default to Medium.

### Grouping by project (shared PM To-Do's page)

If two or more active registry rows point to the **same PM To-Do's page URL**, group all items under a heading per project. Use H2 (`##`) headings matching the project display name (e.g. `## SGD B2B`, `## Gemoss`). Within each group, maintain priority order as above.

When writing to a shared page:
1. Fetch the current page content first.
2. Locate the correct project heading (create it if absent, appending it at the bottom).
3. Insert the new item in the correct priority position within that heading's block.
4. Never move items from one project's heading to another.

If a PM To-Do's page is **dedicated to a single project** (its URL appears in only one registry row), do not add a project heading — just maintain the flat priority-sorted list.

### Removing resolved items

At the start of every sync run, before adding any new items, read the PM To-Do's page for each active project and scan for completed checklist items — these are to-do blocks where the checkbox is checked (Notion marks them with strikethrough in the UI).

Remove any checked items from the page entirely. Do not archive or move them — just delete them so the list stays clean.

Apply this cleanup to all project sections on a shared page, not just the project whose emails are being processed in the current run.

---

## Handling unsorted emails (no project match)

### Preference check (do this first)

Before doing anything with unsorted emails, read `pm-toolkit-prefs.md` from the working directory (same folder as `projects-registry.md`). If the file contains `unsorted_email_summary: off`, skip the entire unsorted triage step silently — don't mention it in the summary either.

If the file doesn't exist or the setting is absent, the feature is **on** by default.

### System notifications — always skip silently

Never triage system-generated notifications, regardless of the preference setting:
- Jira notifications (issue created, commented, status changed, assigned, etc.)
- Notion notifications (page shared, comment added, etc.)
- GitHub/GitLab/Bitbucket notifications
- Any noreply or no-reply sender address
- Automated tool notifications from project management, CI/CD, monitoring, or analytics platforms

Only proceed with triage for emails that look like genuine human-written messages from clients or stakeholders.

### Triage flow (when feature is on)

1. Collect all unmatched human emails into a list.
2. After completing the normal sync summary, present them to the PM using the `AskUserQuestion` tool — one question per email (batch up to 4 per call). For each email show: sender, subject, and a one-line summary of the email content.
3. For each unsorted email, offer these options:
   - One option per active project (e.g. "SGD B2B", "Gemoss") — selecting one assigns the email to that project and creates the to-do item immediately
   - "Skip — not relevant" — discards the email for this sync run
4. After the PM responds, process assigned emails as normal (add to-do items with thread name and deadline as above), then offer to update the project's signals if the same sender/domain keeps appearing.

Present the best guess as the first option (based on partial keyword or domain similarity), labeled with "(best guess)".

### Disable/enable toggle

After presenting all unsorted emails (once all triage questions are answered), show one final `AskUserQuestion` with a single question:

> "Unsorted email summaries are currently **on**. Would you like to change this?"

Options:
- "Keep on" — do nothing
- "Turn off — don't show unsorted summaries in future syncs" — write `unsorted_email_summary: off` to `pm-toolkit-prefs.md` (create the file if it doesn't exist, update the line if it does)

When the feature is **off** and the PM later asks to re-enable it, or when asked directly, write `unsorted_email_summary: on` (or remove the line) in `pm-toolkit-prefs.md`.

---

## Changing these rules

This file is shared across every PM using the plugin. Adding new patterns here improves the sync for everyone; keep changes general rather than tailored to one PM's inbox quirks (use the local `projects-registry.md` signals for anything project- or PM-specific).
