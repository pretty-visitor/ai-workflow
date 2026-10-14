---
name: plan-complex-work
description: Plan large or multi-stage work as a resumable, step-by-step plan file. Use whenever the user asks for a plan, roadmap or breakdown of a non-trivial task, wants work they can continue across sessions, or is in Plan mode on work spanning several components or design decisions — even without saying "plan". Confirms every design decision with the user, then writes a plan whose steps can each be done and verified in a separate session.
---

# Plan complex work

Produce a **resumable** plan. Once the earlier steps are done, any step can be done in a fresh session with no memory of this conversation, so the plan file must hold the goal, the confirmed decisions, and each step's tasks and verification.

Work through the three stages in order. Whenever a new design decision comes up, return to Stage 1.

## Stage 1 — Confirm the design with the user

Every design decision is made here and must come from the user, because the session doing a step can't ask why something was chosen.

1. Explore the codebase first, so you don't ask what the code already answers.
2. Confirm every design decision with the user, using `AskUserQuestion`: scope, approach, architecture, libraries, data formats, UI behavior, edge cases. Answers often raise new decisions, so keep asking until everything is confirmed.

## Stage 2 — Split the work into steps

1. **Split into vertical slices.** A slice is a thin feature that works end to end from the entry point. The first slice is the simplest working version, and each later one adds to it. Don't split by layer, because a layer stays unused until the one above it exists.
2. **Split each slice into one or more steps.** Each step:
   - adds behavior that a test or the entry point shows
   - adds only code that non-test code calls in the same step. Exception: if the file limit forces a split, the caller may come in a later step of the same slice.
   - changes at most 8 files (tests included, plan file excluded), so it can be reviewed in one sitting
   - leaves the project working
   - depends only on earlier steps
3. **Write each step's tasks**, one action per task. Name every data type and public method added or changed, with its signature, its previous form if changed, and where it is called. Name what the task uses from earlier steps: types, methods, decisions.
4. **Write each step's verification.** List checks, each with the behavior it checks, the command or exact steps, and the expected result. Prefer automated tests (unit, integration, end-to-end). If tests aren't possible, run the app or inspect output. A slice's last step includes an end-to-end check through the entry point (e.g. running the CLI command or calling the endpoint).
5. **Confirm the step list with the user.** Show each step in order with its slice, title and one-line summary. If they change anything, redo the affected items.

## Stage 3 — Write the plan

Write the plan to `plans/<short-plan-description>.md` in the repo root (kebab-case name), using the template below exactly. In Plan mode, present the full plan with `ExitPlanMode` and write the file as the first action after approval.

## Plan template

````markdown
# <Plan title>

<2–3 sentences on what will be built or changed, and why.>

## How to continue in a separate session
1. Read this whole file.
2. In this session, do only the first step whose heading is unchecked (`## [ ] Step ...`).
3. Don't stop to ask the user questions. If an implementation decision isn't covered by this plan, or the work had to differ from it, choose what best fits the goal and the **Confirmed decisions**, and record it with the reason under **Deviations from plan**. Update any later steps it affects.
4. Do every item in its **Tasks**, and no work from later steps.
5. Run its **Verification plan** and fix problems until it passes.
6. Write a short **Verification Summary** under the step, saying how it was verified and the result.
7. Only then tick the step's heading (`## [x] Step ...`).

## Confirmed decisions
- <Decision confirmed with the user>
- <...>

---

## [ ] Step 1 — <Slice name>: <step title>

### Tasks
- <Actionable item>
- <...>

### Verification plan
- <Check>
- <...>

### Verification Summary
_Not verified yet._

### Deviations from plan
_None._

---

## [ ] Step 2 — <Slice name>: <step title>

<Same subsections as Step 1.>
````
