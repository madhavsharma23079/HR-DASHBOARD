# HR Analytics Dashboard – Employee Attendance (Power BI)

An end-to-end HR analytics project. Raw, messy monthly attendance sheets are **cleaned and transformed dynamically with Power Query**, modelled in Power BI, and presented in an interactive **HR Analytics Dashboard** that helps HR understand how employees attend, work remotely and take leave.

---

## 📌 Project Overview

| Item | Details |
|---|---|
| **Goal** | Give HR a single view of employee attendance, WFH and sick-leave patterns |
| **Tool** | Microsoft Power BI Desktop (Power Query + DAX) |
| **Source file** | `Attendance-Sheet-2022-2023.xlsx` |
| **Output file** | `hr_dashboard.pbix` |
| **Period covered** | April 2022 – June 2022 (one sheet per month) |
| **Company** | AtliQ |

---

## 📂 Repository Contents

```
├── Attendance-Sheet-2022-2023.xlsx   # Raw monthly attendance data + attendance key
├── hr_dashboard.pbix                 # Power BI report (queries, data model, dashboard)
└── README.md
```

---

## 🗂️ About the Raw Data

The Excel workbook has **4 sheets**:

- `Apr 2022`, `May 2022`, `June 2022` – one row per employee, one column per date, each cell holds an attendance code.
- `Attendance Key` – the legend explaining every code.

**Attendance codes used**

| Code | Meaning | Code | Meaning |
|---|---|---|---|
| P | Present | WFH / HWFH | Work from home / Half-day WFH |
| PL / HPL | Paid leave / Half-day PL | SL / HSL | Sick leave / Half-day SL |
| FFL / HFFL | Floating festival leave / Half-day | BL | Birthday leave |
| BRL / HBRL | Bereavement leave / Half-day | ML / HML | Menstrual leave / Half-day |
| LWP / HLWP | Leave without pay / Half-day | WO | Weekly off |
| HO | Holiday off | | |

### Problems in the raw data (why cleaning was needed)

- Data is in a **wide / cross-tab layout** (dates as columns) – unsuitable for analysis and charts.
- **Three separate sheets** (one per month) with the same structure.
- Header row contains **formulas** (`=C1`, `=D1`, …) that duplicate the date row.
- Month sheets include **spill-over dates** from the next month (e.g. 1 May in the April sheet).
- Extra **summary columns** (Total Present Days, Present, WFH, Paid Leave, etc.) sitting beside the daily data.
- **~900+ empty rows** per sheet below the employee list.
- **Inconsistent text** – trailing spaces in column names and codes (e.g. `"Employee Code "`, `"Floting festival leave "`) and a typo ("Floting").
- Employee codes repeated across rows.

---

## 🧹 Data Cleaning & Processing with Power Query (Dynamic)

All cleaning is done inside **Power Query**, so nothing is edited manually in Excel. When new months are added or the data changes, a single **Refresh** re-applies every step automatically – this is what makes the pipeline *dynamic*.

### Steps applied

1. **Connect to the Excel workbook** and load each month sheet.
2. **Promote headers / remove the helper header row** (the formula row `=C1…`).
3. **Remove blank rows and null employee entries** so only real employee records remain.
4. **Remove summary columns** (Total Present Days, Present, WFH, …) – these are recalculated in DAX instead.
5. **Trim & clean text** to fix trailing spaces and inconsistent names.
6. **Unpivot the date columns** – converts the wide layout into a tidy table with three core columns: `Name` · `Dates` · `Value` (attendance code). This is the key transformation: new date columns are picked up automatically because the unpivot is applied to "other columns".
7. **Append the monthly queries** into one combined table, so adding another month's sheet extends the dataset without rebuilding anything.
8. **Set correct data types** – dates as Date, codes/names as Text.
9. **Remove duplicates / filter invalid dates** (e.g. spill-over dates outside the sheet's month).
10. **Add derived columns** used by the dashboard:
    - `Month`
    - `DAY OF THE WEEK`
11. **Load the result as `final data`** – the single fact table powering all visuals.

> 💡 Because the steps are recorded as a query, the same logic runs again for any new data – no repeated manual cleanup.

---

## 🧮 Data Model & DAX Measures

A dedicated **`Measure Table`** holds the KPIs, calculated on top of `final data`:

| Measure | Description |
|---|---|
| `present%` | Share of working days employees were present |
| `wfh%` | Share of working days spent working from home |
| `sl%` | Share of working days lost to sick leave |

(Weekly-off and holiday codes are excluded from the denominator so percentages reflect working days only; half-day codes count as 0.5.)

---

## 📊 The HR Dashboard

A single-page, 1280×720 **HR ANALYTICS DASHBOARD** designed for HR managers.

**KPI cards**
- Present %
- WFH %
- Sick Leave %

**Visuals**
- **Employee table** – each employee with their Present %, WFH % and SL %.
- **Attendance matrix (heatmap)** – employees (rows) × dates (columns) showing the daily attendance code.
- **Trend area charts** – Present %, WFH % and SL % over time.
- **Day-of-week table** – shows which weekdays have higher WFH or sick leave.

**Interactive slicers**
- Month
- Day of the week

All visuals cross-filter, so HR can click an employee, month or weekday and everything updates.

### What HR can learn from it
- Which employees have unusually high sick leave or WFH rates.
- Overall attendance trend across months.
- Weekday patterns (e.g. are Mondays/Fridays busier for WFH or leave?).
- Individual attendance history day by day.

---

## 🚀 How to Use

1. Download `hr_dashboard.pbix` and `Attendance-Sheet-2022-2023.xlsx`.
2. Open the `.pbix` in **Power BI Desktop**.
3. If prompted, go to **Home → Transform data → Data source settings** and point the source to your local copy of the Excel file.
4. Click **Refresh** to reload and re-clean the data.
5. Use the slicers and click visuals to explore.

To add new data: add a new month sheet in the same format (or append to the workbook) → **Refresh**.

---

## 🛠️ Skills Demonstrated

- Power Query (M): unpivot, append, type conversion, null/duplicate handling, text cleaning
- Data modelling in Power BI
- DAX measures (percentage KPIs)
- Dashboard design & interactive storytelling
- HR / workforce analytics

---

## 📝 Notes & Future Improvements

- Extend the dataset to cover the full 2022–2023 year.
- Add an employee/department dimension table (department, manager, location).
- Add attrition and leave-balance analysis.
- Publish to Power BI Service with scheduled refresh.

