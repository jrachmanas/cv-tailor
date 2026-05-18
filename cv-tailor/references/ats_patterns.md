# ATS Patterns
*Rules for passing Applicant Tracking Systems and mirroring job description language honestly.*

---

## What ATS Actually Scans For

1. **Keyword matches** — exact phrases from the JD, especially in the Skills and Summary sections.
2. **Standard section headings** — "Work Experience", "Education", "Skills". Non-standard names ("My Journey", "What I've Done") may not parse correctly.
3. **Clean, linear text** — ATS parsers read text sequentially. Anything that breaks linear flow causes misparse or data loss.
4. **File format** — `.docx` is safest for ATS. `.pdf` can fail on some older parsers. Submit `.docx` unless the employer specifically requests `.pdf`.

---

## ATS-Hostile Formatting (Never Use)

| Element | Why it breaks ATS | Alternative |
|---------|------------------|-------------|
| Text boxes | Content is invisible to parser | Use plain paragraphs |
| Tables in body content | Column order gets scrambled | Use plain bullet lists |
| Multi-column layouts | Columns read left-to-right across both = garbled | Single column body |
| Headers/footers for key content | Often stripped entirely | Put content in body |
| Images, logos, icons | Ignored completely | Remove from body |
| Embedded fonts / special characters | May render as junk characters | Stick to standard fonts |
| Fancy bullet symbols (→ ◆ ▸) | May not parse | Use standard • or - |

**Note:** Visual design elements are fine in a document you hand to a human. For ATS submission, prioritise clean parse over visual flair.

---

## Keyword Mirroring — The Honest Method

**Rule:** Use the JD's exact phrasing when it genuinely applies to the candidate's experience. Not synonyms — the exact phrase.

**Why:** ATS scores keyword frequency and exact matches. "Cross-functional stakeholder management" and "managing stakeholders across teams" are not equivalent to the parser.

**Honesty constraint:** Only mirror a phrase if the candidate actually did that thing. ATS passes get followed by human review and interviews. Fabricated keywords create mismatches that fail at the next stage.

**Process:**
1. Extract all keywords from the JD (see Stage 1 extraction workflow below)
2. For each keyword: does the candidate's `master_profile.md` contain evidence of this?
   - Yes → mirror the exact phrase in the appropriate section (Summary, Skills, or bullet)
   - No → mark as `[GAP]`, do not insert into the CV
   - Adjacent experience exists → flag for honest framing suggestion

---

## Stage 1 — Keyword Extraction Workflow

When a JD is pasted, extract keywords in three passes:

### Pass 1 — Required Skills & Qualifications
Scan the "Requirements", "Qualifications", or "Must Have" section of the JD.
Classify each item:

| Category | Description | ATS weight |
|----------|-------------|-----------|
| **Must-Have** | Explicitly required — "must have", "required", "essential" | Highest — missing these = auto-reject |
| **Nice-to-Have** | Listed as preferred, bonus, or "would be great" | Medium — worth mirroring if the candidate has it |
| **Cultural** | Values, ways of working, soft-skill phrases in the company description | Low for ATS, high for cover letter hook |

### Pass 2 — Tools & Technologies
List every named tool, platform, language, or framework.
Classify: **Has** (in master_profile.md) / **Partial** (adjacent experience) / **Missing** `[GAP]`.

### Pass 3 — Implicit Keywords
Phrases used in the responsibilities section that signal what they value, even if not in the requirements list.
Example: if the role description says "partnering with quant researchers" six times, "quant research collaboration" is an implicit keyword worth including if honest.

---

## Gap Flagging Protocol

When a Must-Have keyword has no match in `master_profile.md`:

1. Mark it `[GAP: <keyword>]` in the job analysis file.
2. Check for **adjacent experience** — something the candidate did that partially covers the gap.
3. If adjacent experience exists, propose an **honest framing**:
   - Example: JD wants "options pricing models" — the candidate has no direct options experience, but has built quantitative modelling tools and studied derivatives theory.
   - Honest framing: "Built systematic trading infrastructure with direct exposure to risk modelling and factor strategies — no production options pricing, flagged."
4. If no adjacent experience exists: flag as a hard gap. The user decides whether to apply.
5. `[GAP]` items go in the **Gaps & Honest Framings** note to the candidate only. They NEVER appear in the CV body or cover letter.

### Gap categorisation (drives Stage 3 treatment)

Every gap surfaced in Stage 1 must also be labelled with one of three categories. The label tells Stage 3 (tailoring notes) how much offensive narrative the gap needs alongside the defensive honest-framing script. See `SKILL.md` § 2.10 ("Gap coverage template") for the three-part block this drives.

- **Hard** — JD lists this as required (Tier 1) and the candidate has zero evidence. Full three-part block mandatory in tailoring notes.
- **Soft** — JD lists this as preferred or bonus (Tier 2) and the candidate has zero evidence. Three-part block recommended; first two parts only is acceptable if the motivation/plan would be redundant.
- **Adjacent** — candidate has adjacent but not identical experience. Full three-part block mandatory; the motivation element must explicitly bridge the adjacency.

The job-analysis output file (`outputs/<company>_<date>/job_analysis_<company>_<date>.md`) must surface the label inline with each gap so the tailoring-notes step can act on it without re-deriving the category.

---

## Keyword Placement by Section

| Section | What to mirror | How |
|---------|---------------|-----|
| Summary of Qualifications | Top 4–6 Must-Have keywords | Integrate naturally into each bullet |
| Skills (Subcategory style) | All confirmed tools/technologies | Use exact names (e.g. "Apache Airflow" not just "Airflow" if JD uses the full name) |
| Experience bullets | Specific phrases from responsibilities section | Weave into RAP bullets where genuine |
| Personal Brand Statement | Role-level framing language | Mirror the seniority language ("data leader", "Head of Data", "strategic") |

---

## Example Keyword Clusters by Role Type

*Adapt these to your target roles. These are illustrative — always extract actual keywords from the specific JD using the extraction workflow above.*

**Data / Engineering leadership:**
data strategy, data governance, data platform, data architecture, cross-functional, stakeholder management, roadmap ownership, data-driven decision making, ETL, ELT, data quality, data observability, team scaling

**Marketing / Growth:**
growth strategy, acquisition, conversion optimisation, A/B testing, attribution modelling, campaign analytics, pipeline generation, ARR, CAC, LTV, marketing automation

**People / Operations:**
workforce planning, organisational design, HRBP, talent development, performance management, operational efficiency, process improvement, change management, OKRs
