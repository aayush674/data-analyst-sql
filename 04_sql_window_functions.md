# SQL Window Functions — Structured Notes for Data Analyst

## 1. Why Window Functions Are Needed

In analytics, many questions require:
- Comparing rows within a group
- Tracking change over time
- Ranking or ordering records

Traditional `GROUP BY`:
- Aggregates data
- Reduces row count

Window functions:
- Perform calculations **across related rows**
- **Do not reduce** the number of rows

They are used when **row-level detail must be preserved**.

---

## 2. The Window Concept (Foundation)

A *window* is a **set of rows related to the current row**.

Window functions operate using three independent components:

- `PARTITION BY` → divides data into logical groups
- `ORDER BY` → defines the sequence within each group
- Window function → evaluates over a window of rows

Important clarification:
- `PARTITION BY` does **not** create row-by-row behavior
- `ORDER BY` is what enables **progression across rows**

---

## 3. Default Window Behavior (Critical to Understand)

When `ORDER BY` is used inside `OVER()`:

- The window starts at the **first row of the partition**
- The window extends up to the **current row**

Conceptually:
```
UNBOUNDED PRECEDING → CURRENT ROW
```

This explains:
- Running totals
- Cumulative metrics

---

## 4. ROW_NUMBER()

### Purpose
Assigns a **unique sequential number** to rows within each partition.

### Behavior
- Numbers are based on row position
- Ties still receive different numbers

### Typical Use Case
- Selecting **exactly one row per group**

### Example
```sql
ROW_NUMBER() OVER (
  PARTITION BY module
  ORDER BY created_date DESC
)
```

---

## 5. RANK()

### Purpose
Assigns the **same rank to tied rows**.

### Behavior
- Rows with equal ordering values share the same rank
- Rank numbers may be skipped

### Typical Use Case
- Selecting **all top records**, including ties

### Example
```sql
RANK() OVER (
  PARTITION BY module
  ORDER BY created_date DESC
)
```

---

## 6. Choosing Between ROW_NUMBER and RANK

| Requirement | Function |
|------------|----------|
| Exactly one row per group | ROW_NUMBER |
| Include all tied rows | RANK |

This decision is **requirement-driven**, not preference-driven.

---

## 7. Filtering Window Function Results

Important execution rule:

> Window functions are evaluated **after WHERE**.

Therefore:
- They cannot be used directly in `WHERE`
- A subquery or CTE is required

### Canonical Pattern
```sql
SELECT *
FROM (
  SELECT
    defect_id,
    module,
    created_date,
    ROW_NUMBER() OVER (
      PARTITION BY module
      ORDER BY created_date DESC
    ) AS rn
  FROM defects
) t
WHERE rn = 1;
```

---

## 8. Running Totals (Cumulative Metrics)

### Purpose
Track how a value **accumulates over time**.

### Key Insight
- Accumulation happens because the **window grows per row**
- Not because the function behaves differently per row

### Example
```sql
SUM(bug_fix_hours) OVER (
  PARTITION BY module
  ORDER BY created_date
)
```

This produces a cumulative sum within each partition.

---

## 9. LAG() — Previous Row Access

### Purpose
Access a value from the **previous row** in the window.

### Behavior
- First row per partition returns `NULL`

### Example
```sql
LAG(bug_fix_hours) OVER (
  PARTITION BY module
  ORDER BY created_date
)
```

### Typical Use Cases
- Change over time
- Trend analysis

---

## 10. LEAD() — Next Row Access

### Purpose
Access a value from the **next row** in the window.

### Example
```sql
LEAD(bug_fix_hours) OVER (
  PARTITION BY module
  ORDER BY created_date
)
```

### Typical Use Cases
- Forward comparison
- Planning and forecasting logic

---

## 11. Difference Calculation Pattern

```sql
bug_fix_hours
- LAG(bug_fix_hours) OVER (
    PARTITION BY module
    ORDER BY created_date
  ) AS hours_diff
```

Used to compute:
- Day-over-day change
- Incremental impact

---

## 12. Key Takeaways 

- Window functions preserve row-level data
- `PARTITION BY` controls scope, not sequence
- `ORDER BY` enables progression across rows
- `ROW_NUMBER` and `RANK` solve different requirements
- `LAG` and `LEAD` replace many self-joins

