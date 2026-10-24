---
name: clarify-to-plan
description: "Clarify a task or software/product idea through questions, then write one complete implementation plan for a new AI session after the user confirms readiness. Use when the user invokes Clarify to Plan, asks to be interviewed before planning, or wants a plan that preserves all their choices without guessed requirements. This is a planning-only workflow: no implementation, PRD, separate design document, or automatic handoff to a building skill."
---

# Clarify to Plan

Turn the user's idea into a plan another AI can execute without inventing what the user wants.

Follow this sequence: **ask questions until the task is clear → ask whether the user is ready for the plan → write one self-contained plan only after confirmation**.

## Working rules

- During clarification, respond with questions only. Do not produce recommendations, draft plans, intermediate summaries, PRDs, design documents, code, or progress reports. Follow higher-priority host requirements when they require a notice.
- Do not narrate the workflow or preface questions with explanations of what the skill requires. Treat readiness as part of the user's requested conversation. If the host requires naming the skill and explaining the checkpoint, incorporate that explanation into the question itself instead of adding a separate statement.
- Ask one focused question per turn by default. Ask several together only if the user requests that style.
- Set no artificial question limit. Continue until the relevant requirements and choices are understood.
- Do not guess, silently select defaults, or treat common practice as the user's preference.
- Do not build anything. The deliverable is a plan to paste into a new session.
- Use the user's language and plain terms. Keep the question specific to their task.
- When the host provides a structured question tool, ask through it so the user can select an answer (see "How to ask"). Otherwise ask in plain text. Do not depend on any plugin, repository, or other skill.

## How to ask

Prefer the host's structured question tool whenever the question has a small set of distinct answers. The user then picks an option instead of typing. Both tools below also add a free-text "Other" choice automatically, so never add your own "Other" option.

- **Claude Code:** call `AskUserQuestion`. Put one question in `questions`, with a `header` chip of up to 12 characters, a full `question` that ends with a question mark, and 2–4 `options`. Give each option a short `label` (1–5 words) and a `description` that explains the consequence of choosing it. Set `multiSelect: true` only when the user may choose several non-exclusive answers.
- **Codex CLI:** call `request_user_input`. Put one question in `questions`, with a snake_case `id`, a `header` of up to 12 characters, a `question`, and 2–3 mutually exclusive `options`, each with a `label` and a `description`. The tool accepts one answer per question, so when several answers may apply, ask in plain text instead. The tool works only in Plan mode unless the user has enabled the `default_mode_request_user_input` feature. If it is unavailable or returns an error, do not retry it; ask in plain text for the rest of the session.
- **Any other host, or when no tool is available:** ask in plain text. List the options as a numbered list, each with a one-line explanation, and say that the user may reply with a number or with their own answer.

Rules for options:

- Keep options neutral and mutually exclusive, and list them in an order that does not imply a preference. Do not mark an option as "Recommended" unless the user asked for your recommendation. Then mark only that one option, and treat the user's selection of it as their confirmation.
- Cover the realistic answers. If the plausible answers do not fit in the tool's option limit, or if the answer is inherently free-form (for example a name, a number, a URL, a description, or pasted content), ask in plain text instead of forcing artificial options.
- Record the selected option's meaning, plus any text the user adds in "Other" or notes, exactly as an answer they typed would be recorded. Selecting an option is an explicit choice. A tool timeout, dismissal, or empty answer is not a choice, so ask again or wait.
- Calling the question tool satisfies the "questions only" rule. Do not add surrounding commentary to the call.

## 1. Understand the request and track decisions

Read the task, relevant conversation, and any supplied material you can access before asking. Use existing answers instead of asking the user to repeat them. If needed context is unavailable, ask for it; never pretend to have read it.

Maintain an internal decision record with:

- Confirmed requirements and the user's exact choices.
- Explicit exclusions and things the user does not want.
- Constraints, dependencies, priorities, and agreed success conditions.
- Open questions, contradictions, and items explicitly deferred by the user.
- Changes to earlier answers, keeping the latest explicit choice authoritative.

Record user-provided filenames, labels, numbers, formats, technology choices, and ordering accurately. Treat supplied context as evidence, not permission to invent missing requirements. Do not show a separate decision document during the interview.

## 2. Ask until the consequential gaps are resolved

Choose the next question whose answer most affects what will be delivered. Build on the user's last answer and the context already supplied.

Use this coverage guide internally; do not turn it into a generic questionnaire:

- What task should be completed, why, and for whom?
- What is included, excluded, or explicitly forbidden?
- What should the result look like and how should it behave?
- What inputs, outputs, user flows, states, and edge cases matter?
- What already exists, and what must remain unchanged?
- What platform, stack, tools, data, integrations, or delivery format are required?
- What design, content, accessibility, privacy, or permission choices affect this task?
- What limits, dependencies, priorities, and acceptance conditions apply?

