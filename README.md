# CV Tailor — Claude Code Skill

> **Learning project / toy experiment.**
> This is not a production tool or a commercial product. It was built as a hands-on exercise in
> prompt engineering, skill design, and Claude Code's agent capabilities. Use it, fork it, break it,
> learn from it — that's the point.

---

## What it does

A Claude Code skill that turns your master career profile into a tailored CV + cover letter for a specific job, in one conversation. Three stages run automatically:

1. **Job analysis** — paste a URL or JD text; Claude classifies every requirement, maps it to your profile, flags gaps, scores fit
2. **CV tailoring** — rewrites your bullets using the RAP method, mirrors JD keywords for ATS, applies your formatting rules, outputs a `.docx` ready to send
3. **Cover letter** — picks the right hook type, writes four tight paragraphs, outputs a matching `.docx`

---

## Starting from zero — full setup guide

### Prerequisites

- [Claude Code](https://github.com/anthropics/claude-code) installed and running
- A Claude API key configured

---

### 1. Get the repo

Fork or clone:

```bash
git clone https://github.com/jrachmanas/cv-tailor.git
cd cv-tailor
```

Open the folder as a Claude Code project. `CLAUDE.md` auto-loads and tells Claude how to use the skill.

---

### 2. Customise the transformation rules (one-time, ~15 min)

Before building your profile, open `cv-tailor/references/transformation_rules.md` and adjust it for your context. This file is the design spec — Claude follows it when building your CV.

Key decisions to make:

| Rule | Default | When to change |
|---|---|---|
| Rule 1 — Resume type | Chronological | Switch to Combination if you're pivoting careers or skills need to lead |
| Rule 9 — Page length | 2 pages max | Reduce to 1 if you have < 5 years experience |
| Rule 5 — Skills style | Subcategory | Switch to Proven style for quota-driven roles (sales, BD); Creative style for design/visual roles |
| Rule 10 — ATS | Strict (no tables, no columns) | Relax slightly if targeting boutique firms that read CVs manually |

If you're in a field with different CV conventions (academia, government, creative) — read all 11 rules and update any that don't match your industry norms.

---

### 3. Add your existing CV (optional but recommended)

If you have an existing CV, drop it in the project folder. Any format works — `.pdf`, `.docx`, `.txt`.

Claude will read it in Stage 0 to pre-populate your master profile, so you answer questions about gaps and wins rather than typing your entire history from scratch.

```
cv-tailor/
your_cv.pdf        ← drop it here (gitignored by default if named *.pdf)
master_profile_template.md
...
```

> **Privacy note:** your CV and all outputs are gitignored. They stay on your machine and are never committed.

---

### 4. Build your master profile (one-time, ~30 min)

```bash
cp master_profile_template.md master_profile.md
```

Then in a Claude Code conversation:

```
Build my master profile
```

Claude runs a 5-phase interview:
1. **Identity** — confirms your name, location, target roles, target industries
2. **Personal brand** — 3 questions to extract your passion, unique approach, and career goal
3. **Win-mining** — goes role by role through your CV, asking for metrics, context, and stories behind each bullet
4. **Education & projects** — thesis, certifications, side projects, extracurriculars
5. **Gaps** — any date gaps or career transitions that need honest framing

Answers are saved to `master_profile.md` after each phase (crash-safe). This file is your single source of truth — every tailored CV is a projection of it, not a new document.

> **Tip:** Answer in detail. Vague answers produce vague CVs. The interview is the highest-leverage 30 minutes of this whole process.

---

### 5. Apply for a job (repeat for every application)

Paste a job URL or the full JD text:

```
Tailor my CV for this role: https://jobs.example.com/head-of-data
```

Claude runs all three stages and saves outputs to:

```
outputs/<company>_<YYYY-MM-DD>/
├── cv_<company>_<date>.md              # editable markdown source
├── cv_<company>_<date>.docx            # send this to the employer
├── cover_letter_<company>_<date>.md
├── cover_letter_<company>_<date>.docx  # send this to the employer
└── tailoring_notes_<company>_<date>.md # your private prep notes — never send
```

---

### 6. Update your profile over time

When you complete a project, get promoted, or want to add something new:

```
Add my Q2 platform launch to my profile
```

Claude asks targeted questions and updates only the relevant section. All future CVs automatically pick up the change.

---

## What's inside

```
cv-tailor/
├── SKILL.md                     # all skill logic — Stage 0, 1, 2, 3
├── references/
│   ├── transformation_rules.md  # your CV design spec — customise this first
│   ├── action_words.md          # 100+ action verbs by category
│   ├── ats_patterns.md          # ATS keyword mirroring rules
│   └── examples/
│       ├── good_brand_statements.md   # 5 annotated examples across industries
│       ├── rap_before_after.md        # 6 before/after bullet rewrites with notes
│       └── cover_letter_hooks.md      # 8 hook types with templates and examples
CLAUDE.md                        # auto-loaded project instructions
master_profile_template.md       # blank starting point — copy to master_profile.md
.gitignore                       # keeps your personal data off Git
```

---

## Key design decisions

- **Single source of truth.** `master_profile.md` holds everything once. Every tailored CV is a projection of it — not a rewrite from scratch each time.
- **Never invents facts.** Gaps are flagged in `tailoring_notes`, never papered over in the CV.
- **RAP method.** Every bullet: Result → Action → Problem. Quantified where possible, honest where not.
- **ATS-first output.** `.docx` format, Calibri font, no tables in body, no text boxes, no columns — survives any ATS parser.
- **Output versioned by company + date.** Apply to the same role twice? Old output stays, new one sits alongside it.

---

## Limitations (honest)

- Built and tested for one specific career context (data leadership / fintech). The rules and examples are tuned to that background. Adapt them to your field.
- The transformation rules are a synthesis of general CV advice — not a certified methodology. They're a starting point, not gospel.
- Stage 0 is interview-style. It works best when you answer honestly and in detail.
- This is a Claude Code skill, not a standalone app. You need Claude Code running locally with API access.
- Output quality depends on how complete your `master_profile.md` is and how well `transformation_rules.md` reflects your industry's conventions.

---

## Contributing

Fork it. Improve the rules. Adapt it for a different domain (design, academia, law, engineering). If you make something useful, a PR is welcome.

---

## License

MIT — do whatever you want with it.
