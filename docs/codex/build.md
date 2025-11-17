# Codex Build Instructions

Use this file as the full prompt when asking Codex Architect to build the system. Either reference `docs/codex/build.md` directly in the Codex request or paste the content below into the prompt window.

## Mission
Become Codex Architect’s build agent. Read every canonical spec in `docs/codex/` and synthesize them into a single, coherent plan before touching implementation.

## Required Inputs
1. **Requirements (`docs/codex/requirements.md`)**  
   - Capture purpose, user stories, functional + non-functional requirements, acceptance criteria, and edge cases.  
   - Every feature, validation, and test must trace back to a numbered requirement or acceptance bullet.
2. **Constraints (`docs/codex/constraints.md`)**  
   - Enforce prescribed technologies, security rules, coding style, forbidden approaches, and performance budgets.  
   - If a requested implementation conflicts with a constraint, raise the issue and propose compliant alternatives.
3. **Architecture (`docs/codex/architecture.md`)**  
   - Map the system structure, module boundaries, data flow, component interactions, repo layout, and lifecycle.  
   - Implementation must strictly match the defined modules, naming, and interfaces.
4. **Clarity (`docs/codex/clarity.md`)**  
   - Apply the glossary, naming rules, and ambiguity resolutions.  
   - Resolve any conflicts across documents by preferring clarity conventions, then re-validating with requirements/architecture.
5. **Prompts (`docs/codex/prompts.md`)**  
   - Observe all meta-instructions (e.g., Code Generation, Test Generation, System Build templates).  
   - Use the System Build Template as the governing workflow.

## Workflow
1. **Document Assimilation**  
   - Read every section of the five files. Summarize key points and note open questions.  
   - Confirm terminology alignment via `clarity.md` before referencing entities elsewhere.
2. **Build Plan**  
   - Produce a step-by-step plan aligning each requirement to architectural elements and stating how constraints influence the implementation.  
   - Highlight how edge cases and acceptance criteria will be validated through tests.
3. **Implementation**  
   - Generate code and configuration that conform exactly to the architecture and constraints.  
   - Annotate decisions that come directly from requirements or clarity rules.
4. **Testing**  
   - Write comprehensive tests that prove acceptance criteria, edge cases, and performance constraints.  
   - Provide commands to run the test suite.
5. **Compliance Report**  
   - Explain how the delivered system satisfies every referenced document (requirements coverage, constraint adherence, architectural fidelity, terminology consistency).  
   - Call out any unresolved gaps or assumptions.

## Response Format
- Begin with a short overview of the plan and scope.  
- Present the build plan before the implementation details.  
- After code snippets, include brief notes citing the document clauses that justify each major decision.  
- Finish with run instructions and a compliance checklist referencing the five source docs.

When in doubt, stop and quote the specific requirement/constraint that needs clarification before proceeding. Codex must never improvise beyond what these documents allow.
