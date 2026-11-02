---
name: business-trends
description: Check current trends in a WAJ salon or spa - weekly revenue and services sold over recent weeks, services gaining or losing popularity, new versus returning clients and rebooking, the coming week's booking load and the leads pipeline. Use when the user asks what is trending, how the business is doing lately, whether things are improving, or what to focus on next.
---

# Business trends

Show the direction the business is moving in, from WAJ's own figures. The
user's explicit instructions (period, branch, topics) override every default
below.

## Inputs

- **Window.** The last 8 weeks ending today unless the user gives another
  window. Split it into the most recent 4 weeks and the 4 weeks before.
- **Branch.** All branches unless the user names one (`list_branches`).

## Steps

1. Call `get_business_performance` for the whole window with
   `increment_interval` 7: one row per week with revenue and services sold.
2. Call `get_services` once for each half and compare revenue and `no_booked`
   per service. A service listed in only one half is "new" or "no sales".
3. Call `get_clients` for each half with `retention_window_type` "60_days".
   Use its summary: `total_new_clients`, `total_returning_clients` and
   `rebooking_rate`.
4. Call `get_bookings` from today for the next 7 days. `summary.by_date`
   lists only days that have bookings: count every other day as zero.
5. Call `get_leads_overview` for the recent 4 weeks. Open pipeline is in
   `current_snapshot_summary`; activity in the period is in `period_summary`.
   Skip leads only when both are empty.

## Output

Lead with one sentence on the overall direction: revenue across the recent 4
weeks against the 4 before, with the percentage. Then short sections:

- **Weekly revenue and services sold:** a table, one row per week.
- **Rising and falling services:** up to 3 of each, with the change.
- **Clients:** new and returning clients and rebooking rate, recent half
  against earlier half.
- **Next 7 days:** bookings per day, including empty days, and the quietest
  day.
- **Pipeline:** open leads and their value; win rate when the period has
  closed leads.
- **Worth a look:** at most three points the data supports, such as a
  falling service or a quiet day ahead.

## Rules

- A trend needs two comparable periods with data. When a half is empty (a
  new business, a closed branch), say so instead of describing a trend.
- Use client counts from `get_clients`, not from `get_business_performance`;
  the two count new clients differently.
- Every number must come from a tool result in this conversation. Do not
  forecast beyond the bookings already in WAJ.
- This skill only reads data. Suggest actions; do not take them.
