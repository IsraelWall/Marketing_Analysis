# Marketing Campaign Analysis

SQL analysis of a customer marketing dataset ([Kaggle: Customer Personality
Analysis](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis)),
exploring what drives online purchase behavior and how customers respond to
marketing campaigns.

## Business questions

1. **What factors most affect online purchase volume?** — income level,
   number of kids/teens at home, deals offered, education, and web visit
   frequency, each checked as a driver of `NumWebPurchases`.
2. **Which marketing campaigns performed best?** — average online purchases
   compared across the five campaigns run (`AcceptedCmp1`–`AcceptedCmp5`).
3. **Who is the typical customer?** — average age, most common education
   level and marital status, average income, household composition, and
   how purchases split across deals/web/catalog/store channels.
4. **Which products sell best?** — share of total spend across wine,
   fruit, meat, fish, sweets, and gold products.
5. **Which sales channels are underperforming?** — deals, store, catalog,
   and web purchases compared as a share of total activity.

## Key finding

Campaigns 1, 4, and 5 drove the highest average online purchases among
customers who accepted them — the clearest result from this analysis.

The remaining questions (income/household drivers, typical customer
profile, product mix, channel performance) are answered by running the
queries in `Marketing_data_analysisv2.sql` directly against the dataset —
this repo currently documents the *questions and method*, not every
resulting number. Re-running the script and dropping the output tables
into this README would complete it.

## Tech

T-SQL (SQL Server) — window functions (`NTILE`), CTEs, conditional
aggregation, and string-to-numeric cleanup for a `$`-formatted income
column.

## File

- `Marketing_data_analysisv2.sql` — full query set, one query per question
  above.
