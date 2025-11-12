---
applyTo: "notebooks/**/*.ipynb,**/*.ipynb"
---

## Notebook Guidelines
*Last updated: [DATE]*

Ensure reproducible, well-documented notebooks with clear structure.

### Reproducibility
Always start with seed setup + baseline imports in first code cell:
```python
# Repro setup
import os, random, numpy as np
SEED = 42
random.seed(SEED)
np.random.seed(SEED)
os.environ["PYTHONHASHSEED"] = str(SEED)
```

### Structure
Organize in logical sections:
1. Setup (imports, seeds, config)
2. Data load
3. Cleaning & validation
4. Analysis / Feature engineering
5. Visualization
6. Summary & Next Steps

**Best practices:**
- Split long code cells into logical steps with markdown titles
- Each cell should be self-contained and idempotent: re-running any cell (or sequence of cells) multiple times must produce consistent results without side effects

### Code Organization
- Move reusable logic to project utility modules and import
- Avoid mutating global state outside cells; re-run must yield identical results
- Use `head()`, `sample()` to suppress long output

### Security
- No inline credentials or absolute paths
- Use relative paths or environment variables

### Visualization
Follow `.github/instructions/plots.instructions.md`.

### Output
Export results for downstream use via project-specific output utilities.

**Cache intermediate results (parquet):**
For expensive computations, cache as parquet with clear naming in a notebook-specific subdirectory:
```python
# Example: notebook 'explore_data.ipynb' 
# Store in notebooks/explore_data/ subdirectory
df_cleaned.to_parquet('notebooks/explore_data/cleaned_data.parquet')
df_aggregated.to_parquet('notebooks/explore_data/hourly_aggregates.parquet')
```

### Performance
- Use `%%timeit` ad hoc; remove before commit unless educational
- Cache expensive computations as intermediate results (parquet) with clear filenames
- Keep cells focused on basic functionality; avoid unnecessary error handling

### Version Control
Clear outputs before commit: run `jupyter nbconvert --clear-output --inplace <notebook>.ipynb` or use VS Code "Clear All Outputs" command. Exception: keep outputs for demonstration/documentation purposes.