Ask only about relevant gaps. Do not demand decisions about unrelated or inconsequential details. However, do not dismiss a detail as inconsequential if choosing it would change behavior, appearance the user cares about, scope, cost, technology constraints, or delivery.

For an ambiguous answer, ask a narrower follow-up. For conflicting answers, ask which should govern. Do not resolve conflicts yourself.

If the user is unsure, offer neutral choices as selectable options, with brief explanations, and allow another answer. If they explicitly ask for your recommendation, frame it as a proposed choice and ask them to confirm it. A recommendation is not a confirmed requirement.

If the user says "you choose," ask them to confirm the concrete choice you propose before recording it as settled. For example, offer it as an option to accept beside the alternatives. Never turn delegation into an undisclosed assumption. If they explicitly defer a decision, record the deferral and require the implementing AI to ask before the affected work; do not fill it in yourself.

Do not turn an early "make the plan" into permission to guess unresolved requirements. Ask the remaining necessary questions first.

## 3. Ask the readiness question

Before asking for readiness, check internally that:

- The intended result and boundaries are clear.
- All consequential choices are confirmed or explicitly deferred.
- There are no unresolved contradictions.
- Success can be checked against the user's requirements.
- The plan can explain the task without relying on the previous chat.

Then send only this question, adjusted to the user's language:

**"Are you ready for me to write the final plan, or would you like to add or change any details first?"**

When a question tool is available, ask it through the tool with two options: **"Write the final plan"** and **"Add or change details"**. The tool's free-text choice lets the user type the new details directly.

Do not include a draft plan, a summary, or a separate document in that message. Ask this question even when the initial request already supplies all the details.

Handle the response as follows:

- **Ready / yes / write the plan:** proceed if no new gap was introduced.
- **Not ready:** ask, "What would you like to add or change?" Then continue clarification.
- **More details or changed choices:** update the decision record, ask any necessary follow-ups, and ask the readiness question again when complete.
- **Unclear response:** clarify whether they want to add details or receive the plan.
- **Silence:** wait; it is not confirmation.

A confirmation covers the choices understood at that point. If the user introduces a material change with their confirmation, clarify it as needed and confirm readiness again. Do not restart questions about settled choices.

## 4. Write the single final plan

After readiness is confirmed, produce exactly one self-contained Markdown plan in a single fenced code block so the user can copy it into a new session. Do not add a PRD, design document, separate specification, or implementation output.

Include the sections below as parts of that one plan. Scale detail to the task without dropping any confirmed choice.

### Task and execution instructions

State the concrete goal and instruct the next AI to carry out this plan. Tell it to:

- Preserve the confirmed decisions and exclusions.
- Add no unrequested features, redesigns, dependencies, or changes.
- Ask a focused question before making an unspecified consequential choice.
- Stop before work affected by a deferred decision or a contradiction.
- Follow the user's new instructions and the environment's permission rules.
- Verify the result against the acceptance criteria and report actual outcomes.

### Context and starting point

Include the context needed to understand the task, what exists, what should change, and what must remain unchanged. Include relevant user-supplied or verified paths and references. Never invent repository structure or refer vaguely to "what we discussed above."

If a screenshot, file, or other source must accompany the plan in the new session, identify it explicitly and state that it must be attached or accessible before the affected work. Do not assume access carries between sessions.

### Confirmed choices and boundaries

Preserve every current explicit user choice. Use a list or table, grouping related decisions without weakening or omitting them. Include exact labels, values, formats, stack choices, user flows, priorities, and exclusions when provided.

Assign short decision identifiers when useful to connect requirements to implementation steps. Do not add a new requirement while writing the plan. Keep explicitly deferred choices visible with an instruction to ask before proceeding.

### Ordered implementation steps

Translate the confirmed requirements into executable steps in dependency order. State what each step changes and which decisions it must preserve. Include known affected areas or files; use descriptive roles when exact paths are unavailable.

Distinguish task decomposition from product decisions: ordering the work does not authorize you to invent features or select unresolved technologies. If writing a step reveals a consequential gap, return to a focused question and obtain readiness again instead of completing the plan with a guess.

### Acceptance and verification

Give observable checks for the requested result, important edge cases, and preservation of exclusions and unchanged behavior. Derive checks from confirmed requirements; do not introduce new product behavior through a test.

Include required commands only when supplied or verified. Otherwise explain what must be checked and let the implementing AI inspect the environment to choose an appropriate check without changing product requirements.

### Final delivery and deferred decisions

State the agreed final output and any delivery constraints. List explicit deferrals with the point at which the next AI must stop and ask. If there are none, state that no decisions were intentionally deferred. Do not claim all future implementation questions are impossible.

## Final completeness check

Before sending, compare the plan against the full decision record. Ensure every current user choice is present, superseded choices are not accidentally active, no defaults were invented, and all needed context is carried forward.

End after the plan. Do not begin execution, invoke an implementation skill, create additional documents, or ask whether to start building in the current session.
