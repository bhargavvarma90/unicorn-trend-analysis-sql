🦄 Unicorn Trend Analysis – SQL Project

This SQL project analyzes unicorn company trends to help an investment firm identify the **top-performing industries** based on the creation of new unicorns and their valuations between **2019–2021**.

---

 💼 Business Objective

An investment firm wants to understand:
- Which **industries** are producing the most new unicorns
- In which **years** unicorns were created
- The **average valuation** of unicorns in those industries (in billions)

This insight allows the firm to make informed investment decisions and structure their portfolio for high-growth industries.

---


1. Identify the **top 3 industries** with the most unicorns created during **2019, 2020, and 2021**
2. For these industries, extract:
   - The **number of unicorns** per year
   - Their **industry and year joined**
   - The **average valuation** in **billions of dollars**, rounded to two decimal places
3. **Sort** results by **year** and **number of unicorns**, both in **descending order**

---

## 🛠️ SQL Logic

```sql
WITH top_industries AS (
    SELECT i.industry, 
           COUNT(i.*)
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    WHERE EXTRACT(year FROM d.date_joined) IN ('2019', '2020', '2021')
    GROUP BY industry
    ORDER BY count DESC
    LIMIT 3
),

yearly_rankings AS (
    SELECT COUNT(i.*) AS num_unicorns,
           i.industry,
           EXTRACT(year FROM d.date_joined) AS year,
           AVG(f.valuation) AS average_valuation
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    INNER JOIN funding AS f
        ON d.company_id = f.company_id
    GROUP BY industry, year
)

SELECT industry,
       year,
       num_unicorns,
       ROUND(AVG(average_valuation / 1000000000), 2) AS average_valuation_billions
FROM yearly_rankings
WHERE year IN ('2019', '2020', '2021')
  AND industry IN (SELECT industry FROM top_industries)
GROUP BY industry, num_unicorns, year
ORDER BY year DESC, num_unicorns DESC;
