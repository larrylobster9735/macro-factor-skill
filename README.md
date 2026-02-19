# macro-factor-skill

An [OpenClaw](https://github.com/openclaw/openclaw) skill for reading and writing [MacroFactor](https://www.macrofactorapp.com/) nutrition data via the [`macro-factor-api`](https://crates.io/crates/macro-factor-api) Rust crate.

## What It Does

Gives your agent the ability to:

- Check today's or historical calorie/macro intake
- Retrieve daily calorie/macro goals (per day of week)
- Log food entries (name, calories, protein, carbs, fat)
- Determine if a user is over or under their calorie target

## Install

Copy the `macro-factor/` skill directory into your OpenClaw skills folder, or reference it directly from this repo.

## Requirements

Add to your `Cargo.toml`:

```toml
[dependencies]
macro-factor-api = "0.1.1"
```

Set env vars:

```bash
MACRO_FACTOR_EMAIL=user@example.com
MACRO_FACTOR_PASS=password
```

## Related

- [`macro-factor-api`](https://crates.io/crates/macro-factor-api) — the underlying Rust crate
- [benthecarman/macro-factor-rs](https://github.com/benthecarman/macro-factor-rs) — source for the crate
