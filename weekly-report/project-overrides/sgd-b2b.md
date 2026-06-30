# Project Override — SGD B2B

Applies when the resolved project is **SGDB2B**. Apply these defaults without asking.

| Setting | Value |
|---|---|
| Template | Bundled default template in the `weekly-report` skill's `assets/` folder (`default-template.pptx`) — originally built for SGD B2B |
| Roadmap source | Default: PMAI Compact View screenshot. Use `High-level_Roadmap___Sports_Group_Denmark_B2B.xlsx` (most recent dated sheet) only if the user explicitly asks to use it for that request |
| Notion blockers | Search Notion for "Blockers" page scoped to SGD B2B |
| Slide sections | FE implementation / BE & Customizations / Integrations / Digital Marketing |
| Lane mapping | FE implementation / Frontend Styling & Customizations → FE implementation slide; BE, Extensions & Custom Modules Development + Customizations → BE, Customizations, Tailored features slide; Integrations (Agile scope) → Integrations slide; Digital Marketing Tools & Performance / SEO → Digital Marketing Tools & Performance slide |
| Brand colours | Navy #10132C (cover/closing bg), White #FFFFFF (content bg), Green #98FF98 (accent titles) |
| Font | Golos Text (fallback: Calibri) |
| Contact slide | iryna.rubanava@scandiweb.com / viktoriia.ozhozhenko@scandiweb.com — leave untouched |

## Adding a new project

Copy this file, rename to `<project-key-lowercase>.md`, and fill in:
- Template path (or note that the bundled SGD B2B template should be used as a generic base)
- Roadmap source default + override condition
- Notion blockers page location
- Slide sections present in that project's template
- Roadmap lane → slide section mapping
- Brand colours / font
- Any contact-slide or footer details that should be left untouched

The `weekly-report` skill resolves the project key, looks for `weekly-report/project-overrides/<key>.md` in this repo, and applies it if found. If no override file exists for a project, the skill falls back to deriving slide structure directly from that project's own uploaded template (see Step 0F/0G in the skill) plus the org-wide rules in `formatting-rules.md` — no override file is required to use the skill on a new project.
