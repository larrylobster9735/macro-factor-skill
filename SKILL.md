---
name: macro-factor
description: |
  Read and write MacroFactor nutrition data (calories, macros, food logs, goals) via the macro-factor-api Rust crate or larry-tools CLI. Use when an agent needs to: (1) check today's or historical calorie/macro intake, (2) retrieve daily calorie/macro goals, (3) log a food entry (name, calories, protein, carbs, fat) to a specific date, (4) check if a user is under or over their calorie target.
---

# MacroFactor Skill

MacroFactor stores nutrition data in Firebase Firestore. Access is via the `macro-factor-api` crate (Rust) or the `larry-tools` CLI binary.

## Credentials

Required env vars:
- `MACRO_FACTOR_EMAIL` — account email
- `MACRO_FACTOR_PASS` — account password

## Quick Reference — larry-tools CLI

```bash
# Check today's food log + calorie total
larry-tools calories

# Check last N days
larry-tools calories --days 7

# Save to local SQLite DB
larry-tools calories --save

# Get daily calorie/macro goals (per day of week)
larry-tools goals

# Log a food entry (today)
larry-tools log-food --name "Food name" --calories 500 --protein 30 --carbs 40 --fat 15

# Log to a specific date
larry-tools log-food --name "Burger" --calories 950 --protein 45 --carbs 50 --fat 65 --date 2026-02-18
```

## Checking Against Goal

1. Run `larry-tools goals` to get today's calorie target (indexed by day of week: Mon=0..Sun=6)
2. Run `larry-tools calories` to get actual intake
3. Compare — report over/under with margin

## Using the Rust API Directly

See `references/api.md` for full `MacroFactorClient` method signatures and Firestore data layout.

## Notes

- Food log lives in Firestore at `users/{uid}/food/{YYYY-MM-DD}` — one document per day, fields keyed by timestamp ID
- Nutrition summaries at `users/{uid}/nutrition/{year}` with MMDD keys (may lag behind food log)
- Prefer food log over nutrition summaries for same-day accuracy
- Firestore field paths starting with digits must be backtick-quoted in `updateMask` — handled automatically in v0.1.1+
