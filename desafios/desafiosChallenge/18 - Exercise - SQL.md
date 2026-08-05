# 🎯 Desafio 02 — `CASE WHEN`

### 📌 O Problema de Negócio
O time de People Analytics precisa categorizar as vagas de **Data Scientist** publicadas em **2023** para mapear o nível de senioridade salarial e a flexibilidade do modelo de trabalho.

---

### 🎙️ Apresentação Executiva (Relatório ao Chefe)

1. **Classificação por Senioridade Salarial:** Normalizamos os salários em três categorias lógicas (`Junior/Pleno`, `Senior` e `Lead/Specialista`). Essa estrutura permite segmentar a distribuição salarial no mercado sem depender de strings não padronizadas nos títulos.
2. **Distribuição da Modalidade:** A flag booleana de home office foi convertida na dimensão `Modelo_Trabalho` (`Remote 100%` vs `Presencial/Hibrido`), permitindo analisar no BI se posições de liderança/especialista mantêm a oferta remota.
3. **Disponibilidade para BI:** Os dados foram limpos e categorizados na camada de SQL, eliminando qualquer necessidade de transformação adicional no Looker Studio ou Power BI.

---

### 💻 Solução em SQL (DuckDB / MotherDuck)

```sql
SELECT 
    job_id AS id_Trabalho,
    job_title_short AS Titulo_Trabalho,
    salary_year_avg AS Media_Salarial,
    CASE
        WHEN salary_year_avg < 110000 THEN 'Junior/Pleno'
        WHEN salary_year_avg BETWEEN 110000 AND 160000 THEN 'Senior'
        WHEN salary_year_avg > 160000 THEN 'Lead/Specialista'
        ELSE 'Unkown'
    END AS Categorias_Senioridades,
    CASE
        WHEN job_work_from_home IS TRUE THEN 'Remote 100%'
        ELSE 'Presencial/Hibrido'
    END AS Modelo_Trabalho
FROM job_postings_fact
WHERE job_title_short = 'Data Scientist'
  AND salary_year_avg IS NOT NULL
  AND YEAR(job_posted_date) = 2023
ORDER BY salary_year_avg DESC;
