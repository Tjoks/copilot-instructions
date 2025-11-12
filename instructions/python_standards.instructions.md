---
applyTo: "**/*.py,**/*.ipynb"
---

## Python Standards & Best Practices
*Last updated: [DATE]*

This file contains Python-specific coding standards. 

### Documentation Standards
Use Google-style docstrings with: Args, Returns, Raises, Examples (when meaningful).
Language: follow `.github/instructions/user.instructions.md` Communication & Chat section.

**Required for:**
- All public functions and classes
- Complex private functions (>30 lines or non-trivial logic)

**Minimum requirements:**
- Summary line (one sentence)
- Args section (if parameters exist)
- Returns section (if function returns value)

### Naming Conventions
- **PascalCase** for classes / type aliases  
- **snake_case** for functions, methods, variables  
- **`_prefix`** for (pseudo)private members  
- **ALL_CAPS** for module constants  

### Type Hints
**Policy:**
- **Mandatory** for all public API functions
- **Recommended** for complex internal functions
- **Optional** for simple utility functions (<10 lines, obvious types)

**Guidelines:**
- Use `Optional[T]` for parameters that can be `None` (equivalent to `Union[T, None]`)
- Avoid `Union[A, B, C]` for multiple types; prefer function overloads or generics instead

**Example:**
```python
from typing import Optional
import pandas as pd

def process_data(
    df: pd.DataFrame,
    column: str,
    threshold: float = 0.5,
    output_path: Optional[str] = None
) -> pd.DataFrame:
    """Process dataframe based on threshold."""
    result = df[df[column] > threshold].copy()
    if output_path is not None:
        result.to_csv(output_path)
    return result
```

---

## Dependency Management with uv

**Basic principle:** Exclusively use `uv` for package management. No `pip`, `conda`, `mamba`, or `requirements.txt`.

**Exception:** If the project already uses a different environment manager (e.g., `conda`, `poetry`, `pipenv`), continue using that manager for consistency. Do not switch mid-project unless explicitly migrating.

**Essential commands:**
- **Install dependency:** `uv add <package>` (adds to `pyproject.toml` + updates `uv.lock`)  
- **Remove dependency:** `uv remove <package>`  
- **Sync environment:** `uv sync` (installs exactly according to `uv.lock`)  
- **Run script:** `uv run <script.py>` (executes in isolated env)  
- **Run notebook:** `uv run jupyter lab` (starts Jupyter with correct dependencies)  
- **Update all packages:** `uv lock --upgrade` (refreshes lock file with latest versions)  
- **Dev dependencies:** `uv add --dev <package>` (e.g., pytest, ruff)  

**Best practices:**
- Do **not** manually pin versions in pyproject.toml unless explicitly required for compatibility  
- Periodically run `uv lock --upgrade` for security patches  
- Never manually edit pyproject.toml or uv.lock (always use uv commands)  
- For merge conflicts in uv.lock: re-run `uv lock`  

**Python version management:**
- Python version is specified in `pyproject.toml` under `[project]` → `requires-python`  
- To change: adjust `requires-python = ">=3.11"` and run `uv sync`

---

## DataFrame Optimization

**Type management:**
- Set `dtype=` when reading for known types
- After filling object columns: optionally `.infer_objects(copy=False)`
- Use `category` for low-cardinality columns, `Int64` for nullable integers

**Time series conventions:**
- Sub-day frequencies lowercase (`'h','min','s','ms','us','ns'`)
- Month/quarter/year uppercase (`'M','Q','Y'`)

**Best practices:**
- `groupby(..., observed=True)` as default for categorical data
- No chained assignment; always use `.loc` for assignments
- Vectorize where possible; only use loops with `itertuples()` when truly necessary

### Memory / Performance
- Large merges: sort on join keys; use `merge(..., sort=False)`
- Adjust column order just before export
- Use iterators / generator expressions for large datasets

**Examples:**
```python
# Memory-efficient filtering
mask = (df['value'] > threshold) & (df['category'].isin(valid_categories))
result = df.loc[mask, ['col1', 'col2']].copy()

# Correct .loc usage (avoid chained assignment)
df.loc[df['price'] > 100, 'category'] = 'expensive'  # Good
# df[df['price'] > 100]['category'] = 'expensive'    # Bad (SettingWithCopyWarning)

# Categorical dtype conversion
df['status'] = df['status'].astype('category')
df['status'] = df['status'].cat.set_categories(['pending', 'active', 'closed'], ordered=True)
```

---

## Time Conventions (Domain-Specific)

**CUSTOMIZE THIS SECTION:** Define time conventions specific to your domain/organization.

**Example template:**
- **Local time zone:** `[YOUR_TIMEZONE]` (e.g., `America/New_York`, `UTC`, `Asia/Tokyo`)
- **Column naming standards:**
    - `Datetime_local` (local datetime)
    - `Datetime_utc` (UTC datetime)
    - `[Custom timestamp columns based on your domain]`
- **Business day definition:** [Define if different from standard midnight-to-midnight, e.g., "Starts at 06:00 local time"]

**Storage vs presentation:**
- Store timestamps in UTC when possible
- Convert to local timezone for presentation/reporting

**Remove this section if not applicable to your project.**

---

## Code Style & Quality

**Function and file size:**
- Functions <50 lines; refactor larger functions with rationale comment
- Maximum 1000 lines per file; split script across multiple files if needed
- For refactors, provide brief rationale (comment above change)

**Code quality principles:**
- Follow PEP8 + project-specific data science conventions (vectorize where possible)  
- Use try/except for (a)synchronous or IO-bound operations; log contextually  
- Avoid memory-intensive loops; use iterators / generator expressions  

**Error handling:**
- Wrap IO operations in try/except
- Re-raise with compact context (no sensitive data in error messages)

**Logging best practices:**
*Define logging standards for your project (e.g., use logging module, print statements, log levels).*

---

## Practical Examples

### Time Series Resampling
```python
# Resample to hourly, right-labeled (timestamp at end of period)
series = series.resample('h', label='right', closed='right').mean()

# Resample to daily with custom aggregation
daily = df.resample('D', on='timestamp').agg({
    'value': 'sum',
    'status': lambda x: x.mode()[0] if len(x) > 0 else None
})
```

### GroupBy with Categories
```python
# Efficient groupby with observed=True (excludes unused categories)
result = df.groupby(['category_col', 'date_col'], observed=True)['value'].sum()

# Multiple aggregations
summary = df.groupby('category', observed=True).agg({
    'value': ['sum', 'mean', 'count'],
    'timestamp': ['min', 'max']
})
```

### Data Type Optimization
```python
# Read with optimized dtypes
dtypes = {
    'id': 'int32',
    'category': 'category',
    'status': 'category',
    'value': 'float32'
}
df = pd.read_csv('data.csv', dtype=dtypes, parse_dates=['timestamp'])

# Convert nullable integers
df['optional_id'] = df['optional_id'].astype('Int64')  # Capital I for nullable
```
