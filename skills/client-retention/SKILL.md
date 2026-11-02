---
name: client-retention
description: Analyze client retention in a WAJ salon or spa - new versus returning clients, how monthly groups of new clients hold up, first-visit drop-off and repeat rates per staff member, top clients and clients at risk - and, only when the user asks, set up a win-back audience or workflow. Use when the user asks about returning clients, churn, loyalty, lapsed or lost clients, or how to bring clients back.
---

# Client retention

Explain who comes back and who does not, from WAJ's own figures. The user's
explicit instructions override every default below.

## Inputs

- **Recent period.** The last 90 days ending today unless the user gives one.
- **History.** The last 12 months for cohorts and per-staff retention;
  shorter periods hold too few returning clients for those.
- **Retention window.** 60 days unless the user says otherwise ("30_days",
  "60_days" or "90_days" in `get_clients`).
- **Branch.** All branches unless the user names one (`list_branches`).

## Steps

1. Call `get_clients` for the recent period with the retention window. Use
   its summary (`total_new_clients`, `total_returning_clients`,
   `total_retained_clients`, `rebooking_rate`) and its `clients` list
   (`is_new_client`, `is_retained`, `total_revenue`,
   `total_completed_bookings`).
2. Call `get_retention_analytics` for the 12 months. It counts a client as
   lost after `churn_window_days` without a visit: the business's setting
   (60 days unless changed), or `churn_window_days` when the user names a
   window.
   - `cohort_analysis`: per month, `cohort_size` new clients, how many are
     `still_active` and `still_active_pct`.
   - `staff_retention`: per staff member, `first_time_customers`,
     `same_staff_repeat_pct`, `shop_repeat_pct` and `kill_rate_pct`.
   - `revenue_split`: first-time versus repeat revenue per month.
3. Only if the user asks to act on the result:
   - `create_audience` to save lapsed clients as an audience, with filters
     the user agrees to (for example `visited_to` set to 60 days ago).
   - `create_workflow` for a win-back sequence using the user's audience,
     message templates and timing. It is saved switched off unless the user
     explicitly asks to switch it on.

## Output

- Retention in one sentence: returning clients and rebooking rate for the
  recent period.
- Cohorts: a table per month with new clients, still active, and the share
  still active (`still_active_pct`).
- Per staff member: repeat rate (`same_staff_repeat_pct`) and kill rate
  (`kill_rate_pct`, lost after the first visit among clients old enough to
  tell). Skip anyone with fewer than 5 first-time clients and say why.
- Top 10 clients by revenue (name, visits, revenue), only when asked. Leave
  out the row without a client name; it collects walk-in sales.
- Up to three suggestions the numbers support, such as a follow-up after the
  first visit where drop-off is high.

## Rules

- Do not show client phone numbers or emails unless the user asks for them.
- Every number must come from a tool result in this conversation; compute
  shares only from the fields named above.
- Creating an audience or a workflow changes the business's account: do it
  only when the user asks. Customer-facing changes wait for the owner's
  approval in the WAJ app; tell the user when a tool returns
  "awaiting_approval".
- Never send messages to clients from this skill.
