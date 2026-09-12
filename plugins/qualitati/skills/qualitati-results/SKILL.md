---
name: qualitati-results
description: Read and summarise results of a QualiTaTi interview study (completion status, per-interview quality and coding analysis, digests, full transcripts) and write an evidence-backed theme summary. Use when the user asks what came out of their interviews, wants themes, findings, quotes, a results summary, or whether analysis has run.
version: 1.0.0
---

# Read results from QualiTaTi

Tools: `project_list`, `project_interviews`, `analysis_summary`, `analysis_results`, `analysis_run`, `analysis_digest`, `interview_get`.

## 1. Locate the study

`project_list` → match the user's description to a project; keep both its `uuid` (most tools) and `id`. If several match, ask.

## 2. See what exists

- `project_interviews(project_uuid)` → each interview's `uuid`, `status`, `has_transcript`, `duration`, `ai_coding_status`, `quality_score`.
- `analysis_summary(project_uuid)` → total vs coded counts.
Report the state honestly: e.g. "7 interviews, 5 completed, 3 analysed".

## 3. Get the analysis

For each **completed** interview:
- `analysis_results(interview_uuid)` → stored quality + coding (themes/codes). If it says nothing has been computed yet:
  - `analysis_run(interview_uuid, mode="complete")` (quality + coding) or `mode="coding"` (themes only). This spends the owner's credits and is rate-limited (~5 runs/minute): ask before running it on more than a handful, and never re-run what already exists.
- `analysis_digest(interview_uuid)` → keywords / summary / insight, when stored.
- `interview_get(interview_uuid)` → metadata + the full conversation, for verbatim quotes.

## 4. Write the summary

- Themes ordered by how many interviews they appear in, each with: what it is, how many interviews, 1–2 **verbatim** quotes tagged with the interview (order id or name).
- Separate description ("participants said…") from interpretation ("this suggests…").
- State the sample size and what was not analysed. Flag low-quality interviews (`quality_score`) rather than silently including them.
- Never invent quotes, counts or themes; if the analysis is missing, offer to run it.

## Formats

Plain prose with short headings for chat; a table of themes × interviews when the user wants to compare; CSV only on request.
