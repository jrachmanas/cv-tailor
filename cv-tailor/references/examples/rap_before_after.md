# RAP Before & After Examples
*Reference for Stage 2 bullet rewriting. Each pair shows the transformation from duty-statement to accomplishment statement using the Result-Action-Problem method.*
*Pull the pattern — adapt to your own experience.*

---

## Pair 1 — Knowledge Management / Internal Tooling

**Before (duty-focused):**
> Responsible for managing the internal knowledge base for the engineering organisation.

**After (RAP):**
> Reduced support ticket volume by 60% by rebuilding the internal knowledge base from scratch — consolidating 4 years of scattered wikis into a single searchable platform and enforcing documentation as a definition-of-done requirement across all squads.

**Transformation notes:**
- Result leads: 60% ticket reduction — measurable, meaningful to any engineering manager
- Action is specific: rebuilt from scratch, consolidated scattered sources, enforced as a process requirement
- Problem embedded: "4 years of scattered wikis" — context without padding
- Removed: "Responsible for managing" — replaced with the outcome

---

## Pair 2 — Data Infrastructure Build

**Before (duty-focused):**
> Worked on data pipelines for the e-commerce platform, including ETL jobs and reporting.

**After (RAP):**
> Engineered 40+ ETL pipelines — covering order processing, inventory sync, and customer behaviour reporting — that served 80+ internal stakeholders across product, finance, and marketing, building the entire data foundation from scratch as the sole data engineer during a 12-month hypergrowth phase.

**Transformation notes:**
- Result leads: scale (40+ pipelines, 80+ users) does the work — no inflated language needed
- Action is specific: what was built, for whom, under what constraint (sole engineer, hypergrowth)
- Problem implied: built from scratch — signals no legacy to build on
- Removed: "Worked on" — replaced with "Engineered"

---

## Pair 3 — Team Building / Leadership

**Before (duty-focused):**
> Managed and grew the product engineering team.

**After (RAP):**
> Scaled the product engineering team from 3 to 20 in 18 months — with only 1 departure across all 18 hires (94% retention) — by hiring for complementary strengths rather than uniform seniority and developing 3 internal leads, each promoted to senior roles before the team reorganised.

**Transformation notes:**
- Result leads: 3→20 AND 94% retention — two numbers doing double duty (size + quality)
- Action is specific: the hiring philosophy, the development approach, the outcome for those developed
- Problem implied: rapid scaling mandate with sustainability risk
- "Before the team reorganised" — shows the systems outlasted the builder

---

## Pair 4 — Compliance / Regulated Data

**Before (duty-focused):**
> Responsible for financial data pipelines across multiple jurisdictions.

**After (RAP):**
> Owned the financial reporting and transaction data pipelines across 8 regulated EU markets — where a data error carried regulatory consequence, not just a bug ticket — ensuring audit-ready accuracy under GDPR and PSD2 using a stack of Python, Airflow, and dbt.

**Transformation notes:**
- Result implied: pipelines that sustained compliance across 8 markets with no regulatory incidents
- Action is specific: owned end-to-end, named the stack, named the regulatory context
- Problem embedded: "regulatory consequence, not just a bug ticket" — reframes the stakes without exaggerating
- Stack named inline: readable while surfacing ATS keywords

---

## Pair 5 — Process Automation

**Before (duty-focused):**
> Helped automate manual reporting processes for the operations team.

**After (RAP):**
> Eliminated 120 hours of monthly manual reporting by automating 14 recurring operational reports — freeing 3 FTEs to shift from data entry to analysis — by building a Python-based reporting layer on top of the existing data warehouse with no infrastructure changes required.

**Transformation notes:**
- Result leads: 120 hours/month is concrete; the FTE shift is the strategic implication
- Action is specific: 14 reports, the approach (Python layer, no new infra), the constraint respected
- Problem implied: manual process consuming analyst capacity that could be doing higher-value work
- Removed: "Helped automate" — replaced with full ownership

---

## Pair 6 — Root Cause / Technical Problem-Solving

**Before (duty-focused):**
> Fixed a bug in the reporting pipeline that was causing issues for the finance team.

**After (RAP):**
> Permanently resolved a stale-data issue in the month-end financial close report — where figures were being calculated before the source tables had finished updating — by redesigning the pipeline's dependency ordering to enforce strict sequencing, and established this as the standard pattern for all subsequent batch jobs.

**Transformation notes:**
- Result leads: "permanently resolved" + "established as standard pattern" — two outcomes for one fix
- Action is specific: the root cause diagnosis (dependency ordering), the fix, and the generalisation
- Problem is precise: "figures calculated before source tables finished updating" — specific enough that any data engineer understands the severity
- "Established as standard pattern" shows systemic thinking — turned a one-time fix into an org-wide improvement

---

## The Transformation Checklist

Before finalising any bullet, check:

- [ ] Does it start with a strong action verb? (not "Responsible for", "Worked on", "Helped")
- [ ] Does it lead with the result or impact?
- [ ] Is there at least one specific number, scale, or metric? (if quantifiable)
- [ ] Is the action specific enough that a different person couldn't claim the same bullet?
- [ ] Is the problem or context clear without over-explaining?
- [ ] Is it one sentence (or max two tight sentences)?
