---
name: cv-tailor
description: >
  Triggers when the user mentions tailoring a CV or resume, applying for a job,
  writing a cover letter, or adapting their profile to a job description.
  Also triggers on: "tailor my CV", "tailor my resume", "job application",
  "cover letter for [company]", "apply for [role]", "update my master profile".
  Does NOT trigger on: proofreading grammar, formatting a document unrelated to
  a job application, or general career advice without a specific role.
---

# CV Tailor Skill

Transforms the user's master profile into a tailored CV + cover letter for a specific role.
Single source of truth: `master_profile.md`. All outputs versioned under `outputs/<company>_<YYYY-MM-DD>/`.

**Constraints (non-negotiable):**
- Never invent facts. If a qualification is missing, flag it — do not fabricate it.
- `[GAP]` items appear only in the gaps note to the user, never in the CV or cover letter body.
- CV and cover letter tone: professional and polished (reader is a hiring manager).
- Messages to the user: punchy, bulleted, short sections.
- Default resume type: Chronological. Switch only if the JD strongly warrants otherwise.

---

## Stage 0 — Profile Build

> Trigger: "build my master profile", "update my profile", "add [experience] to my profile"

### 0.1 Seed from existing CV

If the user has an existing CV (PDF or text), read it first to pre-populate `master_profile.md` with everything visible.
Then read `master_profile.md` to understand the current state before starting the interview.
Focus the interview on fields marked `[To be filled]` or `[To confirm]`.

### 0.2 Deep-dive interview (gap-filling + win-mining)

After extraction, run a 5-phase interview. **One topic cluster per message. Keep it short.**

**Phase 1 — Identity check**
Confirm extracted contact info and target roles/industries. Ask:
- "Does this look right? Anything to add or correct?"

**Phase 2 — Personal brand (3 questions)**
Ask these one at a time:
1. "What problem do you love solving most — the thing you'd work on even if no one paid you?"
2. "What do you do differently from most people in your field? What's your unusual approach?"
3. "What's the career outcome you're building toward in the next 3–5 years?"

After collecting all 3 answers, do a **brand alignment pass**:
- Review the user's target roles and industries from `master_profile.md` (or Phase 1 updates)
- Identify which parts of his raw answers map directly to what those roles value
- Flag any answers that need reframing to land with a hiring manager in that space
- Output a short **Brand Strength Map**:

  | Raw answer element | Maps to | Reframe suggestion |
  |--------------------|---------|-------------------|
  | e.g. "uncertainty into leverage" | Quant/trading: signal from noise | "Converts ambiguous data environments into durable decision infrastructure" |
  | ... | ... | ... |

- This table is for the user's reference only — not copied into the CV verbatim.
- Then draft a **2-sentence brand statement** using the strongest mapped elements.
  The user approves, adjusts, or rejects it before it gets written to `master_profile.md`.

**Phase 3 — Work experience RAP mining**

For each role on the CV, go through it with the user:
- Read out the existing bullets and challenge vague ones: "This says 'improved X' — what actually changed? By how much?"
- Ask the win-mining questions:
  - "What was broken or missing when you joined this role?"
  - "What was measurably different when you left (or now)?"
  - "What did you build, launch, or fix that you're most proud of?"
  - "Did you lead or mentor anyone? Formally or informally?"
  - "Any internal tools, processes, or systems you created that others used?"
  - "Any cross-team work, client impact, or revenue/cost numbers you can attach?"
- Propose framings: "That sounds like you saved roughly X hours a week — can we put a number on it?"
- Target: every bullet becomes a RAP statement with a quantified result (if it is quantifiable).

**Phase 4 — Education, projects, extras**
Ask about:
- Academic achievements, relevant coursework, thesis
- Side projects, open source, portfolio work
- Volunteer, clubs, extracurriculars with leadership or impact

**Phase 5 — Gaps & career transitions**
Ask about any date gaps. For each:
- "What were you doing during [gap period]? Freelance, caregiving, upskilling?"
- Capture honest framing for future use.

### 0.3 Write + completeness check

After each phase, **write partial results to `master_profile.md`** (crash-safe).

At the end, output a completeness summary table:

