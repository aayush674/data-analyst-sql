# SQL Fundamentals for Data Analyst (Topic‑Wise)

This document is organized **topic‑wise (not day‑wise)** so that recruiters and interviewers can quickly understand your SQL competence. It reflects **real interview patterns** and **production‑ready SQL**, suitable for a QA → Data Analyst transition.

---

## 1. Basic Data Retrieval

### SELECT & WHERE
```sql
SELECT defect_id, module, severity
FROM defects
WHERE status = 'Open';
```

**Purpose:** Fetch row‑level data using simple conditions.

---

### ORDER BY
```sql
SELECT defect_id, module, severity
FROM defects
WHERE status = 'Open'
ORDER BY created_date DESC;
```

**Purpose:** Sort results to identify latest or highest‑priority records.

---

### LIMIT / TOP‑N Analysis
```sql
SELECT defect_id, module, severity
FROM defects
ORDER BY defect_id DESC
LIMIT 2;
```

**Purpose:** Retrieve most recent or top‑N records.

---

## 2. Filtering Best Practices

### IN vs OR
```sql
SELECT defect_id, module, severity, status
FROM defects
WHERE module = 'Login'
  AND severity IN ('High', 'Critical');
```

**Why IN:** Improves readability and reduces logical errors. Performance is generally equivalent to `OR`.

---

## 3. Aggregation Fundamentals

### COUNT with GROUP BY
```sql
SELECT module, COUNT(*) AS defect_count
FROM defects
GROUP BY module;
```

**Purpose:** Summarize data at a group level.

---

## 4. WHERE vs HAVING (Critical Topic)

### Correct Use of WHERE (Row‑Level Filter)
```sql
SELECT module, COUNT(*) AS open_defect_count
FROM defects
WHERE status = 'Open'
GROUP BY module;
```

---

### Correct Use of HAVING (Group‑Level Filter)
```sql
SELECT module, COUNT(*) AS defect_count
FROM defects
GROUP BY module
HAVING COUNT(*) > 1;
```

**Rule:**
- `WHERE` → filters rows before aggregation
- `HAVING` → filters aggregated results

---

## 5. CASE Expressions (Conditional Logic)

### Row‑Level CASE
```sql
SELECT defect_id,
       CASE 
         WHEN status = 'Open' THEN 'Open Defect'
         ELSE 'Closed Defect'
       END AS defect_type
FROM defects;
```

**Purpose:** Transform data without filtering rows.

---

### Numeric Flag Using CASE
```sql
SELECT defect_id,
       CASE 
         WHEN status = 'Open' THEN 1
         ELSE 0
       END AS status_flag
FROM defects;
```

**Purpose:** Prepare data for conditional aggregation.

---

## 6. Conditional Aggregation (Advanced Fundamental)

### Multiple Metrics in One Query
```sql
SELECT
  module,
  COUNT(*) AS total_defects,
  COUNT(
    CASE 
      WHEN status = 'Open' THEN 1
      ELSE NULL
    END
  ) AS open_defects
FROM defects
GROUP BY module;
```

**Why This Matters:**
- Common real‑world reporting requirement
- Frequently tested in interviews
- Demonstrates strong analytical SQL skills

---

## Key Takeaways

- Prefer explicit column selection over `SELECT *`
- Use `WHERE` for row filters, `HAVING` for aggregated filters
- `CASE` works row‑by‑row; aggregation happens later
- `COUNT(column)` ignores NULL values
- Always alias aggregated columns


