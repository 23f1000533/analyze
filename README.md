# Project Setup and CI/CD

This repository contains a simple data processing pipeline using Python with Pandas, along with a responsive web application built with Tailwind CSS. It also features a GitHub Actions workflow for continuous integration and deployment.

## Project Structure

- `index.html`: A single-file responsive HTML application using Tailwind CSS.
- `execute.py`: A Python script to process `data.xlsx`, convert it to `data.csv`, perform data aggregation, and output `result.json`.
- `data.xlsx`: The input Excel file for processing (example content described below).
- `.github/workflows/ci.yml`: GitHub Actions workflow for CI/CD.
- `LICENSE`: MIT License.
- `README.md`: This file.

## `data.xlsx` Structure (Example)

The `data.xlsx` file is expected to contain tabular data. For this project, a sample structure might be:

| ID | Item | Value | Quantity |
|----|------|-------|----------|
| 1  | A    | 100   | 2        |
| 2  | B    | 150   | 1        |
| 3  | C    | 200   | N/A      |
| 4  | D    | 50    | 3        |

*(Note: The `N/A` in `Quantity` is specifically handled by `execute.py`'s error fix.)*

## Getting Started

### Prerequisites

Ensure you have Python 3.11+ installed.

### Local Setup

1.  **Clone the repository:**
    ```bash
    git clone <repository-url>
    cd <repository-name>
    ```

2.  **Create a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate # On Windows: .\venv\Scripts\activate
    ```

3.  **Install dependencies:**
    ```bash
    pip install pandas openpyxl ruff
    ```

4.  **Place `data.xlsx`:**
    Ensure your `data.xlsx` file is in the root directory of the project.

### Running the `execute.py` script

To run the data processing script locally:

```bash
python execute.py
```

This will:
1.  Read `data.xlsx`.
2.  Convert `data.xlsx` to `data.csv`.
3.  Process `data.csv` (handling potential non-numeric entries in 'Quantity').
4.  Generate `result.json` in the root directory, containing aggregated data.

## `execute.py` - Non-Trivial Error Fix

The `execute.py` script has been updated to fix a non-trivial error related to data type handling. Previously, attempting to multiply columns like 'Value' and 'Quantity' would fail if 'Quantity' contained non-numeric values (e.g., 'N/A' or strings). The fix involves using `pd.to_numeric(..., errors='coerce').fillna(0)` to robustly convert relevant columns to numeric types, coercing errors to `NaN` (Not a Number), and then filling `NaN` values with `0` before performing calculations. This ensures the script is resilient to messy or incomplete input data.

## GitHub Actions CI/CD Workflow (`.github/workflows/ci.yml`)

A GitHub Actions workflow is configured to run on every `push` to the repository. This workflow performs the following steps:

1.  **Checkout Code:** Fetches the repository content.
2.  **Setup Python:** Configures Python 3.11.
3.  **Install Dependencies:** Installs `pandas`, `openpyxl`, and `ruff`.
4.  **Run Ruff:** Executes `ruff check .` to lint the Python code and displays the results in the CI log.
5.  **Run `execute.py` and Generate `result.json`:** Executes `python execute.py > result.json`. This command runs the data processing script and redirects its JSON output to `result.json`.
6.  **Upload GitHub Pages Artifact:** The generated `result.json` is uploaded as an artifact, specifically for GitHub Pages deployment.
7.  **Deploy to GitHub Pages:** The `result.json` artifact is then deployed to GitHub Pages. After a successful run, `result.json` will be accessible via your GitHub Pages URL (e.g., `https://<your-username>.github.io/<repository-name>/result.json`).

**Note:** `result.json` is generated only during the CI process and is not committed to the repository.
