---
applyTo: "**/plot_*.py,**/visualize*.py,**/*.ipynb"
---

## Plot & Visualization Guidelines
*Last updated: [DATE]*

This file contains standards for consistent, professional visualizations.

### Style Loading
**CUSTOMIZE THIS SECTION:** Define your organization's matplotlib style.

Option A - Use custom style file:
```python
import matplotlib.pyplot as plt
plt.style.use("path/to/your/custom_style.mplstyle")
colors = plt.rcParams["axes.prop_cycle"].by_key()["color"]  # Get color cycle
```

Option B - Define inline style:
```python
import matplotlib.pyplot as plt
plt.rcParams.update({
    'figure.figsize': (10, 6),
    'axes.labelsize': 12,
    'axes.titlesize': 14,
    'xtick.labelsize': 10,
    'ytick.labelsize': 10,
    'legend.fontsize': 10,
    'font.size': 11
})
colors = ['#1f77b4', '#ff7f0e', '#2ca02c', '#d62728', '#9467bd']  # Define color palette
```

### General Rules
- **Language:** Follow the language used in the current context (notebook/script). Default: as specified in `user.instructions.md`
- **Titles:** Short & descriptive ("Temperature over time" not "Plot of the time series temperature")
- **Axis labels:** Units in square brackets: `Temperature [°C]`, `Flow rate [m³/h]`
- **Legend:** Only for >1 series; use `loc='best'` or `bbox_to_anchor` for many items
- **Layout:** Use `tight_layout()` or `constrained_layout=True`
- **Avoid:** 3D plots, pie charts (unless explicitly requested)

### Date Axis
Use `AutoDateLocator` + `ConciseDateFormatter`, or `plt.gcf().autofmt_xdate()` for short time windows.

### Saving Figures
- Save to `figures/` directory (create if needed)
- Filename convention: `<script_name>_<descriptive_name>.png` (e.g., `analyze_flow_temperature_timeseries.png`)
- DPI ≥110
- Transparent background for presentations: `savefig(..., transparent=True)`

### Annotations
Style settings (line colors, styles) are defined in your custom style file or inline configuration. Use:
- **Thresholds:** `ax.axhline(..., linestyle='--', linewidth=1, color=colors[1])` + short label
- **Events:** Vertical lines (`ax.axvline`) or shaded regions (`ax.axvspan`)
- Minimize text labels; use downstream tooltips (not in static figures)

### Performance
For datasets >200k points: downsample to ~10k-50k before plotting (unless specified otherwise).

### Example
```python
import matplotlib.pyplot as plt
# Load your custom style here

fig, ax = plt.subplots(figsize=(10, 4), constrained_layout=True)
ax.plot(df.index, df['temperature'], label='Temperature')
ax.set(ylabel='Temperature [°C]', xlabel='Time', title='Temperature time series')
ax.legend()

# Save with script reference
plt.savefig('figures/explore_data_temperature_timeseries.png', dpi=150, transparent=True)
plt.show()
```
