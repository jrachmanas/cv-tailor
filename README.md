# CV Tailor — Claude Code Skill

> **Learning project / toy experiment.**
> This is not a production tool or a commercial product. It was built as a hands-on exercise in
> prompt engineering, skill design, and Claude Code's agent capabilities. Use it, fork it, break it,
> learn from it — that's the point.

---

## What it does

A Claude Code skill that turns your master career profile into a tailored CV + cover letter for a specific job, in one conversation. Three stages:

1. **Job analysis** — paste a URL or JD text; Claude classifies every requirement, maps it to your profile, flags gaps, scores fit
2. **CV tailoring** — rewrites your bullets using the RAP method, mirrors JD keywords for ATS, applies formatting rules, outputs a `.docx` ready to send
3. **Cover letter** — picks the right hook type, writes four tight paragraphs, outputs a matching `.docx`

---

## How to use it

### Prerequisites

- [Claude Code](https://github.com/anthropics/claude-code) installed
- A Claude API key configured

### Setup

```bash
git clone https://github.com/<your-username>/cv-tailor.git
cd cv-tailor
```

Open the folder as a Claude Code project (it will auto-load `CLAUDE.md`).

### Step 1 — Build your master profile (once)

```
cp master_profile_template.md master_profile.md
```

Then in a Claude Code conversation:

```
Build my master profile
```

Claude interviews you across 5 phases (~30 min). Your answers get saved to `master_profile.md` — the single source of truth for everything that follows. This file is gitignored and never leaves your machine.

### Step 2 — Apply for a job

Paste a job URL or JD text:

```
Tailor my CV for this role: https://jobs.example.com/head-of-data
```

Claude runs all three stages automatically. Outputs land in:

```
outputs/<company>_<YYYY-MM-DD>/
├── cv_<company>_<date>.md           # editable source
├── cv_<company>_<date>.docx         # send this to the employer
├── cover_letter_<company>_<date>.md
├── cover_letter_<company>_<date>.docx
└── tailoring_notes_<company>_<date>.md  # your private prep notes
```

### Step 3 — Update your profile over time

```
Add my Q2 platform launch to my profile
```

Claude asks targeted questions and updates only the relevant section. All future CVs pick up the change.

---

## Customise for your context

Before running Stage 2 for the first time, open `cv-tailor/references/transformation_rules.md` and decide:

- **Which resume type is your default?** The file defaults to Chronological for experienced candidates. If you're a recent graduate or making a career pivot, Combination may serve you better — update Rule 1 accordingly.
- **What does your ideal CV look like?** The rules cover section order, bullet format, skills style, length, and visual hierarchy. Read through them and adjust anything that doesn't match the conventions of your industry (e.g., creative fields, academia, or government roles have different norms).
- **What's your ATS context?** Rule 10 assumes most employers use ATS. If you're targeting small companies or roles where CVs are read by humans first, you can be slightly more expressive with formatting.

The rules file is your design spec. The skill follows it — so the output quality is directly tied to how well the rules reflect what a great CV looks like in your specific field.

---

## What's inside

```
cv-tailor/
├── SKILL.md                     # all skill logic — Stage 0, 1, 2, 3
├── references/
│   ├── transformation_rules.md  # CV construction rules (RAP, ATS, formatting)
│   ├── action_words.md          # 100+ action verbs by category
│   ├── ats_patterns.md          # how to mirror JD language without stuffing
│   └── examples/
│       ├── good_brand_statements.md
│       ├── rap_before_after.md
│       └── cover_letter_hooks.md
CLAUDE.md                        # auto-loaded project instructions
master_profile_template.md       # blank starting point — copy to master_profile.md
.gitignore                       # keeps your personal data off Git
```

---

## Key design decisions

- **Single source of truth.** `master_profile.md` holds everything once. Every tailored CV is a projection of it, not a new document.
- **Never invents facts.** Gaps are flagged in `tailoring_notes`, never papered over in the CV.
- **RAP method.** Every bullet: Result → Action → Problem. Quantified where possible, honest where not.
- **ATS-first output.** `.docx` format, Calibri font, no tables in body, no text boxes, no columns — survives any ATS parser.
- **Output versioned by company + date.** Apply to the same role twice? Old output stays, new one sits alongside it.

---

## Limitations (honest)

- Built and tested for one person's specific career (data leadership / fintech). Prompts and rules are tuned to that context. Your mileage will vary.
- The transformation rules are a synthesis of general CV advice — not a certified methodology. Treat them as a starting point, not gospel.
- Stage 0 (profile build) is interview-style. It works best when you answer honestly and in detail. Vague answers produce vague CVs.
- This is a Claude Code skill, not a standalone app. You need Claude Code running locally with API access.

---

## Contributing

Fork it. Improve the rules. Try it for a different domain or seniority level. If you make something useful, a PR is welcome.

---

## License

MIT — do whatever you want with it.
