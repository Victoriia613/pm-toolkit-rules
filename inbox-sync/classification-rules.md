# Inbox Sync — Classification Rules (Org-Wide)

Guidance for deciding what counts as an actionable item in the `inbox-notion-sync` skill, independent of any specific project's signals (which live in each PM's local `projects-registry.md`).

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

## Resolution language (Step 3 of the sync task)

Treat an item as resolved only when the email contains a clear signal of one of:
- Explicit confirmation the task is complete ("done", "approved", "all set", "confirmed")
- The actual requested deliverable/input itself (the file, the answer, the sign-off)
- An explicit statement that no further action is needed

Generic positive language ("sounds good", "great") without one of the above is **not** sufficient to mark an item resolved — when in doubt, leave it open rather than risk a false close.

## Changing these rules

This file is shared across every PM using the plugin. Adding new actionable/non-actionable patterns here improves classification for everyone; keep changes general rather than tailored to one PM's inbox quirks (use the local `projects-registry.md` signals for anything project- or PM-specific).
