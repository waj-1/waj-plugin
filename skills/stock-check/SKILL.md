---
name: stock-check
description: Check a WAJ salon or spa's product stock - items out of stock or below their low-stock level, product sales by category, which services use the most products and what they cost per service - and list what to reorder. Use when the user asks about stock, inventory, what to reorder, product usage or product cost per service.
---

# Stock check

Show what needs reordering and where products go, from WAJ's own figures.
The user's explicit instructions override every default below.

## Inputs

- **Period.** The last 30 days ending today for stock and sales, and the
  last 90 days for product usage by service (shorter periods often hold too
  few services to show usage), unless the user gives a period.
- **Branch.** Stock rows carry no branch name. When the business has more
  than one branch (`list_branches`), call `get_inventory` once per branch
  with its `branch_id` so each row can be labelled; otherwise call it once.

## Steps

1. Call `get_inventory` for the period. In `product_summary`, each product
   has `stock_quantity`, `low_stock_threshold`, `is_low_stock` and
   `is_out_of_stock`; the summary has `low_stock_count` and
   `out_of_stock_count`, and sales by category (`total_products_sold`,
   `sales_revenue`).
2. Call `get_service_product_usage` for the usage period: per service,
   `execution_count`, `avg_product_cost_per_execution` and `products_used`.

## Output

- **Reorder now:** out-of-stock items first, then items at or below their
  low-stock level, with branch, quantity on hand and the low-stock level.
- **Product sales:** units and revenue by category for the period.
- **Product cost per service:** the services with the highest average
  product cost, with the products they use most.
- A reorder quantity only when the user asks and gives a target (for example
  "back to twice the low-stock level").

## Rules

- WAJ does not return units sold per product, so do not rank individual
  products by sales or estimate days of stock left.
- Every number must come from a tool result in this conversation. When usage
  data is empty for the period, say so instead of guessing.
- This skill only reads data. It never adjusts stock or places stock orders;
  point the user to a stock order in WAJ when they want to reorder.
