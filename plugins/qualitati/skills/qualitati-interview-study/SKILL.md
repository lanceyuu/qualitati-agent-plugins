---
name: qualitati-interview-study
description: Set up an AI-moderated interview study on QualiTaTi (create the project, write the interview guide, get the participant link, check who has completed). Use when the user wants to run interviews, a qualitative study, user research conversations, or "an AI interviewer" for their participants.
version: 1.0.0
---

# Set up an interview study on QualiTaTi

The QualiTaTi MCP server (tools prefixed `project_`, `interview_`, `analysis_`) is connected. Studies are conducted by QualiTaTi's AI interviewer with **human participants** who open a share link. You set the study up and read the results; you never "run" an interview yourself.

## 1. Check before you create

Call `project_list` first. If a project with the same purpose exists, reuse it: ask the user rather than creating a duplicate. Creating a project is free; each interview a participant completes spends credits from the owner's account, so never create test projects or duplicate studies without being asked.

## 2. Collect what `project_create` needs

Ask only for what is missing; propose sensible defaults for the rest.

| Field | Values | Default / advice |
|---|---|---|
| `name` | short study title | e.g. "Onboarding friction — new users, Sept 2026" |
| `outline` | the interview guide (see §3) | write it yourself from the research question, then confirm |
| `interview_type` | `SEMI_STRUCTURED`, `STRUCTURED` | `SEMI_STRUCTURED` unless the user needs identical wording for every participant |
| `interview_language` | `ENGLISH`, `CHINESE`, `FRENCH`, `DUTCH`, `NORWEGIAN`, `GERMAN`, `SPANISH`, `PORTUGUESE`, `JAPANESE`, `ARABIC`, `FLEXIBLE` | match the participants; `FLEXIBLE` lets each participant choose |
| `interaction_mode` | `VOICE_TO_VOICE`, `VOICE_TO_TEXT`, `TEXT_TO_VOICE`, `TEXT_TO_TEXT` | `VOICE_TO_VOICE` for spoken interviews; `TEXT_TO_TEXT` for chat-style |
| `max_duration` | minutes | 20–30 for most studies (tool default 60) |
| `record_audio`, `record_video` | booleans | `false` unless the user explicitly wants recordings (consent implications) |
| `check_duplicate` | boolean | keep `true` |

## 3. Write a good outline

The outline is what the AI interviewer follows. Structure it as:

1. **Opening** — purpose in one sentence, reassurance, a warm-up question.
2. **Core topics** — 4–8 open questions, one idea each, neutral wording ("Tell me about the last time…", "What happened next?"). Under each, 1–3 probes the interviewer may use.
3. **Closing** — anything missed, thanks.

Avoid leading or double-barrelled questions and jargon. Keep it under ~400 words; the interviewer adapts the order in semi-structured mode.

## 4. Create and hand over

- Call `project_create(...)`. It returns `id`, `uuid`, `share_url` (the code) and **`share_link`** (the full participant URL).
- If `share_link` is empty, call `project_share(project_id=<id>)`; it returns `share_link` too.
- Give the user `share_link` and say plainly: send it to participants; the AI conducts the interview; results appear as participants finish.

## 5. Follow up

- Progress: `project_interviews(project_uuid, limit, offset)` lists interviews with `status`, `has_transcript`, `duration`, `analysis_ready` (an analysis is stored) and `quality_score`.
- Results: switch to the **qualitati-results** skill (`analysis_summary`, `analysis_run`, `analysis_results`, `analysis_digest`, `interview_get`).
- Existing transcripts from elsewhere: `interview_import(project_uuid, interviews=[{interviewee, conversation: [{role, content}, ...], duration_s}])` — idempotent when you pass `interview_uuid`.

## Boundaries

- Never invent results, quotes or participant counts. If nothing has been completed yet, say so.
- Do not create more than one project per request unless the user asked for several.
- If a tool returns an authentication error, the user's `QUALITATI_API_KEY` is missing or revoked: point them to https://qualitati.com/profile → API keys.
