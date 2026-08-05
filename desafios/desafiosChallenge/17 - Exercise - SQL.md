# 🎯 Desafio 17 — CTEs (`WITH ... AS`)

### 📌 O Problema de Negócio
Mapear as vagas de **Data Analyst** publicadas em 2023 que oferecem salários anuais acima da média geral da categoria naquele mesmo ano.

---

### 💻 Solução em SQL (DuckDB / MotherDuck)

```sql
WITH media_salarial AS (
    SELECT ROUND(AVG(salary_year_avg)) AS media_salario
    FROM job_postings_fact
    WHERE EXTRACT(YEAR FROM job_posted_date) = 2023
      AND job_title_short = 'Data Analyst'
      AND salary_year_avg IS NOT NULL
)

SELECT 
    job_id,
    job_title_short,
    ROUND(salary_year_avg) AS Vagas_De_Alta_Remuneracao
FROM job_postings_fact
WHERE job_title_short = 'Data Analyst'
  AND EXTRACT(YEAR FROM job_posted_date) = 2023
  AND salary_year_avg > (SELECT media_salario FROM media_salarial)
ORDER BY salary_year_avg;
---
