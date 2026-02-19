# macro-factor-skill 🦞

An [OpenClaw](https://github.com/openclaw/openclaw) skill for reading and writing [MacroFactor](https://www.macrofactorapp.com/) nutrition data.

## What It Does

Gives your agent access to MacroFactor via the [`macro-factor-api`](https://crates.io/crates/macro-factor-api) Rust crate:

- Check today's calories and macros
- Retrieve daily calorie/macro goals
- Log food entries (name, calories, protein, carbs, fat)
- Check historical nutrition data

## Install

Drop `macro-factor.skill` into your OpenClaw skills directory.

## Credentials

Set these env vars for your agent:
- `MACRO_FACTOR_EMAIL`
- `MACRO_FACTOR_PASS`
