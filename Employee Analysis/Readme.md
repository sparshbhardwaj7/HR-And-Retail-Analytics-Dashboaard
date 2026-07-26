# Employee Analysis Dashboard

## Project overview

Employee Analysis is a Power BI dashboard that combines employee records from two regional offices (South Asia and Middle East) with company-wide training data, to explore headcount, performance, competency, and training ROI across departments, locations, and nationalities.

> **About the dataset**
> Built from three exercise workbooks — two "data appending" files (Middle East Office, South Asia Office) with matching employee-record structures, and one "data merging" file joining training-impact and training-initiatives data on Employee ID. This is classroom exercise data with intentional data-quality issues (see below), not real company records.

## Business objectives

The dashboard answers the following questions:

1. How is headcount distributed across departments, office locations, and nationalities in each region?
2. How do average performance score and competency compare between the South Asia and Middle East offices?
3. How much monthly revenue is each office and department generating?
4. Which training type (on the job, mentorship, outsourced vendor) delivers the best ROI and new-client acquisition?
5. Does performance score improve from one quarter to the next after training initiatives?
6. Where are the data-quality gaps (missing values, mismatched formats) that need fixing before the two offices can be reliably compared or combined?

## Dashboard pages

- **Employee Directory** — company-wide view: total employees, department/job-role breakdown, age-group distribution, and current vs. previous performance score by department, alongside training type, ROI, and sessions attended.
- **Middle East Office** — total employees, average performance score, average competency, and total monthly revenue for the Middle East office, with breakdowns by department, office location (Dubai, Oman, Sharjah), and nationality.
- **South Asia Office** — the same KPI set and breakdowns for the South Asia office, across offices in Bhutan, Bangladesh, and Nepal.

## Data sources

| File | Contents | Rows × Cols |
|---|---|---|
| `Data_Appending_Exercise_-_Middle_East_Office.xlsx` | Employee records for the Middle East office (Dubai, Oman, Sharjah) | 100 × 11 |
| `Data_Appending_Exercise_-_South_Asia_Office.xlsx` | Employee records for the South Asia office (Bhutan, Bangladesh, Nepal) | 100 × 11 |
| `Data_Merging_Exercise.xlsx` | Two sheets — **Training impact** (215 rows) and **Training initiatives** (147 rows) — joined on Employee ID | 2 sheets |

Both office files share the same fields: Employee ID, Department, Months of Experience, Leaves Taken, Office Location, Nationality, Work-life Balance, Total Sick Days, Competency, Performance Score, and Monthly Revenue Generated.

## Data quality notes

The source files carry realistic data-quality issues, intentional to the exercise:

- **South Asia file:** the last 35 rows (Employee IDs 66–97) have no Work-life Balance, Total Sick Days, or Monthly Revenue values — likely recent joiners without a full quarter of data.
- **Middle East file:** the last ~35 rows have non-integer Employee IDs and a steadily escalating Monthly Revenue pattern — worth validating before treating as clean transactional data.
- **Inconsistent headers:** column names differ slightly between the two office files (e.g. `Performance score` vs. `Performance Score`, trailing spaces/newlines in header text), so they need normalizing before appending.
- **Employee ID formats differ** between the office files (numeric) and the training files (alphanumeric, e.g. `16BSPHH01C0012`) — there's no direct join key between office headcount and training records.

## Key insights

### Headcount and offices

- Each office file holds **100 employees**. South Asia is led by **Administration (14)**; Middle East is led by **Finance (18)**.
- South Asia's busiest location is **Bhutan (43 employees)**, followed by Bangladesh (29) and Nepal (28). Middle East's busiest is **Sharjah (39)**, followed by Oman (31) and Dubai (30).
- **India is the largest nationality group in both offices (40 employees each)** — Middle East's next-largest groups are Sri Lanka (27) and Indonesia (19); South Asia's are Indonesia (14) and the Philippines (13).

### Performance and competency

- South Asia's average performance score is **3.5**, versus **3.2** for Middle East's 65 cleanly-recorded employees — South Asia also edges ahead on average competency (**3.19** vs. **2.80**).
- At the training-initiatives level, average performance rose from **3.64 (last quarter) to 3.79 (current quarter)** across the 147 tracked employees — a modest improvement following training investment.

