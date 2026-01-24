# SQL JOINs — Fundamentals, Pitfalls, and Edge Cases

This document consolidates **Day 4 and Day 5 learning** on SQL JOINs. It is written from an **interview + real-world analytics perspective**, focusing on *why* JOINs behave the way they do, not just syntax.

---

## 1. INNER JOIN — Core Concept

### Definition
`INNER JOIN` returns **only rows that have matching keys in both tables**.

### Example
```sql
SELECT d.defect_id, m.module_name, d.severity
FROM defects d
INNER JOIN modules m
  ON d.module_id = m.module_id;
```

### Key Points
- Rows without a match in either table are excluded
- Use when **both sides are mandatory**
- Default choice when requirements don’t mention missing data

---

## 2. LEFT JOIN — Core Concept

### Definition
`LEFT JOIN` returns **all rows from the left table**, and matching rows from the right table. If no match exists, right-table columns become `NULL`.

### Example
```sql
SELECT m.module_name, d.defect_id, d.severity
FROM modules m
LEFT JOIN defects d
  ON m.module_id = d.module_id;
```

### Key Points
- Left table rows are always preserved
- `NULL` values indicate missing or non-matching data
- Commonly used for gap analysis and data quality checks

---

## 3. Choosing the LEFT Table (Critical Rule)

> The table whose rows must **always appear in the output** goes on the LEFT side of a LEFT JOIN.

Example requirement:
> “Show all modules, along with any defects they have”

- `modules` → LEFT table
- `defects` → RIGHT table

---

## 4. Using NULLs Intentionally

LEFT JOIN is often used **to surface NULLs**, not avoid them.

### Use Cases
- Modules with no defects
- Customers with no orders
- Products with no sales

### Pattern
```sql
SELECT m.module_name
FROM modules m
LEFT JOIN defects d
  ON m.module_id = d.module_id
WHERE d.defect_id IS NULL;
```

This finds **modules with zero defects**.

---

## 5. WHERE vs ON in LEFT JOIN (MOST IMPORTANT)

### Key Difference
- `ON` → controls **how rows are matched**
- `WHERE` → filters **final result rows**

---

## 6. LEFT JOIN Filtering Trap

### ❌ Incorrect (Breaks LEFT JOIN)
```sql
SELECT m.module_name, d.defect_id
FROM modules m
LEFT JOIN defects d
  ON m.module_id = d.module_id
WHERE d.severity = 'High';
```

**Problem:**
- `WHERE` removes rows where `d.severity` is `NULL`
- LEFT JOIN behaves like INNER JOIN

---

### ✅ Correct (Preserves LEFT JOIN)
```sql
SELECT m.module_name, d.defect_id, d.severity
FROM modules m
LEFT JOIN defects d
  ON m.module_id = d.module_id
 AND d.severity = 'High';
```

**Result:**
- All modules remain
- Only High severity defects appear
- Other defects become `NULL`

---

## 7. ON-Clause Filtering Behavior (Important Insight)

If a module has defects, but none meet the ON-condition:
- The module still appears
- Right-table columns become `NULL`

This means:
> “The row exists, but does not meet the condition.”

---

## 8. Combining LEFT JOIN with NULL Logic (Advanced)

### Requirement
> Modules with **no defects** OR **High severity defects only**

```sql
SELECT m.module_name, d.defect_id, d.severity
FROM modules m
LEFT JOIN defects d
  ON m.module_id = d.module_id
WHERE d.defect_id IS NULL
   OR d.severity = 'High';
```

### Note
- This works, but is **fragile**
- ON-clause filtering is safer in production

---

## 9. Interview-Safe Rules (Memorize These)

1. INNER JOIN → when both sides are required
2. LEFT JOIN → when left table must be preserved
3. Filters on right table → prefer `ON`, not `WHERE`
4. `WHERE` removes rows; `ON` controls matching
5. NULLs are analytical signals, not errors

---

## Status
After this module, you understand:
- INNER vs LEFT JOIN deeply
- JOIN selection logic
- NULL behavior
- Real interview elimination traps

Next recommended topic: **Window Functions**

