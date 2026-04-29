# CV Tailor — Claude Project Instructions

This project contains a CV tailoring skill for Claude Code.

## How it works

The skill lives in `cv-tailor/SKILL.md`. Claude reads it automatically in this project.

## Setup (first time)

1. Copy `master_profile_template.md` → rename to `master_profile.md` at the project root
2. Open a conversation in this project and say: **"Build my master profile"**
3. Claude will interview you (Stage 0) and fill `master_profile.md` — takes ~30 minutes once

## Day-to-day usage

Once your profile is built, every new job takes one conversation:

```
"Tailor my CV for this role: [paste URL or JD text]"
```

Claude runs Stage 1 (job analysis) → Stage 2 (CV tailoring) → Stage 3 (cover letter) in sequence.
Outputs land in `outputs/<company>_<date>/`.

## What you send to employers

| File | Purpose |
|---|---|
| `outputs/<company>_<date>/cv_<company>_<date>.docx` | Send this |
| `outputs/<company>_<date>/cover_letter_<company>_<date>.docx` | Send this |
| `outputs/<company>_<date>/tailoring_notes_<company>_<date>.md` | Keep for interview prep — never send |

## File layout

```
cv-tailor/
├── SKILL.md                          # skill logic (all 3 stages)
├── references/
│   ├── transformation_rules.md       # CV construction rules
│   ├── action_words.md               # verb bank by category
│   ├── ats_patterns.md               # ATS keyword mirroring rules
│   └── examples/
│       ├── good_brand_statements.md
│       ├── rap_before_after.md
│       └── cover_letter_hooks.md
master_profile.md                     # your data (gitignored — never committed)
master_profile_template.md            # blank starting point
outputs/                              # per-application outputs (gitignored)
```
