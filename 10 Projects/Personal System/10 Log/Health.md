---
note_type: Log
topic: Health
created: 2026-10-24 07:05
format: v1
---
> Health log: headaches, mood, energy, sleep, and meds. One line per entry, newest at the bottom. Background lives in `04 Health`.

## How to log

No schedule. Add a line whenever it's handy, for example while doing another task. Gaps are fine; the AI should treat a missing day as unknown, not as a good day.

Two kinds of lines. Both start with the date, then a type, then fields split by `|`.

**Check-in** — a quick snapshot of how the day is going, as often or rarely as you like:

```
- 2026-10-24 | day | sleep 7 | mood 3 | energy 2 | headache 5 | caffeine 2 | water ok | note: long delivery day
```

**Events** — when something happens:

```
- 2026-10-24 14:30 | headache | 6 | temples, pressure | note: skipped lunch
- 2026-10-24 14:45 | med | ibuprofen 400mg | for headache
- 2026-10-24 16:30 | relief | 2 | note: ate, drank water
- 2026-10-24 20:00 | mood | 2 | note: rejection email
```

Skip any field you don't know. A short line is better than no line.

## Scales

| Field | Scale |
| --- | --- |
| `headache` | 0 none, 1–3 mild (can work), 4–6 moderate (hard to focus), 7–10 severe (have to stop). A check-in uses the worst so far that day. |
| `relief` | Headache level after something helped, same 0–10 scale |
| `mood` | 1 very low, 2 low, 3 okay, 4 good, 5 great |
| `energy` | 1 empty, 2 low, 3 medium, 4 good, 5 high. Maps to Low / Medium / High in `01 About Me`. |
| `sleep` | Hours slept |
| `caffeine` | Cups of coffee, soda, or energy drinks |
| `water` | `low`, `ok`, or `good` |
| `med` | Name and dose, then what it was for |

## Asking the AI

Paste the last 2–4 weeks of entries with `04 Health`, then ask:

- "What patterns do you see between headaches and sleep, caffeine, water, workdays, and meds? How many entries support each one?"
- "How many days did I take painkillers each week?"
- "Summarize the last month for my doctor in five lines, plus three questions to ask."

## Entries

