# 🎯 Desafio 20 — Agregações com Junção de Tabelas (`JOIN` + `GROUP BY` + `HAVING`)

### 📌 O Problema de Negócio
O time de Parcerias Estratégicas solicitou a identificação das **5 empresas com maiores médias salariais** em **2023**, considerando apenas entidades com amostragem relevante (**mínimo de 3 vagas cadastradas**).

---

### 🎙️ Apresentação Executiva (Relatório ao Chefe)

1. **Enriquecimento de Dados por Relacionamento:** O cruzamento entre a tabela de fatos (`job_postings_fact`) e a dimensão de empresas (`company_dim`) permitiu traduzir IDs numéricos em nomes corporativos auditáveis.
2. **Controle de Distorções com `HAVING`:** A aplicação do filtro de agregados para exigir no mínimo 3 vagas ativas eliminou distorções causadas por empresas com ofertas pontuais desproporcionais, garantindo uma média estatisticamente confiável.
3. **Métrica Gerencial:** O resultado entrega a lista direta dos top players contratantes de 2023 para guiar negociações de parcerias de recrutamento.

---

### 💻 Solução em SQL (DuckDB / MotherDuck)

```sql
SELECT 
    c.name,
    ROUND(AVG(j.salary_year_avg)) AS Media_Salarial,
    COUNT(j.job_id) AS Vagas
FROM company_dim AS c
INNER JOIN job_postings_fact AS j
    ON c.company_id = j.company_id
WHERE YEAR(j.job_posted_date) = 2023
  AND j.salary_year_avg IS NOT NULL
GROUP BY c.name
HAVING Vagas >= 3
ORDER BY Media_Salarial DESC
LIMIT 5;
