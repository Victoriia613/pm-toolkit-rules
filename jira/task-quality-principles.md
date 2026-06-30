# Task Quality Standards (Org-Wide)

These standards apply to every Jira task created through the `create-jira-task` skill, regardless of project.

## 8 Principles of a Good Task

1. **Clear, Measurable Progress** — Define what changes in the system once the task is complete. Must be easy for PM to validate, QA to test, and customer to see.
2. **Business-Level Expression** — Write in terms of business outcomes, not technical jargon. Express "before vs. after" in a way the customer would understand.
3. **Specific, Not Vague** — Avoid "fully functional", "ready for orders", "improve performance", "ensure accuracy". List concrete actions and results instead.
4. **Incremental Deliverables** — Break large features into staged, demonstrable steps. Each step should show visible progress.
5. **Platform-Aware Guidance** — PM must know platform basics (Magento, Shopify, Pimcore, etc.). Add hints so developers don't reinvent the wheel.
6. **Self-Contained and Testable** — Include acceptance validation steps. Even a non-developer should understand how to check if it's done.
7. **Concise but Complete** — Not three pages of text, but also not a vague one-liner. Capture essential progress and validation precisely.
8. **Customer-Visible Change** — Think in terms of what the customer can see, do, or validate.

## 7 Qualities of a Bad Task (avoid these)

1. **Vague / Unverifiable** — "Connect ERP", "Improve cache", "Ensure accurate listings" — no measurable outcome.
2. **Abstract Business Value Only** — "Increase conversion", "boost revenue" — too high-level, not actionable.
3. **Overloaded / Merged** — Multiple unrelated requirements in one task.
4. **Dependency on Developer Interpretation** — PM assumes developer will "figure it out".
5. **Buzzwords and Ambiguity** — "Fully integrated", "functioning", "ready", "optimized" without specifics.
6. **Platform Ignorance** — Asking for custom builds where standard features exist.
7. **No Validation Path** — Tasks that cannot be easily checked by PM or QA.

## KISS Rules for Sentence Quality

- Each sentence or bullet must communicate **one clear idea**.
- Wording must be **direct and natural** — understandable on first read.
- Prefer **subject-verb-object** structure.
- **No redundant wording** — same meaning with fewer words is always better.
- Grammatically awkward, clunky, or hard-to-parse sentences are invalid even if meaning is recoverable.

## Banned Words / Phrases

Never use these in task descriptions:
- "fully functional", "fully integrated", "fully working"
- "ready for orders", "ready for customers"
- "ensure accuracy", "ensure it works"
- "improve performance" (without a specific metric)
- "optimize" (without specifying what and by how much)
- "enable customers to…" (replace with specific observable outcome)

## Task Structure (Org Template)

### TITLE
- Must have a call-to-action verb. Example: "Remove H2 tag from email block" not "H2 tag in email block".
- **Exception for Bugs:** Bug titles must describe the broken behaviour as a statement, not a call-to-action. Write what is happening, not what should be done. Example: "Product import from DIH is failing when product category does not exist in M2" — not "Fix product import from DIH failing when product category does not exist in M2". The title should read like a bug report, not a task assignment.

### PLATFORM AWARENESS
Expected solution recipe — one of:
1. Configuration of existing features
2. Extension installation and configuration
3. Customizing existing module
4. Data migration
5. Implementing a new custom module
6. FE development

### BEFORE & AFTER
- Current state (Before) and result state (After) clearly separated.
- Use wireframes, images, or explanation video where applicable.

### REAL EXAMPLE
- A real-world use case example that demonstrates the outcome.
- Helps developer and QA understand exactly what should be achieved.

### PROOF (Test Cases)
- Detailed test steps as if written for a machine: exact inputs, exact expected outputs.
- Format:
  ```
  Test case name
  1. Step one
  2. Step two
  Expected result: XYZ
  ```
- Do not use vague test cases like "test with valid email" — use actual values.
- If descriptions exceed 20–30 lines, split the ticket.

### BOUNDARIES
- List explicitly what is NOT included in this task that could be assumed to be included.

### ASSETS (IF ANY)
- List designs, documentation, credentials, APIs — point to the exact relevant part.

## Templates by Task Type

### Story / Sub-task template:
```
TITLE: [Action verb + specific outcome — no buzzwords]

PLATFORM AWARENESS
[Which of the 6 solution types applies. Give developer direction.]

BEFORE & AFTER
Before: [Current state — what is broken, missing, or wrong]
After: [Desired state — what will be observable and measurable]

REAL EXAMPLE
[Concrete real-world scenario demonstrating the outcome]

PROOF
[Test case name]
1. [Exact step]
2. [Exact step]
Expected result: [Specific, observable outcome]

[Additional test cases as needed]

BOUNDARIES
[What is explicitly NOT included in this task]

ASSETS
[Links, designs, credentials — or "None"]
```

### Bug template:
```
TITLE: [Short statement describing the broken behaviour — not a call-to-action.
  Good: "Checkout fails when applying coupon on EU store"
  Bad:  "Fix checkout failing when applying coupon on EU store"]

JIRA FIELD MAPPING — populate these as dedicated Jira fields only (no description needed):
  - Summary          → ticket title (see TITLE rule above)
  - Environment      → Jira "Environment" field (General tab) — `environment` system field (ADF)
  - Steps to reproduce → Jira "Steps to reproduce" field (General tab) — `customfield_10091` (ADF)
  - Actual Result    → Jira "Actual Result" field (General tab) — `customfield_10089` (ADF)
  - Expected Result  → Jira "Expected Result" field (General tab) — `customfield_10090` (ADF)
  - Screenshot/Video → Jira "Screenshot/Video" field (General tab) — `customfield_11703` (ADF)
    → Include when: the bug is visual/UI (wrong layout, missing element, display glitch, broken rendering)
    → Ask the PM: "Do you have a screenshot or video link to attach?"
  - Logs             → Jira "Logs" field (General tab) — `customfield_11704` (ADF)
    → Include when: the bug involves a backend/server/import/export/API/integration error
    → Ask the PM: "Do you have relevant error logs or a stack trace to include?"

No description is required for bug tickets. All relevant information goes into the dedicated General tab fields above.
```

### Epic template:
```
TITLE: [Epic: high-level feature name — e.g. "Epic: ERP Stock Integration"]

GOAL
[Business outcome this epic delivers — in customer-visible terms]

SCOPE OVERVIEW
[What this epic covers at a high level — the major pieces]

DELIVERABLES (Stories to be created)
- [ ] Story 1: [title]
- [ ] Story 2: [title]
- [ ] Story 3: [title]
[These stories should represent incremental, demonstrable steps]

OUT OF SCOPE
[What is explicitly not part of this epic]
```

## Quality Self-Check (apply before presenting any draft)

- [ ] Title has an action verb and is specific (Stories/Tasks) — OR is a behaviour statement for Bugs (no action verb)
- [ ] No banned words used
- [ ] Before/After is concrete and measurable
- [ ] Test cases use exact values, not "test with valid input"
- [ ] No sentence combines multiple distinct ideas (KISS)
- [ ] Boundaries section is present
- [ ] Task is a single concern — not overloaded
- [ ] For Bugs: Environment, Steps to reproduce, Actual Result, and Expected Result are set as dedicated Jira fields (General tab), not buried in the description
