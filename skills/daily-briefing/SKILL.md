---
name: daily-briefing
description: Give a salon or spa manager a short start-of-day briefing from WAJ - today's bookings by staff member, first and last appointment, yesterday's sales, money still owed and gift cards expiring soon. Use when the user asks for a daily briefing, a morning summary, what today looks like, or what needs attention today.
---

# Daily briefing

A one-screen summary of the day for the person running the business. The
user's explicit instructions (date, branch, sections) override every default
below.

## Inputs

- **Day.** Today in the business's local time, unless the user names another
  day. "Yesterday" below means the day before that.
- **Branch.** All branches unless the user names one (`list_branches`).

## Steps

1. Call `get_bookings` for the day (start and end date both that day). Use
   `summary.by_state` for counts and `summary.by_staff` for bookings per
   staff member; a booking with two staff counts once for each. Take the
   first and last appointment from the bookings' start and end times.
2. Call `get_business_performance` for yesterday (start and end date both
   yesterday): `total_revenue` and `services_sold`.
3. Call `get_invoices` with `status` "owed" for the last 7 days and use its
   summary: number of invoices and the amount owed.
4. Call `get_gift_cards` with `expiring_within_days` 7.
5. When the user asks where a service still fits today, call
   `find_free_slots` for that service and day.

## Output

Keep it short enough to read in a minute:

- **Today:** number of bookings, first and last appointment, and a table of
  staff with their booking count.
- **Yesterday:** revenue and services sold.
- **Needs attention:** invoices still owed (count and amount) and gift cards
  expiring within 7 days (count). Omit a line when there is nothing.

When there are no bookings for the day, say so and keep the other sections.

## Rules

- Do not print client phone numbers or emails, including the client field of
  an invoice, which can contain a phone number. Names and times are enough.
- Every number must come from a tool result in this conversation. If a step
  fails, keep the rest of the briefing and say which part is missing.
- This skill only reads data. It never books, moves or cancels appointments.