### Revenue

- Middle East's 65 employees with valid (integer) IDs and South Asia's 100 employees both total **₹7,85,405** in recorded monthly revenue — the two office files appear to share the same underlying revenue template, just relabeled by office.
- The remaining **35 Middle East rows carry non-integer IDs and an escalating revenue pattern (up to ₹2.1 Lakh/month)** that looks synthetic rather than real — see [Data quality notes](#data-quality-notes) before using Middle East's revenue total as-is.

### Training

- Of 215 training records, **"On the job" is the dominant training type (154, ~72%)**, followed by outsourced vendor (40) and mentorship (21).
- **Outsourced vendor training shows the highest average new-client acquisition (12.1)**, ahead of mentorship (11.2) and on-the-job (11.0).
- **Research & Development has by far the highest average Training ROI (₹82,353)**, well above Human Resources (₹43,794) and Sales (₹26,243) — largely driven by high-cost R&D roles like Manufacturing Director and Research Director.

## Core DAX measures

These match the fields the report's cards, charts, and tables are actually built on (read from the visuals' query definitions — Power BI stores the compiled data model in a binary format, so treat these as representative measures rather than an exact export):

```DAX
Total Employees =
COUNT('Office Table'[Employee ID])
```

```DAX
Avg Performance Score =
AVERAGE('Office Table'[Performance Score])
```

```DAX
Average Competency =
AVERAGE('Office Table'[Competency])
```

```DAX
Sum of Monthly Revenue Generated =
SUM('Office Table'[Monthly Revenue Generated])
```

```DAX
Count of Employee ID by Nationality =
CALCULATE(
    COUNT('Office Table'[Employee ID]),
    ALLEXCEPT('Office Table', 'Office Table'[Nationality])
)
```

```DAX
Training ROI (avg) =
AVERAGE('Training impact'[Training ROI])
```

```DAX
New Clients (Post-Training) =
SUM('Training impact'[New client acquisition post-training])
```

```DAX
Current vs Previous Performance Score =
AVERAGE('Training initatives'[Current Quarter Performance Score])
    - AVERAGE('Training initatives'[Last Quarter Performance Score])
```


## Repository structure

```text
employee-analysis/
├── README.md
├── Employee_Analysis.pbix
└── Dataset/
    ├── Data_Appending_Exercise_-_Middle_East_Office.xlsx
    ├── Data_Appending_Exercise_-_South_Asia_Office.xlsx
    └── Data_Merging_Exercise.xlsx
```

## How to use the dashboard

1. Download or clone this repository.
2. Open `Employee_Analysis.pbix` in Power BI Desktop.
3. If Power BI reports a missing data source, point it to the files in `Dataset/`.
4. Refresh the model, then use the slicers (Department, Nationality, Training Type) to filter each page.

## Tools and technologies

- **Microsoft Power BI** for data modeling and dashboard design
- **Power Query** for appending and merging the source workbooks
- **DAX** for KPI measures (headcount, average performance, average competency, revenue totals)
- **GitHub** for version control and project documentation

## Future improvements

- Add a shared key between the office headcount tables and the training tables so headcount, performance, and training ROI can be analyzed together instead of on separate pages.
- Investigate and clean the 35 anomalous Middle East rows (non-integer Employee IDs, escalating revenue) before including them in revenue KPIs.
- Backfill or clearly flag the 35 missing South Asia rows (Work-life Balance, Sick Days, Monthly Revenue) rather than leaving them blank.
- Normalize column headers and casing across the two office files (e.g. `Performance score` vs. `Performance Score`) before any future append.
- Add quarter-over-quarter and department-level trend measures for performance score, not just a single current-vs-previous comparison.
- Add a training-type-by-department cross analysis to see which training approach works best per function.
- Publish to Power BI Service and link the published report here.

## Author

**Sparsh Bhardwaj**
B.Tech CSE (Data Science), Bharati Vidyapeeth's College of Engineering (BVCOE)

