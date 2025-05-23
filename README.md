# barinbar

![barinbar](https://github.com/1vecera/barinbar/blob/main/barinbar.png?raw=true)


[![PyPI version](https://badge.fury.io/py/barinbar.svg)](https://badge.fury.io/py/barinbar)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Poetry](https://img.shields.io/endpoint?url=https://python-poetry.org/badge/v0.json)](https://python-poetry.org/)

A Python package for creating bar-in-bar charts and treemap bar charts using Matplotlib.

## Inspiration

This package was created in response to discussions and implementations of bar-in-bar charts across different platforms:

-   **Original LinkedIn Post by Colin Tomb:** [Link](https://www.linkedin.com/posts/colintombfinancialprofessional_data-dataanalysis-python-activity-7315405292647653378-TsKG)
-   **Tableau Implementation (Kevin Flerlage, Sebastine Amede, Darragh Murray, Brittany Rosenau):** [Blog Post](https://lnkd.in/gxFkKz8f)
-   **R Implementation (Brian Julius):** [Post](https://lnkd.in/gKhjXEvN)
-   **Deneb Implementation (Daniel Marsh-Patrick):** [Blog Post](https://lnkd.in/gRMk9GtK)

This package aims to provide straightforward Python (Matplotlib) alternatives.

## Installation

```bash
pip install barinbar
```

*(Note: This assumes the package is published on PyPI.)*

## Development Setup (using Poetry)

If you want to contribute or run the code locally:

1.  **Install Poetry:** Follow the instructions on the [official Poetry website](https://python-poetry.org/docs/#installation).
2.  **Clone the repository:**
    ```bash
    git clone <your-repo-url>
    cd barinbar
    ```
3.  **Install dependencies:** This command creates a virtual environment and installs all dependencies (including development tools).
    ```bash
    poetry install --with dev # Use --with dev instead of --all-extras for newer Poetry
    ```
4.  **Activate the virtual environment:**
    ```bash
    poetry shell
    ```
5.  Now you can run scripts or linters, e.g., `python barinbar/plot.py`.

## Requirements

-   Python >= 3.10
-   `matplotlib`
-   `pandas`
-   `numpy`
-   `squarify`

## Usage

### Bar-in-Bar Chart

The main function is `plot_bar_in_bar`. It requires a pandas DataFrame structured with columns for the main category, sub-category, segment value, and total category value.

```python
import pandas as pd
from barinbar import plot_bar_in_bar, BarinbarStyle, plot_treemap_bars # Import from package root
from matplotlib.cm import get_cmap
import numpy as np # Import numpy

# 1. Prepare your data
data = {
    "Region": ["West"]*3 + ["East"]*3 + ["Central"]*3 + ["South"]*3,
    "Segment": ["Consumer", "Corporate", "Home Office"] * 4,
    "Revenue": [364, 232, 143, 357, 204, 131, 254, 158, 91, 196, 122, 74],
}
revenues = pd.DataFrame(data)

# Calculate totals per main category
region_totals = revenues.groupby("Region")["Revenue"].sum().reset_index()
region_totals = region_totals.rename(columns={"Revenue": "TotalRevenue"})

# Merge totals back into the main DataFrame
revenues = pd.merge(revenues, region_totals, on="Region")

# Sorting is now handled internally by _prepare_data

# 2. Define Styling (Optional)
default_color_palette = get_cmap("Dark2")
custom_colors = {
    "Consumer": default_color_palette(0),
    "Corporate": default_color_palette(1),
    "Home Office": default_color_palette(2),
}
my_style = BarinbarStyle(
    fontname='serif',
    colors=custom_colors,
    figsize=(12, 7)
)

# 3. Plot the chart
# Example with total background width
print("Plotting Bar-in-Bar (Total Background)...")
plot_bar_in_bar(
    df=revenues.copy(),
    category_col="Region",
    subcategory_col="Segment",
    value_col="Revenue",
    total_col="TotalRevenue",
    title="Revenue by Region and Segment (Background=Total Width)",
    style=my_style,
    back_ground_width="total" # Specify background width option
)

# Example with single background width
print("Plotting Bar-in-Bar (Single Background)...")
plot_bar_in_bar(
    df=revenues.copy(),
    category_col="Region",
    subcategory_col="Segment",
    value_col="Revenue",
    total_col="TotalRevenue",
    title="Revenue by Region and Segment (Background=Single Width)",
    style=my_style,
    back_ground_width="single" # Specify background width option
)

```

### Treemap Bars Chart

The `plot_treemap_bars` function generates horizontally arranged treemaps, where each treemap represents a group, and the rectangles within represent categories. The width of each treemap is consistent, while its height is proportional to the total value of the group. Groups are sorted left-to-right from highest total value to lowest.

```python
# (Assuming 'revenues' DataFrame and 'my_style' from previous example)

print("Plotting Treemap Bars...")
plot_treemap_bars(
    df=revenues.copy(),
    category_col="Segment",  # Categories within each treemap box
    group_col="Region",      # How data is grouped into separate treemaps
    value_col="Revenue",     # Determines size and height scaling
    title="Regional Revenue Distribution by Segment (Treemap)",
    figsize=(14, 7),         # Adjust figsize as needed
    value_format_str="${value}K",
    fontname=my_style.fontname, # Reuse font from style
    base_palette="tab10",     # Choose a suitable palette
)
```

This will generate and display the respective charts.

## Customization

You can customize the appearance of the bar-in-bar chart by creating an instance of the `BarinbarStyle` dataclass and modifying its attributes (e.g., `figsize`, `colors`, `fontname`, `fontsize`, `background_color`). Pass this style object to the `plot_bar_in_bar` function.

For `plot_treemap_bars`, customization is done via function arguments like `figsize`, `value_format_str`, `fontname`, and `base_palette`.

```python
from barinbar import BarinbarStyle # Import from package root

custom_style = BarinbarStyle(
    background_color="#F0F0F0",
    figsize=(10, 6),
    fontsize=18,
    small_fontsize=11,
    fontname="Arial",
    colors={"Consumer": "blue", "Corporate": "green", "Home Office": "red"}
)

# ... then pass `style=custom_style` to the plot_bar_in_bar function
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

-   **Daniel Vecera** - [daniel.vecera@outlook.com](mailto:daniel.vecera@outlook.com)


