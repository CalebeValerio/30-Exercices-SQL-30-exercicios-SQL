# 🎯 Desafio 19 — Window Functions (`DENSE_RANK()`)

### 📌 O Problema de Negócio
A diretoria executiva solicitou o mapeamento dos **3 maiores salários** oferecidos no ano de **2023** para **cada uma das profissões** cadastradas na base.

---

### 🎙️ Apresentação Executiva (Relatório ao Chefe)

1. **Top 3 por Cargo sem Omissão de Linhas:** O uso de `DENSE_RANK()` combinado com `PARTITION BY job_title_short` permitiu criar um ranking independente para cada profissão da base.
2. **Tratamento de Empates:** Diferente de um filtro comum com `LIMIT`, o `DENSE_RANK()` garante que, caso haja salários idênticos na 3ª posição, ambos sejam mantidos no relatório sem pular a sequência numérica.
3. **Métrica Pronta para Dashboard:** O dataset resultante entrega o recorte exato do teto salarial por categoria profissional em 2023, otimizando consultas diretas para painéis de benchmarking de RH no Looker Studio.

---

### 💻 Solução em SQL (DuckDB / MotherDuck)

```sql
WITH Maiores_Salarios AS (
    SELECT 
        job_title_short,
        salary_year_avg,
        DENSE_RANK() OVER (
            PARTITION BY job_title_short
            ORDER BY salary_year_avg DESC
        ) AS Posicao_Ranking
    FROM job_postings_fact
    WHERE YEAR(job_posted_date) = 2023
      AND salary_year_avg IS NOT NULL
)
SELECT 
    job_title_short,
    salary_year_avg,
    Posicao_Ranking
FROM Maiores_Salarios
WHERE Posicao_Ranking <= 3
ORDER BY job_title_short, Posicao_Ranking;
