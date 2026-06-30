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

---

## Duplicate detection

Before creating any new to-do item (Client To-Do's or PM To-Do's), check whether an equivalent open item already exists to avoid stacking duplicates across sync runs.

**For Client To-Do's (database):** Query the data source for open items (Status ≠ "Completed") with a Name that closely matches the task you're about to create — use the email thread subject as the key anchor. If a match exists, skip creation and optionally append a dated note to the existing item's page body referencing the new email.

**For PM To-Do's (database mode):** Query `collection://bedf182b-9cc2-48bf-9c4c-78c8a0ee3b79` for items where Project matches AND Name is substantially similar AND Status ≠ "Done". If found, skip creation.

**For PM To-Do's (page mode):** Fetch the page content and check whether any unchecked checklist item contains the same Reference (email thread subject). If found, skip creation.

Similarity check: an item is a duplicate if the thread subject or core task description matches an existing open item for the same project. Err on the side of skipping — a missed duplicate is worse than a missed item.

---

## Resolution language (Step 3 of the sync task)

Treat an item as resolved only when the email contains a clear signal of one of:
- Explicit confirmation the task is complete ("done", "approved", "all set", "confirmed")
- The actual requested deliverable/input itself (the file, the answer, the sign-off)
- An explicit statement that no further action is needed

Generic positive language ("sounds good", "great") without one of the above is **not** sufficient to mark an item resolved — when in doubt, leave it open rather than risk a false close.

---

---

## PM To-Do's management

These rules apply every time the sync reads or writes PM To-Do's, whether adding new items, cleaning up completed ones, or escalating stale items.

### Detecting the storage mode

Inspect the `PM To-Do's page` value in the registry row:
- If it starts with `collection://` → **database mode**: use structured Notion database fields (Name, Project, Priority, Deadline, Status, Reference).
- If it starts with `https://` → **page mode**: use checklist items on a plain Notion page (legacy — new setups should use database mode).

### Database mode (preferred)

**Schema fields:**
- **Name** (title) — task description, no project prefix needed (Project field handles routing)
- **Project** (select) — the project display name from the registry (e.g. "SGD B2B", "Gemoss")
- **Priority** (select) — `high`, `Medium`, or `Low`
- **Deadline** (date) — set if extracted from email; leave empty otherwise
- **Status** (select) — always `To do` when creating
- **Reference** (text) — email thread subject

**Derive priority from the email:**
- Explicit urgency language ("urgent", "ASAP", "blocking", "critical"), deadline today or tomorrow → `high`
- General request with a stated deadline → `Medium`
- FYI-style obligation, no urgency → `Low`
- When unclear → `Medium`

**Removing resolved items:** At the start of every sync run, before adding new items, query the database for items where Status = "Done" and delete them. Do not archive — delete.

**Staleness escalation:** After the cleanup step, query the database for items where Priority = "high" AND Status ≠ "Done" AND createdTime is more than 2 days ago. If any are found, include them in the sync summary under a "⚠️ Stale HIGH items" heading — list the item name, project, and how many days it has been open. Do not auto-resolve or modify these items; just surface them for the PM's attention.

### Page mode (legacy)

If the PM To-Do's URL is a plain page (starts with `https://`):

**Priority ordering** — maintain items in priority order within each project section:
1. ⚠️ HIGH (items with explicit urgency)
2. Medium
3. Low / unspecified

Indicate priority inline: `- [ ] [SGD B2B] Task name ⚠️ HIGH`

**Grouping** — if the same page URL appears in multiple registry rows, group items under H2 project headings (e.g. `## SGD B2B`). Single-project pages use a flat list.

**Removing resolved items** — scan for checked checkboxes (`- [x]`) and delete them before adding new items.

**Staleness escalation** — flag any ⚠️ HIGH items that appeared in the previous sync run and are still present (indicating no resolution). List them in the summary under "⚠️ Stale HIGH items".

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

### Handling a free-text project name from the PM

If the PM types a project name not found in the registry (e.g. they selected "Other" and typed "Ermitazas"):
1. Acknowledge that the project isn't registered yet.
2. Ask: "Would you like to register [project name] now so future emails route automatically?" with options "Yes, register it" / "Skip for now".
3. If yes: ask for the Jira key and any initial signals (domain, stakeholder names), then append a new row to `projects-registry.md` with Status = `active`. The Client To-Do's data source and PM To-Do's page will be blank until the PM runs full onboarding for this project.
4. Once registered, proceed to create the to-do item for the current email as a PM task (since there's no Client To-Do's database yet).

### Signal learning

After completing triage (all unsorted emails assigned or skipped), check whether any assignment reveals a sender name or domain that consistently signals a known project but isn't in that project's Signals column yet.

If so, mention it at the end of the summary:
> "Jonas (jonas@...) was assigned to SGD B2B — consider adding 'Jonas' or the domain to SGD B2B's signals so future emails route automatically."

Do not auto-update the registry — always suggest and let the PM confirm. If they confirm, append the new signals to the relevant row in `projects-registry.md`.

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
