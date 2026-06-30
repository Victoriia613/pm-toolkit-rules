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

## Handling unsorted emails (no project match)

If an email looks like a genuine client/stakeholder email but doesn't match any project's signals in the registry, do not silently skip it. Instead:

1. Collect all such emails into a list.
2. After completing the normal sync summary, present the unsorted emails to the PM using the `AskUserQuestion` tool — one question per email (batch up to 4 per call). For each email show: sender, subject, and a one-line summary of the email content.
3. For each unsorted email, offer these options:
   - One option per active project (e.g. "SGD B2B", "Gemoss") — selecting one assigns the email to that project and creates the to-do item immediately
   - "Skip — not relevant" — discards the email for this sync run
4. After the PM responds, process the assigned emails as normal (add to-do items with thread name and deadline as above), then offer to update the project's signals in the registry if the same sender/domain keeps appearing.

Present the best guess as the first option (based on partial keyword or domain similarity), labeled with "(best guess)". This lets the PM confirm quickly with a single click in the common case.

---

## Changing these rules

This file is shared across every PM using the plugin. Adding new patterns here improves the sync for everyone; keep changes general rather than tailored to one PM's inbox quirks (use the local `projects-registry.md` signals for anything project- or PM-specific).
