---
name: macro-factor
description: |
  Read and write MacroFactor nutrition data (calories, macros, food logs, goals) using the macro-factor-api Rust crate. Use when an agent needs to: (1) check today's or historical calorie/macro intake, (2) retrieve daily calorie/macro goals by day of week, (3) log a food entry (name, calories, protein, carbs, fat) to a specific date, (4) determine if a user is under or over their calorie target.
---

# MacroFactor Skill

MacroFactor stores nutrition data in Firebase Firestore. Access is via the [`macro-factor-api`](https://crates.io/crates/macro-factor-api) Rust crate.

## Cargo.toml

```toml
[dependencies]
macro-factor-api = "0.1.1"
tokio = { version = "1", features = ["full"] }
chrono = "0.4"
```

## Credentials

```bash
MACRO_FACTOR_EMAIL=user@example.com
MACRO_FACTOR_PASS=password
```

## Common Patterns

### Check today's calories

```rust
use macro_factor_api::client::MacroFactorClient;
use chrono::Local;

let mut client = MacroFactorClient::login(email, password).await?;
let today = Local::now().date_naive();
let entries = client.get_food_log(today).await?;
let total_cal: f64 = entries.iter().filter_map(|e| e.calories()).sum();
let total_protein: f64 = entries.iter().filter_map(|e| e.protein()).sum();
```

### Get today's calorie goal

```rust
let goals = client.get_goals().await?;
let dow = chrono::Local::now().weekday().num_days_from_monday() as usize;
let goal_cal = goals.calories.get(dow).copied().unwrap_or(0.0);
```

### Check over/under

```rust
let margin = total_cal - goal_cal;
if margin > 0.0 {
    println!("Over by {:.0} kcal", margin);
} else {
    println!("Under by {:.0} kcal", margin.abs());
}
```

### Log a food entry

```rust
use chrono::NaiveDate;

client.log_food(
    NaiveDate::from_ymd_opt(2026, 2, 18).unwrap(),
    "Wagyu Cheeseburger",
    1000.0,  // calories
    45.0,    // protein g
    50.0,    // carbs g
    65.0,    // fat g
).await?;
```

## Full API Reference

See [references/api.md](references/api.md) for all methods and Firestore schema details.
