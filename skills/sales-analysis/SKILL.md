---
name: sales-analysis
description: Analyze a WAJ salon or spa's sales for a period - total revenue and how it splits, the change against the previous period, best and declining services and categories, payment methods, busiest day, money still owed and, when asked, profit and margins. Use when the user asks how sales or revenue went, wants a sales report or summary, or asks why revenue went up or down.
---

# Sales analysis

Answer sales and revenue questions with WAJ's own figures. The user's explicit
instructions (period, branch, what to include) override every default below.

## Inputs

- **Period.** Use the dates the user gives. Without one, use the last 30 days
  ending today and say so. Dates are YYYY-MM-DD in the business's local time.
- **Comparison.** The period of the same length just before it, unless the
  user names another (for example "this month vs the same month last year").
- **Branch.** All branches unless the user names one; resolve a name with
  `list_branches`. Ask which branch only when the name matches more than one.

## Steps

1. Call `get_business_performance` for the period (`increment_interval` 1 for
   up to 31 days, 7 for longer) and again for the comparison period.
2. Call `get_services` for both periods and `get_categories` for the period.
   Service rows carry `bookings_revenue` and `no_booked`.
3. Call `get_invoices` with `status` "owed" for the period when the user asks
   about money owed, or when `total_unpaid_amount` is above zero.
4. When the user asks about profit, costs or margins, call `run_report` with
   slug "income_statement" for both periods and use its `profit_and_loss`:
   `net_revenue`, `cost_of_goods`, `gross_profit`, `staff_pay`, `expenses`,
   `net_profit` and `net_margin_pct`. If the report is locked for the user's
   role, say so.
5. Compute changes yourself: absolute difference and percentage per figure.
   A service with sales in only one of the periods is "new" or "no sales",
   not a percentage.

## Output

Lead with two sentences: total revenue for the period, and its change against
the comparison period. Then:

- A table for both periods with the change: total revenue, service sales
  (`services_amount`), product sales (`products_amount`), memberships
  (`memberships_amount`), gift cards (`gift_cards_amount`), discounts
  (`total_discount`), VAT (`total_tax`), services sold (`services_sold`) and
  still unpaid (`total_unpaid_amount`). Say that total revenue includes the
  unpaid amount.
- Top 5 services by revenue, and services whose revenue fell by more than 25%
  against the comparison period.
- Busiest day (`summary.busiest_day_by_revenue`).
- Payment methods: `summary.payment_methods`, the amount per method for the
  period.
- Profit, only when asked: net revenue, cost of goods, gross profit,
  operating expenses and net profit with the net margin, for both periods.
- Money owed: the count and amount from the invoices summary. Never print an
  invoice's client field; it can contain a phone number.
- One to three observations the numbers support, each with its figure.

Offer a CSV with `export_csv` only when the user asks for a file.

## Rules

- Every number must come from a tool result in this conversation. If a tool
  returns no data or an error, say which part is missing; do not estimate it.
- Use the discount from `get_business_performance`; service-level discounts
  are counted differently, so do not add or compare the two.
- Amounts are in the business's currency as returned; do not convert them.
- Name the period, the comparison period and the branch scope in the answer.
- This skill only reads data. It never changes prices, sales or invoices.
