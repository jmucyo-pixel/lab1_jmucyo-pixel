# Grade Evaluator & Archiver

A Python application that evaluates a student's academic standing from a CSV file of course grades, paired with a Bash shell script that archives and resets the grade data.

---

## Project Structure

```
project/
├── grade-evaluator.py   # Python grade evaluation script
├── organizer.sh         # Bash archiving script
├── grades.csv           # CSV file containing course grades
├── archive/             # Created automatically by organizer.sh
└── organizer.log        # Created automatically by organizer.sh
```

---

## Requirements

- Python 3.x
- Bash (Linux/macOS or Git Bash on Windows)

---

## grades.csv Format

The CSV file must have the following column headers and structure:

```
assignment,group,score,weight
Quiz,Formative,85,20
Group Exercise,Formative,40,20
Functions and Debugging Lab,Formative,45,20
Midterm Project - Simple Calculator,Summative,70,20
Final Project - Text-Based Game,Summative,60,20
```

**Column descriptions:**
- `assignment` — Name of the assignment
- `group` — Either `Formative` or `Summative`
- `score` — Raw score between 0 and 100
- `weight` — Weight of the assignment (Formative weights must sum to 60, Summative to 40)

---

## Running the Python Script

1. Make sure `grades.csv` is in the same directory as `grade-evaluator.py`
2. Run the script:

```bash
python grade-evaluator.py
```

3. When prompted, enter the filename:

```
Enter the name of the CSV file to process (e.g., grades.csv): grades.csv
```

**Example output:**

```
--- Processing Grades ---
=============================================
         GRADE EVALUATION REPORT
=============================================
  Formative Score : 34.00 / 60.00  ✓
  Summative Score : 26.00 / 40.00  ✓
  Final Grade     : 60.00 / 100.00
  GPA             : 3.00 / 5.00
  Status          : PASSED
=============================================

  Eligible for resubmission: Group Exercise, Functions and Debugging Lab
  (Failed formative(s) with highest weight: 20.0)
=============================================
```

**Error handling:**
- If `grades.csv` is not found, the program prints an error and exits
- If the CSV is empty, the program prints an error and exits
- If any score is outside the 0–100 range, the program prints an error and exits
- If weights do not meet the 60/40 split, the program prints an error and exits

---

## Running the Bash Script

The `organizer.sh` script archives the current `grades.csv` and resets the workspace for the next batch of grades.

1. Give the script execute permission (first time only):

```bash
chmod +x organizer.sh
```

2. Run the script:

```bash
bash organizer.sh
```

**What it does:**
- Creates an `archive/` directory if it does not already exist
- Renames `grades.csv` with a timestamp (e.g., `grades_20251105-170000.csv`) and moves it to `archive/`
- Creates a fresh empty `grades.csv` in the current directory
- Appends a log entry to `organizer.log`

**Example terminal output:**

```
Created archive directory.
Archived: grades.csv → archive/grades_20251105-170000.csv
Reset: fresh grades.csv created.
Logged to organizer.log.
```

**Example organizer.log entry:**

```
[20251105-170000] Original: grades.csv | Archived as: archive/grades_20251105-170000.csv
```

Each run appends a new line to `organizer.log`, building up a full history of all archiving operations.

---

## Pass/Fail Criteria

A student **PASSES** only if they score at or above 50% in **both** categories independently:

| Category | Total Weight | Minimum to Pass |
|---|---|---|
| Formative | 60 | 30 (50% of 60) |
| Summative | 40 | 20 (50% of 40) |

**GPA Formula:** `GPA = (Final Grade / 100) * 5.0`

---

## Resubmission Logic

After evaluation, the script checks for any failed formative assignments (raw score below 50%). The failed formative assignment(s) carrying the **highest weight** are flagged as eligible for resubmission. If multiple failed formatives share the same highest weight, all of them are listed.
