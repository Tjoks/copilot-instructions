## Copilot Repository-Wide Instructions
*Last updated: [DATE]*

This file contains generic guidelines for data science projects. Specialized guidelines are located in path-specific instruction files under `.github/instructions/`.

### Project overview 
This is a **data science / analytics project** with a standardized structure for reproducible research. Main components:
- Python utilities for reusable functionality  
- Notebooks for exploration and analysis  
- Models for stored assets  
- Optional interactive interfaces for visualization  

**Business context:** 
<!-- CUSTOMIZE THIS SECTION: Add 2-4 sentences about the specific domain, objective, and key stakeholders of this project. 
Example: "This project focuses on predictive maintenance for manufacturing equipment, aiming to reduce downtime by 30%. 
Primary stakeholders are the Operations team and Plant Managers." -->

### Folder structure (template)
```
├── .github/
│   ├── copilot-instructions.md      # This file (generic)
│   ├── instructions/                # Path-specific instructions
│   └── prompts/                     # Reusable prompt templates
```

### Environment & Dependencies
- **Package management:** 
  - See `.github/instructions/python_standards.instructions.md` for complete workflow and best practices. 
- **Code quality:** Use `ruff` for formatting and linting (replaces black + flake8).  
  - Configuration in `pyproject.toml` under `[tool.ruff]`  
  - Line-length default: 150 characters  
  - On PR: `ruff check` validates code style  
- **Makefile commands:** Use `make` for standard workflows:  
  - `make clean` – remove compiled Python files  
  - `make lint` – run ruff linting check  
  - `make format` – run ruff formatter  
  - `make data` – generate processed datasets (if data pipeline defined)  

### Development Workflows
**Setup (new developer):**
1. Clone repository  
2. Install `uv`: `pip install uv` (or follow [official installation](https://github.com/astral-sh/uv))  
3. Initialize project: `uv sync` (installs all dependencies from `pyproject.toml`)  
4. Install VS Code recommended extensions (`.vscode/extensions.json`)  
5. Verify setup: `uv run python test_environment.py`  

**Documentation generation:**
- Generate HTML docs via Sphinx: see documentation guide in root  
- Auto-doc only works if docstrings are consistent (Args, Returns, Raises, Examples)  

### Security & Privacy
- **No sensitive data** (PII, IP, business-sensitive info) in code, comments, or documentation.  
- Treat files with names containing *secret*, *credential*, or *key* as sensitive.  
- Use `.env` or secret management tools for sensitive configurations (API keys, passwords).  
- Data storage: `data/` folder is `.gitignored` – never commit data to repo.  
- External API calls: wrap in try/except; do not log sensitive request/response details.  

### Language & Documentation
- See `.github/instructions/user.instructions.md` for language preferences and response style.

### Testing Strategy (global framework)
- New functionality: add unit tests (pytest or unittest).  
- Keep tests deterministic; minimize external dependencies.  
- See `.github/instructions/tests.instructions.md` for detailed guidelines.  

### Reference to path-specific instructions
The following additional instruction files exist:
- `python_standards.instructions.md` for python standards like dependency management & DataFrame optimization.
- `plots.instructions.md` for plotting style & visualization guidelines.
- `tests.instructions.md` for detailed test/fixtures guidelines.
- `notebooks.instructions.md` for notebook reproduction & conventions.
- `user.instructions.md` for language and response preferences.

### Maintenance & Update Guidelines
- **Folder structure:** Maintain an up-to-date folder structure overview in this file that reflects the actual project layout. Update when directories are added, removed, or reorganized.
- **Path-specific instructions:** Update "Reference to path-specific instructions" when instruction files are added or changed.
- **Business context:** Periodically review and update the "Business context" section to ensure it reflects current project objectives and stakeholders.
- **Dependency changes:** When `pyproject.toml` or `Makefile` changes, verify that dependency lists and command descriptions in this file remain accurate.
- **Proactive improvement:** Analyze existing scripts, notebooks, and code patterns in the project. Propose updates to instruction files when you identify:
  - Recurring patterns that should be standardized
  - Common practices not yet documented
  - Inconsistencies between instructions and actual codebase
  - Opportunities to improve clarity or add missing guidelines
- **Conflict resolution:** When conflicts arise between instructions, report to user and suggest solutions. Wait for approval before committing changes to instruction files.
