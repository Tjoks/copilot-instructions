---
applyTo: "tests/**/*.py,**/test_*.py,**/*.ipynb"
---

## Testing Guidelines
*Last updated: [DATE]*

Write deterministic, understandable tests that cover edge cases.

### Basics
- **Framework:** `pytest` or `unittest`; choose one style per module
- **File naming:** `test_<module|function>.py`
- **Function naming:** `test_<function_name>` (pytest convention)
- **Comments:** Add brief comment above each test explaining purpose & expectation

### Quality Rules
- **Deterministic:** Set random seeds (`numpy.random.seed(42)`)
- **Minimal IO:** Mock external sources (see examples below)
- **Fixtures:** Reuse via `conftest.py`
- **Edge cases:** Empty input, None, large datasets, wrong dtypes, timezone issues
- **Exceptions:** Use `pytest.raises` or `unittest.assertRaises`

### Test Types
**Unit tests:** Test single function/method in isolation. Target <1s execution time.

**Integration tests:** Test interaction between components (e.g., data pipeline end-to-end). Separate with `itest_` prefix:
```python
# itest_data_pipeline.py
def itest_full_pipeline_execution():
    # Goal: verify complete data flow from raw to processed
    result = run_full_pipeline('data/raw/sample.csv')
    assert result.shape[0] > 0
```

### Parameterized Tests
Use `@pytest.mark.parametrize` for data-driven tests:
```python
import pytest

@pytest.mark.parametrize("input_val,expected", [
    (0, 0),
    (5, 25),
    (-3, 9),
])
def test_square_values(input_val, expected):
    # Goal: test square function with multiple inputs
    assert square(input_val) == expected
```

### Mocking External Dependencies
Mock file IO and external API calls:
```python
from unittest.mock import patch, MagicMock
import pandas as pd

def test_load_data_mocked():
    # Goal: test data loading without actual file access
    mock_df = pd.DataFrame({'a': [1, 2, 3]})
    
    with patch('pandas.read_csv', return_value=mock_df):
        result = load_data('fake.csv')
        assert len(result) == 3
        assert 'a' in result.columns

def test_api_call_mocked():
    # Goal: test API integration without network call
    mock_response = MagicMock()
    mock_response.json.return_value = {'status': 'ok'}
    
    with patch('requests.get', return_value=mock_response):
        result = fetch_data('https://api.example.com/data')
        assert result['status'] == 'ok'
```

### Example (pytest)
```python
# test_calculation_module.py
import pytest
from your_module import safe_sum

def test_safe_sum_basic():
    # Goal: correct sum for list of integers
    assert safe_sum([1, 2, 3]) == 6

def test_safe_sum_none():
    # Goal: ValueError when input is None
    with pytest.raises(ValueError):
        safe_sum(None)
```

### Example (unittest)
```python
import unittest
from your_module import safe_sum

class TestSafeSum(unittest.TestCase):
    def test_basic(self):
        # Goal: correct sum
        self.assertEqual(safe_sum([1, 2, 3]), 6)

    def test_none(self):
        # Goal: ValueError when None
        with self.assertRaises(ValueError):
            safe_sum(None)
```
