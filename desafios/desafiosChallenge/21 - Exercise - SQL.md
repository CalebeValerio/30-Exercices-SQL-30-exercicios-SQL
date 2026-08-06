# 🎯 Desafio 21 — Relacionamentos Muitos-para-Muitos (N:N com 3 Tabelas)

### 📌 O Problema de Negócio
O time de Treinamento e Desenvolvimento precisa identificar as **5 habilidades técnicas mais exigidas** para vagas de **Data Analyst** em **2023** que oferecem salários anuais **superiores a $100.000**.

---

### 🎙️ Apresentação Executiva (Relatório ao Chefe)

1. **Cruzamento em Ponte (N:N):** Conectamos a tabela de fatos de vagas (`job_postings_fact`) à dimensão de habilidades (`skills_dim`) por meio da tabela ponte (`skills_job_dim`), garantindo o mapeamento de múltiplas skills por anúncio.
2. **Filtro de Escopo e Senioridade:** O recorte focado em vagas acima de $100k isolou as competências de alta relevância e maior impacto financeiro no mercado de análise de dados.
3. **Tomada de Decisão em T&D:** A ordenação do volume de ocorrências por skill entrega a matriz exata de tecnologias prioritárias (ex: SQL, Python) para direcionar capacitações internas.

---

### 💻 Solução em SQL (DuckDB / MotherDuck)

```sql
SELECT 
    s.skills,
    COUNT(j.job_id) AS Total_Vagas
FROM job_postings_fact AS j
INNER JOIN skills_job_dim AS sj 
    ON j.job_id = sj.job_id
INNER JOIN skills_dim AS s 
    ON sj.skill_id = s.skill_id
WHERE j.job_title_short = 'Data Analyst'
  AND j.salary_year_avg > 100000
  AND YEAR(j.job_posted_date) = 2023
GROUP BY s.skills
ORDER BY Total_Vagas DESC
LIMIT 5;