| Section | Status | Notes |
|---------|--------|-------|
| Contact info | ✅ complete | — |
| Personal brand | ✅ complete | — |
| Work experience | ⚠️ 2/4 roles quantified | Roles at [X], [Y] need metrics |
| Education | ✅ complete | — |
| Projects | ❌ missing | User to add |
| Gaps | ✅ accounted for | — |

---

## Stage 1 — Job Analysis

> Trigger: any URL or pasted JD text, "analyse this job", "apply for [role] at [company]"

### 1.1 Fetch & parse

Accept either:
- A URL → fetch and extract full JD text
- Pasted text → use as-is

Extract and record:
- Company name, role title, seniority level
- Location / remote policy
- Compensation (if listed)
- Date fetched (today's date)

### 1.2 ATS keyword classification

Load `references/ats_patterns.md`.

Classify every requirement and skill into three tiers:

**Tier 1 — Must-Have** (explicit: "required", "you must", "X+ years of", listed first)
- Hard skills / tools (languages, platforms, frameworks)
- Minimum experience thresholds
- Domain knowledge non-negotiables

**Tier 2 — Nice-to-Have** (explicit: "preferred", "bonus", "familiarity with", "ideally")
- Additional tools or platforms
- Domain adjacencies

**Tier 3 — Cultural / Values signals** (implicit: company values language, how they describe the team,
  adjectives used for the ideal candidate, mission statements)

### 1.3 Responsibility themes

Extract 3–5 dominant themes from the responsibilities section.
Label each: Strategic / Operational / Technical / People / Cross-functional

### 1.4 Match analysis vs master_profile.md

Read `master_profile.md`. For every Tier 1 and Tier 2 keyword:

| Keyword | Tier | In profile? | Evidence from profile |
|---------|------|----------------|-----------------------|
| dbt | 1 | ❌ GAP | Not in profile |
| Python | 1 | ✅ Strong | Multiple roles |
| ... | ... | ... | ... |

Status legend:
- ✅ Strong — explicitly in profile with context
- ⚠️ Partial — adjacent skill or implied but not stated
- ❌ GAP — missing entirely

### 1.5 Overall fit score + resume type recommendation

Calculate:
- Tier 1 match rate: X / Y Must-Haves covered
- Tier 2 match rate: X / Y Nice-to-Haves covered
- Fit band: Strong (≥80% T1) / Solid (60–79% T1) / Stretch (<60% T1)

Recommend resume type:
- Chronological → default (strong/solid fit, recent roles are relevant)
- Combination → if career pivot or if skills need to lead
- Functional → rarely; only if all experience is old or mis-titled

### 1.6 Gaps note to user

List every ❌ GAP item with a brief coaching note **and a gap category** (Hard / Soft / Adjacent). The category drives how Stage 3 handles the gap in tailoring notes:

- **Hard gap** — JD lists this as required (Tier 1) and the candidate has zero evidence. Stage 3 must produce the full three-part gap section (factual / honest framing / motivation + close-the-gap plan).
- **Soft gap** — JD lists this as preferred or bonus (Tier 2) and the candidate has zero evidence. Stage 3 three-part section recommended; first two parts only is acceptable if the motivation/plan would be redundant with another gap.
- **Adjacent gap** — candidate has adjacent but not identical experience (Tier 1 or Tier 2). Stage 3 must produce the full three-part section; the motivation element should explicitly bridge the adjacency.

Examples:
- "dbt — **[Adjacent]** — Airflow/Spark experience exists; mention any dbt exposure or flag for honest framing"
- "GCP/BigQuery — **[Hard]** — on-prem/Hadoop experience exists but no managed-cloud production; position as cloud-agnostic, note willingness to learn"
- "Corporate travel domain — **[Soft]** — bonus only in JD; zero direct exposure"

Flag if any Hard gap is a likely knock-out criterion (Tier 1, no adjacent skill at all — not even category-adjacent).

### 1.7 Write output file

Write to `outputs/<company>_<YYYY-MM-DD>/job_analysis_<company>_<YYYY-MM-DD>.md`

File structure:
```
# Job Analysis — <Company> — <Role Title>
Date: <YYYY-MM-DD>
Source: <URL or "pasted">

## Role snapshot
## ATS keywords (Tier 1 / 2 / 3)
## Responsibility themes
## Match analysis table
## Fit score
## Gaps & coaching notes (with Hard / Soft / Adjacent labels)
```

After writing, confirm: "Analysis saved. Ready for Stage 2 — shall I tailor the CV now?"

---

## Stage 2 — CV Tailoring

> Trigger: "tailor my CV", "tailor my resume", "write my CV for [company]", or immediately after Stage 1 with user approval

### 2.0 Load reference files

Before writing a single line of CV, load:
- `references/transformation_rules.md` — structural rules, section order, length limits
- `references/action_words.md` — verb bank by category
- `references/ats_patterns.md` — keyword mirroring rules
- `references/examples/good_brand_statements.md` — brand statement patterns
- `references/examples/rap_before_after.md` — bullet rewriting patterns
- `outputs/<company>_<date>/job_analysis_<company>_<date>.md` — Stage 1 output

### 2.1 Pre-writing decisions

Before drafting, make four explicit decisions and state them to the user:

**A. Resume type** (from Stage 1 fit score recommendation):
- Chronological → standard; section order: Brand Statement → Summary of Qualifications → Experience → Education → Skills
- Combination → section order: Brand Statement → Skills → Experience → Education
- Functional → section order: Brand Statement → Skills → Education → Experience (rarely used; only for major career changes or very limited experience)

**IMPORTANT:** In Chronological, Skills comes LAST — after Education. Experience is the primary asset and leads directly after the Summary of Qualifications.

**B. Role selection** — which roles to include:
- Always include: last 3–4 roles + any role with directly relevant impact
- Compress or omit: roles >12 years ago unless uniquely relevant
- Compress older/less-relevant roles into a 1-line "Earlier career" block unless the JD specifically signals their relevance

**C. Bullet budget per role:**
- Most recent / most relevant role: 4–5 bullets
- Second role: 3–4 bullets
- Third role: 2–3 bullets
- Fourth+ role: 1–2 bullets
- Earlier career block: 1 line total

**D. Keywords to surface** — list the Tier 1 ✅ and ⚠️ keywords that MUST appear naturally in the CV body.
  Do not keyword-stuff. Each keyword should appear in context, not as a bare list.

### 2.2 Header

Read from `master_profile.md` and render as:
```
[Full name]
[City, Country] · [Remote / On-site / Hybrid preference]
[email] · [phone]
[LinkedIn URL]
```

All values sourced from `master_profile.md` YAML front matter. Never hardcode contact details.

### 2.3 Personal Brand Statement

2–3 sentences. Structure:
1. **Who + what** — seniority, domain, years
2. **How** — distinctive approach (from brand block Phase 2)
3. **Where headed / what you bring to this specific role** — 1 sentence anchored to the JD's core need

Rules:
- Mirror at least 2 Tier 1 keywords from the JD naturally
- Do NOT use: "passionate about", "results-driven", "dynamic", "synergy"
- Do NOT open with "I"
- Max 60 words
- Load `references/examples/good_brand_statements.md` for tone reference
- Anchor to the core problem this specific role solves — read it from the job analysis

#### 2.3.1 Claim audit on the brand statement (hallucination guardrail)

Before emitting the brand statement, run a literal claim-by-claim audit. For every phrase, identify which section of `master_profile.md` backs it. If a phrase fails any of the three gates below, rewrite it using a stricter alternative drawn from the profile. **Do not soften with hedges ("approximately", "roughly") — replace.**

**1. Years gate.** Any "X+ years owning/leading/managing [scope]" claim must use the tenure of the role(s) where that scope was actually owned. Do not fold tenure across role types where the prior role did not own the claimed scope.
- ✅ Honest: "5+ years owning platform modernisation" when TPM tenure ≥ 5 years.
- ❌ Hallucination: "6+ years owning platform modernisation" by folding in a prior Data Engineer role that did not own modernisation.

**2. Title gate.** Title-level adjectives (Senior, Lead, Principal, Staff, Director, Head of, VP) in self-descriptions must either (a) match the candidate's formal title in `master_profile.md`, or (b) be omitted. Do not promote a title to mirror the JD's seniority framing. Describing scope directly is allowed — that is substance, not title inflation.
- ✅ Honest: "Technical product leader with company-level scope across a 300+ person organisation".
- ❌ Tilt: "Senior technical product leader" when the formal title is "Technical Product Manager".

**3. Portfolio gate.** Phrases that imply formal ownership of a product portfolio ("API roadmap", "platform roadmap", "integration roadmap", "product line", "product portfolio") must be backed by evidence the candidate owned such a portfolio with the rigor those words imply (versioning, KPIs, formal stakeholder ownership). If the candidate built a platform that has APIs in it but did not own an API-product portfolio, use descriptive phrasing instead.
- ✅ Honest: "API-driven platform capabilities", "platform exposing programmatic interfaces".
- ❌ Tilt: "API/integration roadmaps" when no such roadmap was formally owned.

After the audit, the brand statement should survive a literal read from a person who knows the candidate's history. If any phrase makes the candidate flinch on a literal-truth read, the audit failed — rewrite the phrase.

### 2.4 Skills section

Format: 3–4 grouped rows. Mirror JD language exactly for T1 keywords.

Groups (adapt to JD):
- **Data Engineering** — list tools; lead with JD-matched ones
- **Analytics & BI** — self-service, semantic layers, reporting
- **Leadership & Strategy** — team building, roadmap ownership, stakeholder management
- **AI / Emerging** — MCP, LLMs, AI adoption (if JD values it)

Rules:
- Skills must appear in master_profile.md or be provably implicit from experience
- Never list a skill the user doesn't have
- Suppress skills not relevant to this JD (don't list C#, ASP.NET for a data leadership role)
- For gaps: omit, do not substitute a vague synonym

### 2.5 Experience section — bullet rewriting

For each included role, rewrite bullets using the RAP → ATS pipeline:

**Step 1 — RAP check**: does each bullet have a Result, Action, and Problem implied?
  If Result is missing: flag it and use the best available framing ("Reduced… by X" or "Enabled… for Y users")

**Step 2 — ATS mirror**: does this bullet naturally contain any T1/T2 keyword from the JD?
  If yes: preserve the exact keyword phrasing from the JD (not a synonym)
  If no: check if the bullet can be reframed to surface one without distorting truth

**Step 3 — Verb selection**: load `references/action_words.md`
  Match verb category to the bullet type:
  - Building/shipping → Construction verbs
  - Improving/fixing → Improvement verbs
  - Leading/managing → Leadership verbs
  - Saving cost/time → Financial verbs
  - Analysing/reporting → Analysis verbs

**Step 4 — Length check**: each bullet ≤ 2 lines when rendered. Cut filler.

**Step 5 — Quantification check**: if a number exists in master_profile.md, it must appear.
  If no number: use relative framing ("across 12 jurisdictions", "for 60+ internal stakeholders")
  Never invent numbers.

**Role-specific bullet priority:**

For each included role, consult the job analysis to determine which JD themes it best supports. Lead with the 1–2 bullets that most directly map to the role's top responsibilities. Order remaining bullets by relevance to this JD, not chronologically.

### 2.6 Projects section

Include **Automated Trading System** if:
- JD is finance / fintech / quant / trading — always include
- JD is general data leadership — include with shorter framing

**Framing for finance/quant/trading roles:**
> **[Project name]** *(Personal — in progress)*
> Building from first principles: [brief description of what you're building]. Focus on data quality, reproducibility, and auditability — the same standards required in production financial data infrastructure.

**Framing for general data leadership roles:**
> **[Project name]** *(Personal — in progress)*
> [1-sentence description of the project and what skills it demonstrates — link to something the JD values].

Do NOT claim results or live trading performance. Honest framing only.

### 2.7 Education & certifications

Standard block. No changes needed for most applications.
Suppress Microsoft certifications if applying to cloud-native / open-source shops (irrelevant signal).

### 2.8 ATS keyword audit

Before finalising, run a mental audit:

For each Tier 1 ✅ keyword from Stage 1:
- [ ] Does it appear in the CV body (not just the skills list)?
- [ ] Is it in the same form as the JD used? (e.g., JD says "ETL/ELT" → CV should say "ETL/ELT", not just "pipelines")

For each ❌ GAP keyword:
- [ ] Is it absent from the CV? (good — do not fabricate presence)

### 2.9 Final checks before writing

- [ ] No invented facts
- [ ] All numbers sourced from master_profile.md
- [ ] Brand statement ≤ 60 words, no banned phrases
- [ ] **Brand statement passes the 2.3.1 claim audit (Years / Title / Portfolio gates)**
- [ ] Header shows correct location and remote preference from master_profile.md
- [ ] Total CV length: 2 pages max (aim for ~700–850 words of body copy)
- [ ] Gaps note written separately for the user (not in the CV)

### 2.10 Write output files

Write three files:

**1. Tailored CV (markdown source — editable):**
`outputs/<company>_<date>/cv_<company>_<date>.md`

**2. Tailored CV (Word document — send to employer):**
`outputs/<company>_<date>/cv_<company>_<date>.docx`

Use the `anthropic-skills:docx` skill to convert the `.md` to `.docx`.

Formatting spec for the docx:
- Name: 18pt bold, centered
- Contact line: 10pt, centered, separator · between items
- Section headers: 12pt bold, left-aligned, thin bottom border
- Body text: 11pt, left-aligned
- Bullets: standard bullet list, 10pt, 0.3cm hanging indent
- Margins: 1.9cm top/bottom, 2.0cm left/right (fits 2 pages comfortably)
- Font: Calibri throughout (ATS-safe, professional)
- Colour: one accent — dark navy (#1F3864) for section headers only
- No tables in body content (ATS-hostile). Skills section: plain text rows, not a table.
- No text boxes, no headers/footers for key content, no columns

**3. Tailoring notes (for the user only — never sent to employer):**
`outputs/<company>_<date>/tailoring_notes_<company>_<date>.md`

Contents of tailoring notes:
- Pre-writing decisions made (A–D)
- ATS keyword audit results
- **Gap coverage section** (see template below — replaces the older "honest framing suggestions" sub-section)
- Anything the user should be ready to discuss in an interview that the CV signals but doesn't fully explain

#### Gap coverage template (three-part offensive structure)

For every gap surfaced in Stage 1, produce a block in the tailoring notes using the template below. Every **Hard** and **Adjacent** gap receives the full three-part treatment. **Soft** gaps may receive the first two parts only if the motivation/plan would be redundant with another gap covered in full.

```
### Gap N — <one-line label> [Hard | Soft | Adjacent]

**The gap (factual):**
What's missing from the candidate's profile relative to the JD requirement.
One or two sentences. Neutral tone, no defence yet.

**Honest framing (defensive script for the interview):**
How to acknowledge the gap when it's raised. A 30-second script the candidate
can deliver without being evasive or oversold.

**Motivation + close-the-gap plan (offensive narrative):**
- Why the candidate wants to close this gap — the genuine motivation, drawn from
  master_profile.md (career direction, brand block, project signals). Not a posture.
- The concrete plan — first-60-days reading list, weekly cadence, ramp project,
  who to shadow, what to ship as the first proof. Scoped, not generic.
- Evidence from history that the candidate can close gaps of this shape at speed —
  at least one cross-reference to a prior fast-ramp moment in master_profile.md.
```

**Why three parts and not two:** the first two parts give the candidate a defensive script for when a gap is raised. The third part gives an offensive narrative — the answer to "why are you the right hire even with this gap?" rather than just "how do you respond when this gap is raised?". Senior roles in particular require the second posture; without it the candidate looks like a junior-with-experience instead of a senior-with-trajectory.

**Source rule:** the motivation element must cross-reference `master_profile.md` directly (career-direction goal, brand block, prior fast-ramp project). Do not invent motivation. If the profile lacks a clear signal for why the candidate wants to close a specific gap, leave the motivation element blank and flag it to the user instead of fabricating it.

After writing all three, confirm:
> "CV saved as .md and .docx. Here's what I want you to check before we move to the cover letter: [3–5 specific things to review carefully]"

---

## Stage 3 — Cover Letter

> Trigger: "write a cover letter", "cover letter for [company]", or immediately after Stage 2 with user approval

### 3.0 Load reference files

Before writing a single line, load:
- `references/examples/cover_letter_hooks.md` — 8 hook types with examples; never open without consulting this
- `outputs/<company>_<date>/job_analysis_<company>_<date>.md` — Stage 1 output (company pain points, culture signals)
- `master_profile.md` — Personal Brand block (passion, unique approach, goal)

### 3.1 Hook selection

Choose the best hook type from `cover_letter_hooks.md` based on what you know about the company:

| Signal in JD / company | Best hook type |
|---|---|
| Clear mission / values language | Type 1 — Mission/Values |
| One result that directly mirrors the role | Type 2 — Specific Achievement |
| Trading firm / quant fund / financial markets | Type 3 — Industry Insight |
| Company in growth/transformation phase | Type 4 — Problem-You-Solve |
| User has a contrarian take on the conventional approach | Type 5 — Contrarian/Reframe |
| Referred by someone at the company | Type 6 — Mutual Connection |
| Small firm, user has done specific research on the company | Type 7 — Research Signal |
| User making a deliberate career arc toward this role type | Type 8 — Trajectory |

**Rule:** The hook must be ≤ 2 sentences. If it takes 3 sentences to land, rewrite it.
**Never open with:** "I am writing to apply...", "I am a passionate...", "I was excited to see...", "I believe I am a great fit..."

### 3.2 Letter structure

Four paragraphs + close. Total target: 300–400 words (one tight page).

**Paragraph 1 — Hook + stakes** (2–3 sentences)
The hook. Then one sentence that names the core problem this role solves and signals the user understands it at depth.

**Paragraph 2 — The three things that matter most for this role** (4–5 sentences)
Pick the 2–3 responsibilities from the JD that are most critical. For each, name the specific proof from the user's experience — with a number where possible. Do not list everything — choose the most relevant.

**Paragraph 3 — Specific proof that maps to their situation** (3–4 sentences)
One or two deeper examples. This is where the most specific and memorable writing goes. The goal: make the hiring manager think "only someone who has actually done this could write that."

**Paragraph 4 — Why this company, why now** (2–3 sentences)
Company-specific. Reference something real: their values if they resonate with the user's actual behaviour, a product/infrastructure decision they've made, or the user's deliberate career arc toward this type of role. Never generic enthusiasm.

**Close** (1 sentence)
Confident. Propose a specific next step: "I'd like to discuss how I would approach the first 90 days."
Never: "I look forward to hearing from you", "Thank you for your consideration", "Please find my CV attached."

### 3.3 Tone rules

- Write in the user's voice: direct, precise, slightly formal but not stiff.
- No adjectives without proof: not "passionate", "dedicated", "results-driven" unless followed immediately by a number.
- No throat-clearing: cut the first sentence of any paragraph if it could be skipped without losing meaning.
- State facts; let facts do the persuading. Never claim fit — demonstrate it.
- One strong idea per paragraph. No rambling.

### 3.4 Final checks before writing

- [ ] Hook ≤ 2 sentences, no banned openers
- [ ] Total length 300–400 words
- [ ] At least 3 specific numbers from the user's experience
- [ ] Paragraph 4 is company-specific (cannot be copy-pasted to another letter)
- [ ] Close proposes a concrete next step
- [ ] No tables, no bullet lists in the letter body (prose only)

### 3.5 Write output files

**1. Cover letter (markdown source):**
`outputs/<company>_<date>/cover_letter_<company>_<date>.md`

**2. Cover letter (Word document — send to employer):**
`outputs/<company>_<date>/cover_letter_<company>_<date>.docx`

Formatting spec for cover letter docx:
- Same header as CV (name bold, contact line below — but full size, not small)
- Date: left-aligned, 11pt, below header
- Salutation: "Dear Hiring Manager," (or specific name if known)
- Body: 11pt Calibri, 1.5 line spacing, left-aligned, no indent on first line of paragraphs
- Paragraph spacing: 6pt after each paragraph
- Close: user's full name bold (from master_profile.md), no "Sincerely" or "Best regards" — just the name
- Margins: match CV (1.9cm top/bottom, 2.0cm left/right)
- One page only — if over one page, cut

After writing both files, confirm:
> "Cover letter saved. Three things to personalise before sending: [specific items that need local knowledge — e.g., referral name, specific product research, salary expectations if mentioned]"

---

## Reference files (load when needed)

- `references/transformation_rules.md` — load before Stage 2
- `references/action_words.md` — load during bullet rewriting in Stage 2
- `references/ats_patterns.md` — load during Stage 1 keyword extraction and Stage 2 summary writing
- `references/examples/good_brand_statements.md` — load when writing Personal Brand Statement
- `references/examples/rap_before_after.md` — load when rewriting experience bullets
- `references/examples/cover_letter_hooks.md` — load during Stage 3
