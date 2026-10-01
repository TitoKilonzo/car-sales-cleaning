# Data Wrangling & Preprocessing: Car Sales Dataset

Phase 2, Thread 2, Week 2 practical lab: clean and prepare a used-car dealership's sales records end to end — missing values, two different kinds of duplicates, mileage outliers, data types, and categorical encoding — ready for next week's visualization work.

## A note on the data

The lab guide references a dealership-provided `Car_Sales_Dataset.csv` with no public link. The copy in this repo is a synthetic stand-in built to match the lab's exact schema and the exact issues it describes: missing prices (~6%), 15 exact duplicate rows, 20 more records sharing a `Car_ID` with conflicting prices/mileage (the re-entry problem Part C.2 asks you to investigate), and mileage entries inflated by an extra digit. The notebook runs correctly on it end to end. **Swap in the real dealership CSV** (same filename, same column names) to get real numbers in every exercise answer — the notebook doesn't need any changes to do that.

## A real bug this run caught

The lab guide's own code uses `df['Price ($)'].fillna(df['Price ($)'].mean(), inplace=True)`. On a recent pandas version (Copy-on-Write enabled — the default from pandas 3.0 onward), that exact pattern silently does nothing: `df['Price ($)']` hands back a copy, not the live column, so the in-place fill never reaches the real DataFrame. Running the notebook caught this directly — the "should now read 0" verification step still showed 25 missing values after the fillna ran without error. It's the exact symptom the lab's own troubleshooting section warns about ("`fillna()` ... not working silently").

The fix, used throughout this notebook instead of the guide's literal syntax:
```python
df['Price ($)'] = df['Price ($)'].fillna(df['Price ($)'].mean())
```
Reassigning the result back to the column works regardless of pandas version. The same fix is applied to the Mileage median fill in Part D. If you're on an older pandas without Copy-on-Write, the guide's original `inplace=True` syntax would have worked too — but the reassignment form works everywhere, so there's no reason to risk it.

## What the notebook does

| Part | What happens |
|---|---|
| A | Load the dataset (Colab Drive or local path, auto-detected) |
| B | Find and fill missing `Price ($)` values — mean, justified by the distribution's shape |
| C | Exact duplicate rows removed; duplicate `Car_ID`s with conflicting details investigated and left for the dealership, not silently resolved |
| D | Mileage outliers (impossible six-figure-plus readings) found, blanked, and re-filled with the median |
| E | Data types enforced; `Sold_Yes_No` binary-encoded, `Fuel Type`/`Transmission` label-encoded, `Owner History` one-hot encoded |
| F | Cleaned dataset saved as `Cleaned_Car_Sales_Dataset.csv` |

Every "Exercise" question from the lab guide is answered in a markdown cell next to the relevant code, using the actual numbers from this run.

## Repo contents

```
Car_Sales_Cleaning.ipynb      the notebook, with outputs from a real run
data/
  Car_Sales_Dataset.csv       synthetic stand-in matching the lab's schema — replace with the real file
requirements.txt
```

## Running it

### VS Code

1. Install the Python and Jupyter extensions if you don't have them.
2. Clone the repo and open the folder in VS Code.
3. Create a virtual environment and install dependencies:
   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```
4. Open `Car_Sales_Cleaning.ipynb`, select the `.venv` interpreter as the kernel, and Run All.

The notebook detects that it isn't running in Colab and automatically reads `data/Car_Sales_Dataset.csv` — no path editing needed.

### Google Colab

1. Create a `data_science` folder in your Google Drive (if you don't have one already) and upload `Car_Sales_Dataset.csv` there — swap in the real dealership file if you have it.
2. Upload the notebook to Colab (File → Upload notebook), or open it directly from GitHub.
3. Runtime → Run all. It mounts Drive and reads from `MyDrive/data_science/Car_Sales_Dataset.csv` automatically, and saves `Cleaned_Car_Sales_Dataset.csv` back to the same folder.

## Tools

Python, pandas.
