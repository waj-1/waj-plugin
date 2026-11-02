---
name: staff-performance
description: Review a WAJ salon or spa team's performance - revenue, bookings, completed services, booked versus scheduled hours, unique clients and client retention per staff member, ranked and compared with the previous period. Use when the user asks who performed best, how a staff member is doing, which staff are under-booked, or wants a team performance review.
---

# Staff performance

Compare staff on WAJ's own figures. The user's explicit instructions (period,
branch, staff members, metrics) override every default below.

## Inputs

- **Period.** Use the dates the user gives; without one, the last 30 days
  ending today, and say so. Compare with the period of the same length just
  before it.
- **Staff.** Everyone unless the user names people. Resolve names with
  `list_staff`; if a name matches more than one person, ask which one.
- **Branch.** All branches unless the user names one (`list_branches`).

## Steps

1. Call `get_staff_performance` for the period and for the comparison period.
   Each row has `revenue_generated`, `no_bookings_booked`,
   `no_bookings_checkedout` (completed), `services_count`, `no_hours_booked`,
   `no_hours_worked` (scheduled hours) and `unique_clients_count`.
2. For client retention, call `get_retention_analytics` for the last 12
   months (shorter periods hold too few returning clients). Use
   `staff_retention`: `first_time_customers`, `same_staff_repeat_pct` and
   `kill_rate_pct` per staff member.
3. When the user asks about a named person's hours, call `get_staff_schedule`
   for them and add up the weekly hours from `week` (a day reads like
   "09:00–18:00" or "Day off").
4. Call `get_staff_payroll` only when the user asks about pay or commission.
   Salary-plan pay is in `staff_payroll`; commission is in
   `commission.by_staff` (`commission`, `paid`, `pending`, per earning
   period) and, for the current month so far, `commission.in_progress`
   (`commission_so_far`). If it is refused for the user's role or returns
   nothing, say so.

## Output

- A table with one row per staff member: revenue, bookings, completed,
  booked hours, scheduled hours, utilisation (booked ÷ scheduled hours),
  revenue per booked hour, unique clients, and the change in revenue against
  the comparison period. Sort by revenue unless the user asks otherwise.
  Leave out staff with no revenue and no bookings in both periods.
- The top performer and the biggest drop, each with the figures behind it.
- Retention per staff member over the 12 months: repeat rate
  (`same_staff_repeat_pct`) and kill rate (`kill_rate_pct`, clients lost
  after the first visit, leaving out those too recent to tell). Skip anyone
  with fewer than 5 first-time clients and say why.
- Commission per staff member, only when the user asked about pay or
  commission: earned and still pending for the period, and this month so far
  when the period includes today.
- Staff with low utilisation, as candidates for promotion or schedule
  changes, only when the numbers show it.

## Rules

- Every number must come from a tool result in this conversation; compute
  ratios only from the fields named above, and do not invent reasons for a
  change.
- Present performance factually. Do not label anyone a poor performer or
  recommend discipline; leave judgements about people to the user.
- Staff names and pay are internal business data: show them only in answer
  to the user's question.
- This skill only reads data. It never changes schedules, pay plans or staff.
