# Prompts

Reusable prompt templates grounded in Robby's Motivator’s requirements, constraints, architecture, and clarity documents.

## 1. Code Generation Template
Using the requirements, constraints, architecture, and clarity documents for Robby's Motivator, implement the following feature:

[Describe the desired feature or enhancement here]

Guidelines:
- Assume Flutter targeting Android and Google Sheets as the single source of truth.
- Never mutate sheet columns other than the delivery flag (column B) and the frequency field.
- Keep data sync, scheduling, and UI layers separated as defined in architecture.md.
- Write or update tests that prove the acceptance criteria in requirements.md.

## 2. Test Generation Template
Create focused unit and integration tests for the following feature of Robby's Motivator, using acceptance criteria and edge cases defined in requirements.md:

[Describe the code or scenario that needs tests]

Ensure tests cover:
- Frequency parsing and scheduling expectations.
- Random selection/reset behavior when rows are added, removed, or exhausted.
- Google Sheet update calls (mock the API layer—do not hit the network).

## 3. Debugging Template
Given the following failing test, runtime error, or unexpected Google Sheet state:

[Insert logs, stack traces, or sheet diffs]

Diagnose the issue while adhering to architecture.md and constraints.md, then provide a corrected implementation plan and the minimal code changes needed. Highlight how the fix preserves the sheet as the source of truth and keeps the delivery flag logic intact.

## 4. Refactoring Template
Refactor the following module or set of files in Robby's Motivator to improve clarity, maintainability, or testability without altering external behavior:

[Identify the target module(s)]

Instructions:
- Preserve module boundaries (sheet sync, scheduler, delivery engine, UI) defined in architecture.md.
- Keep the frequency and delivery flag semantics identical.
- Update or add tests to confirm behavior parity.

---

Use these templates whenever Codex Architect needs explicit instructions for implementing, testing, debugging, or refactoring within this project.
