# Day 3 — Numeric Aggregation & Derived Metrics (SQL)

This file captures **Day 3 learning** focused on numeric aggregation and analyst-level metric derivation. The emphasis is on **how metrics are calculated**, not just SQL syntax.

---

## Dataset Used

```text
Table: defects
Columns:
- defect_id
- module
- severity
- status
- bug_fix_hours
```

---

## 1. SUM — Total Effort Analysis

**Use case:** Total effort spent per module.

```sql
SELECT
  module,
  SUM(bug_fix_hours) AS total_hours
FROM defects
GROUP BY module;
```

**Business meaning:** Which module consumes the most engineering effort.

---

## 2. AVG — Average Fix Time (Exploratory)

```sql
SELECT
  module,
  AVG(bug_fix_hours) AS avg_hours
FROM defects
GROUP BY module;
```

⚠️ Used only for **quick exploration**, not final metrics.

---

## 3. COUNT + SUM Together

**Use case:** Volume vs effort comparison.

```sql
SELECT
  module,
  SUM(bug_fix_hours) AS total_hours,
  COUNT(*) AS defect_count
FROM defects
GROUP BY module;
```

---

## 4. Derived Metric: Average Fix Hours per Defect

**Preferred method (explicit calculation):**

```sql
SELECT
  module,
  SUM(bug_fix_hours) * 1.0 / COUNT(*) AS avg_fix_hours_per_defect
FROM defects
GROUP BY module;
```

### Why SUM / COUNT is preferred over AVG
- Gives control over joins and filters
- Avoids hidden duplication issues
- Easier to explain to stakeholders
- Safer for production dashboards

---

## Analyst Interpretation Example

If a module has:
- High average fix hours
- Low defect count

This usually indicates:
- Higher complexity or business criticality
- Better quality control despite complexity

---

## Key Takeaways

- Prefer `SUM / COUNT` for business metrics
- Avoid blind use of `AVG()` in production
- Always alias derived metrics
- Guard against integer division by multiplying with 1.0



