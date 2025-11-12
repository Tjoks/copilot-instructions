---
description: 'Stepwise Python file optimization with review checkpoints'
mode: 'agent'
---

# Optimize Python Script
*Last updated: [DATE]*

## Mission
Optimize a Python file step by step with review checkpoints after each phase. Focus on type hints, documentation, refactoring, and DataFrame optimization (if applicable). 

## Scope & Conditions
- Target: the Python file provided via input
- Ignore folders: folders specified by user or project-specific exclusions
- Review required after each step before proceeding
- Preserve existing business logic; no semantic changes without motivation

## Inputs
- `${input:pythonFile[:Path to Python file]}` - The Python file to optimize
- `${input:focusFolder[:Folder to focus on (optional)]}` - Specific folder within codebase (optional)
- `${input:detailedSummary[:Summarise or list output in chat? (yes/no, default: yes)]}` - Whether the user wants a summary/list output in chat (default: yes). If explicit 'no': skip listing changes in chat, only confirm completion of each step.

**Behavior for missing inputs:**
Explicitly request the file path. Stop if not provided.

## Communication & Chat
- Follow Communication & Chat instructions from `.github/instructions/user.instructions.md`

## Workflow

### Step 1: Type Hints
- **Execute:**
  - Add type hints to all functions and methods, following `.github/instructions/python_standards.instructions.md`
- **Output:** List of added type hints
- **Pause for review**

### Step 2: Docstrings
- **Execute:**
  - Add Google-style docstrings, following `.github/instructions/python_standards.instructions.md`
  - Review existing docstrings for accuracy/completeness
  - Document all functions, classes, and modules
- **Output:** List of added documentation (in keywords)
- **Pause for review**

### Step 3: Inline Comments
- **Execute:**
  - Add concise comments where intent is unclear
  - Avoid redundancy with docstrings (remove if overlapping)
- **Output:** List of added comments with rationale (brief)
- **Pause for review**

### Step 4: Clean Imports
- **Execute:**
  - Remove unused imports
  - Order logically: stdlib → third-party → local
- **Output:** Diff of import changes
- **Pause for review**

### Step 5: Refactoring
- **Execute:**
  - Improve variable names for clarity
  - Split functions according to the Code Style & Quality guidelines in `.github/instructions/python_standards.instructions.md`
  - Simplify complex logic (leaving business logic intact)
  - Standardize error handling, follow guidelines from `.github/instructions/python_standards.instructions.md`
- **Output:** Overview of refactors with motivation
- **Continue to step 6 (skip pause)**

### Step 6: Best Practices Proposal
**⚠️ Analysis only - do not execute changes before user approval.**
- Analyze code and suggest improvements, e.g.:
  - **Testing:** Add/expand unit tests for critical paths
  - **Validation:** Extra input validation (type checks, range checks)
  - **Performance:** Vectorization opportunities, identify bottlenecks
  - **Configuration:** Extract hardcoded values to config
  - **Security:** Check for hardcoded secrets, unsafe input handling
  - **Code Quality:** Identify duplication, inconsistent API patterns
  - **Business Logic:** Review for inconsistencies or optimization opportunities
- **Output:** List (A, B, C, ...) of recommendations in chat, ask user if and which to implement
- **Pause for review**
- When the user requests to execute any recommendations:
  - **Execute:**
    - Implement approved recommendations immediately, and only those
  - **Output:** Summary of executed changes
  - **Pause for review**
- **Continue to step 7**

### Step 7: DataFrame Optimization (Conditional)
**Only perform if Pandas DataFrame pipeline is present. Otherwise: skip this step and proceed to Wrap-up.**
- **Execute:**
  - Optimize DataFrame transformations per all guidelines in `.github/instructions/python_standards.instructions.md`:
  - Focus on: indexing, type management, memory efficiency, vectorization, and time series conventions
  - Apply WITHOUT functional regression
- **Output:** List of optimizations with performance impact estimate
- **Pause for review**

## Expected Output

**Per step:**
1. Compact summary of changes (bullet list)
2. Short diff fragments OR explanation why no changes needed
3. Open questions / uncertainties
4. **Explicit question:** "Proceed to [next step]?"

**Success criteria:**
- All 6 (or 7) steps completed
- Each step reviewed and approved
- No functional regression
- Code complies with project conventions

**Failure conditions:**
- User stops process
- Functional bug introduced
- Project conventions violated
On failure: revert uncommitted changes and explain issue to user.

## Quality Control

**Constraints:**
- No new dependencies without proposal + motivation
- No experimental pandas APIs
- No semantic changes without explicit rationale

**Validation steps:**
1. Check that all type hints are correct
2. Verify that docstrings are complete
3. Test that existing functionality remains unchanged
4. Review DataFrame optimizations for correctness

## Wrap-up (after all steps)
Provide concise summary:
- **Completed steps:** List which steps were executed
- **Key improvements:** Highlight main changes (3-5 bullets)
- **Recommended next steps:**
  - Add/expand test cases (refer to `.github/instructions/tests.instructions.md`)
  - Monitor performance in production
  - Further refactoring opportunities (if identified)

## Usage Example

**Basic usage:**
```
@workspace /run optimize-this-script.prompt.md
File: path/to/calculation_module.py
```

**With focus folder, limited chat output:**
```
@workspace /run optimize-this-script.prompt.md
File: path/to/data_processing.py
Focus folder: src/
detailedSummary: no
```
