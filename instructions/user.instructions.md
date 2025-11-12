---
applyTo: "**"
---

## User specific instructions
*Last updated: [DATE]*

This file is intended for personal preferences. Adjust as desired.

### Communication & Chat
**CUSTOMIZE THIS SECTION:** Define language and communication preferences for your team.

**Language defaults:**
- Chat: same language as user input (e.g., **English**, **Dutch**, **French**, etc.)
- Code comments & docstrings: **[SPECIFY LANGUAGE]**
- Function, class, and variable names: **English** (recommended for international collaboration)
- Copilot instruction files: **English** (recommended for maintainability)

**When to divert from defaults:**
- The user explicitly requests a different language
- The file you are working on is primarily in another language

**Language-specific notes:**
- If using non-English languages, avoid spacing errors in compound words
  - Example (Dutch): write 'projectconventies' instead of 'project conventies' and 'Python-bestand' instead of 'Python bestand'
  - Add similar guidance for your language if applicable

**Chat response style:**
- Short and concise; focus on programming speed.
- Balanced level of detail; use summaries where meaningful.
- Ask for clarification when input is unclear.
- Proactively offer code improvements; do not execute without permission.
- Provide choices as bullet points (A, B, C).

### Coding Workflows
**Task size classification:**
- **Small task:** Single function/file, <100 lines, <1 hour, clear scope
- **Large task:** Multiple files/modules, new data structures, architectural decisions, >1 hour estimated effort

**Small tasks**
- For complex code (>30 lines): step-by-step with review checkpoints.
- Focus on performance, readability, memory efficiency, security.
- Avoid over-engineering; prefer smallest viable solution first.
- When making assumptions: document clearly in comments with rationale.
  Example: `# A1: Assume daily refresh interval (rationale: confirmed by stakeholder)`

**Large tasks**
1. Always start with a structured workflow plan (no code yet):
  - Context & objective confirmation
  - Requirements extraction (functional, non-functional)
  - Architectural / design outline
  - Implementation phases (ordered, with dependencies)
  - Quality & governance (tests, security, data consistency)
After workflow: ask critical clarification questions (bullet list) focusing on assignment essentials and missing details like:
  - Refined problem statement & success criteria 
  - Scope boundaries (explicit IN vs OUT)
  - Data sources (availability, quality, access)
  - If data incomplete or quality uncertain: run profiler first, document quality issues before proceeding
  - Required feature types (categorical, numerical, time series, geospatial)
  - Other assumptions you must currently make, influencing the quality of the result (list these explicitly for confirmation)
Wait for user answers or explicit "proceed" before generating code.
2. When proceeding, provide in bullets:
  - Refined workflow (updated with user input)
  - Implementation checklist (actionable steps)
  - Assumption block (numbered A1, A2, A3..., including rationale per assumption)
    Example format: `A1: Assume daily data refresh (rationale: confirmed by stakeholder)`
  - Risks & mitigations (concise)
Then start writing code aligned with agreed plan.
- If user changes direction mid-process: re-run clarification phase quickly.
- Avoid over-engineering; prefer smallest viable solution first.
- Escalate (ask) if conflicting requirements appear.
- When making assumptions: document clearly in comments with rationale and reference number (A1, A2...).
- Never assume sensitive data; enforce placeholders for secrets (use `os.getenv()` pattern).
- Overall: work independently. Run code if runtime available to verify correctness or explore data. No review checkpoints.
- Sensitive work: do NOT execute operations that imply external side effects (deploy, delete) without explicit instruction.
3. When finished make sure:
  - All tasks are completed
  - Requirements are 100% met
  - Code is properly tested
  - Documentation is updated
  - `.github/copilot-instructions.md` and `.github/instructions/python_standards.instructions.md` compliance
4. Finally, provide concise:
  - Summary of delivered solution
  - Known limitations & assumptions recap
  - Next steps recommendations and improvement suggestions (performance, readability, security, memory) if relevant - in bullet points

**Completion checklist:**
- ✓ Workflow followed (for large tasks)
- ✓ Assumptions confirmed and documented (A1, A2...)
- ✓ Code tested (unit tests or manual verification)
- ✓ Docstrings present (minimum: summary + Args + Returns)
- ✓ Security: no hardcoded secrets, sensitive data handled via environment variables
- ✓ Compliance: `.github/copilot-instructions.md` and `.github/instructions/python_standards.instructions.md` rules followed
