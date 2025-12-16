# Validation Tool (Iteration 1)

A lightweight React + TypeScript app to validate tabular data against field rules defined in `audit-fields-rules.csv`.

## Quick Start

- Requirements: Node 18+, npm
- Install and run the app:

```zsh
git clone https://github.com/telecasteren/validation-tool.git
cd validation-tool
npm install
npm run dev
```

Open the local URL printed by Vite. The UI lets you upload a data file (CSV/XLSX) for validation. Rules are loaded from `audit-fields-rules.csv` at the repo root. Potential SQL mode is stubbed for later iteration.

## How It Works

- Rules source: `audit-fields-rules.csv` (semicolon-delimited) at the repo root. Required headers: `Field;Type;Required;ExposeInReporting;Description`.
- Supported types (case-insensitive): `string`, `int`, `int?`, `bool`, `bool?`, `DateTime`, `DateTime?`, `ObjectId`, `ObjectId?`, `List<...>`, `Dictionary<...>`, `dynamic`.
- Required flag: `required` vs `optional` (string values in the CSV).
- Upload flow:
  1.  Validate Data: upload `.csv` (semicolon-delimited, headers) or `.xlsx` (first sheet) file to validate.
- Errors shown per row with human-readable messages plus a summary that aggregates repetitive errors and lists affected rows.

## Architecture

- UI
  - `src/ui/App.tsx`: main app layout (Rules loaded from file → Mode selector → Validate Data → Results).
  - `src/ui/UploadArea.tsx`: file input; parses CSV via PapaParse and XLSX via SheetJS.
  - `src/ui/FieldInfo.tsx`: lists all fields with types/required/description. Visibility is toggled in the UI.
  - `src/ui/Results.tsx`: per-row errors and aggregated summary.
  - `src/ui/ModeSelector.tsx`: choose Upload or SQL (SQL disabled placeholder for now).
  - `src/ui/styles.css`: centralized styles (no inline layout/styles in components).

- Validation
  - `src/validation/RulesLoader.ts`: parses the semicolon-delimited rules CSV into an internal model.
  - `src/validation/Validator.ts`: validates each row against the rules.
    - Checks: missing required values, type mismatches, unknown fields.
    - Aggregates repetitive errors: shows count + row numbers.
  - `src/validation/types.ts`: shared types for rules and results.

## File Formats

- Rules CSV (semicolon `;` delimiter):

```
Field;Type;Required;ExposeInReporting;Description
CaseNumber;string;optional;YES;Human readable case number
...
```

- Data CSV: header row required; semicolon `;` delimiter is expected. `.xlsx` is also supported (first sheet used, first row as header).

## Maintaining Rules

- Source of truth: `audit-fields-for-validation-csv.csv` in the repo root. To update rules, modify this file and restart the app if needed.
- The rules loader validates headers and reports load errors (e.g., missing columns, empty file).

## Commands

From `app/`:

```zsh
# Start dev server
npm run dev

# One-off format and lint
npm run format
npm run lint
```

If you encounter peer-dependency issues during install, use:

```zsh
npm install --legacy-peer-deps
```

## Notes

- SQL mode is a UI stub for later iterations; no backend calls yet.
- ObjectId validation expects 24-hex characters.
- Lists/Dictionaries accept common representations (arrays/JSON, comma- or key=value;key=value strings) and are validated permissively.
