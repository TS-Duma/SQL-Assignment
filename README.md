# Sizwe Coffee: Year-End SQL Analysis
 
## Project overview
Sizwe Coffee (Pty) Ltd is a coffee roastery and wholesale supplier based in Isando, Johannesburg, with a second distribution centre in Epping, Cape Town. It sells to about 1,400 business customers through two channels, Wholesale (about 70% of revenue) and Subscription (about 30%). Revenue was R441m in FY2024 and R480m in FY2025 (financial year 1 July to 30 June).
 
As a junior data analyst, I answered eight questions from the Financial Director (Nomsa Dlamini) and the National Sales Manager (Bongani Twala) ahead of the year-end review:
 
1. Revenue by channel, FY2024 vs FY2025, with growth.
2. Revenue, cost, gross profit and margin by product line.
3. Collections performance by channel.
4. Top ten customers by FY2025 revenue.
5. Top fifteen Wholesale customers who never received a trade promotion.
6. Sales reps' FY2025 monthly average against target.
7. Monthly revenue with month-on-month change and a rolling 3-month average.
8. Why gross margin fell from 34.7% to 33.0%.

All queries are answered separately, each with a written answer beneath it.
 
## Dataset
Data covers 1 July 2023 to 30 June 2025. Tables used:
 
| Table | Rows | Contents |
|---|---|---|
| `customers` | 1,400 | Trading name, channel, segment, province, credit limit, assigned rep |
| `products` | 23 | SKU, product line, pack size, list price, unit cost |
| `sales_orders` | 48,144 | One row per order (customer, dates, channel, rep) |
| `sales_lines` | 168,172 | One row per order line (units, price, discount, `line_revenue`, Cost of Goods (`cogs`)) |
| `ar_ledger` | 48,144 | One row per invoice (amount, due date, amount settled, settlement date) |
| `reps` | 18 | Territory and monthly target |
| `trade_promotions` | 640 | Promotions given to a customer on a SKU |
 
Relationships:
- `sales_orders.customer_id` → `customers.customer_id`
- `sales_orders.order_id` → `sales_lines.order_id`
- `sales_lines.sku` → `products.sku`
- `customers.rep_id` and `sales_orders.rep_id` → `reps.rep_id`
- `ar_ledger.customer_id` and `trade_promotions.customer_id` → `customers.customer_id`

Notes: revenue lives on `sales_lines`, not on orders. Order channel and customer channel agree on every order (checked: zero mismatches), because each customer belongs to one channel. FY2024 is orders on or before 30 June 2024, FY2025 everything after.
 
## Skills demonstrated
| Technique | Where |
|---|---|
| INNER JOINs across 3-4 tables | Tasks 1-5, 7, 8 |
| LEFT JOIN to keep unmatched rows | Task 4 (customers without a rep), Task 6 (reps with no sales) |
| Aggregation (`SUM`, `COUNT`, `AVG`) with `GROUP BY` | All tasks |
| `CASE` expressions (conditional aggregation / pivoting) | Tasks 1, 3, 8 |
| `COALESCE` / NULL handling (`COUNT(col)` ignoring NULLs) | Tasks 3, 4, 6 |
| `NOT EXISTS` anti-join | Task 5 |
| Subquery in `FROM` | Task 6 |
| CTEs | Tasks 7, 8 |
| Window functions: `LAG`, rolling `AVG ... ROWS BETWEEN`, `SUM() OVER (PARTITION BY)` | Tasks 7, 8 |
| Date functions (`DATE_TRUNC`, date arithmetic) | Tasks 3, 7 |
 
## How to run
1. Open repository on GitHub.
2. Select desired query to view and double click (left) to open.

## Key insights
 
**For Nomsa (Finance)**
- **Collections are the real problem and it is all Wholesale.** Wholesale invoices take 68.7 days on average to settle (Subscription: 3.8). Wholesale went from 56.9 days in FY2024 to 84.1 in FY2025 on 60-day terms. Wholesale holds R87.8m of the R88.6m outstanding (3,454 of 3,498 open invoices). The 84.1 figure is probably understated, since open invoices are excluded from the average.
- **The margin decline is a mix problem, not a cost problem.** Gross margin fell from 34.7% to 33.0%, yet every product line's own margin was flat. Bulk Commercial (19.9% margin) grew 46%, from R110.3m to R161.5m, lifting its share of revenue from 25.0% to 33.6%. It dilutes the blended margin as it grows.
- **Profit and revenue tell different stories.** House Blend earns the most profit (R107.1m). Bulk Commercial is the second-largest line by revenue (R271.8m) but earns only R54.1m profit. Single Origin has the best margin (44.3%).
- **Growth is even across channels.** Both channels grew 8.8%, so the 70/30 split is unchanged.
- **Seasonality:** revenue peaks in June/July and bottoms out in December (R29.9m in Dec 2023, R32.6m in Dec 2024, about 13% below November each year). Cash planning should reflect this.
**For Bongani (Sales)**
- **Concentration risk:** Halvane Retail Group alone bought R38.4m in FY2025, over 11 times the second-largest account (Highveld Deli 85 at R3.4m).
- **Promotion gap:** 200 of 600 Wholesale customers have never had a trade promotion. The three biggest are Umhlanga Fresh 108 (R6.2m), Ridgeway Foods 308 (R4.4m) and Kalahari Kitchen 42 (R4.4m), and all three are also in the FY2025 top ten.
- **Targets look too low:** 16 of 18 reps beat target, and several exceed 200% (Karabo van Wyk 250.3%, Kagiso Mkhize 249.8%). Only Sipho Steyn (98.0%) and Johan Naidoo (77.6%) missed. Targets should be reviewed.
