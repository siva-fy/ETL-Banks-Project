# Banking ETL · from a web table to SQL

A Python learning project that extracts bank market capitalizations from an
archived Wikipedia page, converts USD values into GBP, EUR and INR, and writes
the results to CSV and SQLite.

## Pipeline

```text
Archived HTML + exchange_rate.csv
              ↓
BeautifulSoup extraction → pandas / NumPy currency conversion
              ↓
Largest_banks_data.csv + Banks.db → example SQL queries
```

## Run locally

Use Python 3 and run the commands from the repository root. The extraction step
requires access to the Internet Archive.

```bash
git clone https://github.com/siva-fy/ETL-Banks-Project.git
cd ETL-Banks-Project
python3 -m venv .venv
source .venv/bin/activate
python -m pip install requests beautifulsoup4 pandas numpy
python banks_project.py
```

On Windows, activate with `.venv\Scripts\activate` instead.

The script prints the extracted and transformed data, the SQL query results and
the progress log. A run overwrites `Largest_banks_data.csv`, replaces the
`Largest_banks` table in `Banks.db`, and appends to `code_log.txt`. Work in a fresh
clone if you want to retain the checked-in sample outputs.

## What to inspect

| File | Purpose |
| --- | --- |
| [`banks_project.py`](banks_project.py) | Extraction, transformation, loading, logging and sample queries |
| [`exchange_rate.csv`](exchange_rate.csv) | Currency multipliers used by the transformation |
| [`Largest_banks_data.csv`](Largest_banks_data.csv) | Checked-in sample output |
| [`Banks.db`](Banks.db) | SQLite output with the `Largest_banks` table |

The SQL examples list all banks, compute average market capitalization in GBP,
and show the first five bank names in the extracted order. For a reproducible
ranking, use an explicit ordering:

```sql
SELECT Name, MC_USD_Billion
FROM Largest_banks
ORDER BY MC_USD_Billion DESC
LIMIT 5;
```

## Scope and limitations

- This is an ETL exercise using a September 2023 source snapshot and local exchange
  rates, not a live financial dataset.
- HTML extraction depends on table and link positions. A changed or unavailable
  archive response can break parsing.
- The current script has no HTTP timeout/retry policy, input validation or automated
  tests; dependencies are not pinned. It also runs at import time.
- The checked-in outputs show a historical run. The live extraction has not been
  revalidated as part of this documentation update.

For a tested, offline example with deduplication and quarantine, see
[Event Warehouse](https://github.com/siva-fy/event-warehouse).
