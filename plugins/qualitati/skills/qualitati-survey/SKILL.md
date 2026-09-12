---
name: qualitati-survey
description: Build and publish a conversational survey on QualiTaTi (create the survey under a project, add typed questions with optional AI follow-up probing, publish, return the share link). Use when the user wants a survey, questionnaire, poll, NPS, or "a survey with AI follow-ups".
version: 1.0.0
---

# Build a survey on QualiTaTi

Surveys live under a project. Tools: `survey_create`, `survey_add_question`, `survey_publish` (plus `project_list` / `project_create` for the parent project).

## 1. Pick the parent project

- `project_list` → use an existing project's numeric `id` when the survey belongs to a study the user already has.
- Otherwise `project_create` with a one-paragraph `outline` describing the survey's purpose (see the qualitati-interview-study skill for the fields), then use its `id`.

## 2. Create the survey

`survey_create(project_id, title, description="", language_default="en")` → returns the survey with its `id`. `language_default` is a two-letter code (`en`, `zh`, `fr`, `nl`, `nb`, `de`, `es`, `pt`, `ja`, `ar`).

## 3. Add questions, one call each

`survey_add_question(survey_id, prompt, type, variable_name, required, options, ai_follow_up_intensity)`

| `type` | needs `options`? | notes |
|---|---|---|
| `short_text`, `long_text` | no | open answers; pair with AI follow-up |
| `single_choice`, `multi_choice`, `dropdown`, `ranking`, `image_choice` | **yes** (list of strings) | |
| `rating_scale`, `nps`, `slider`, `bipolar_scale`, `number`, `yes_no`, `date`, `email`, `phone` | no | `nps` is 0–10 |
| `info_text`, `section_break`, `page_break` | no | layout only, not answered |
| `matrix`, `constant_sum`, `file_upload`, `ai_interview` | — | need rows/columns or settings the tool does not expose: build these in the web survey builder and tell the user |

- `variable_name`: snake_case identifier (letters, digits, underscore; not starting with a digit), e.g. `overall_satisfaction`. Give every scored question one; it becomes the column name in exports.
- `required`: default `true`; use `false` for sensitive or optional items.
- `ai_follow_up_intensity`: `none` (plain form), `light` (one clarifying probe when an answer is thin), `deep` (a short conversational follow-up). Use `light` or `deep` on open questions only; keep scales at `none`.

Design rules of thumb: 8–12 questions ≈ 10 minutes; start with easy, end with demographics; one idea per question; neutral wording; balanced scales with labelled endpoints.

## 4. Publish and hand over

`survey_publish(survey_id)` → returns the share token / link. Give the user the link. Responses appear in their QualiTaTi Surveys dashboard.

## Boundaries

- Confirm the question list with the user before adding it unless they asked you to just build it.
- Do not publish a survey the user has not seen the questions of.
- Do not fabricate response data; reading survey responses is not available through these tools yet — point the user to the dashboard.
