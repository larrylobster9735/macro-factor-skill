# MacroFactor API Reference

Crate: `macro-factor-api = "0.2"` on crates.io
Source: https://github.com/benthecarman/macro-factor-rs

## Client Setup

```rust
use macro_factor_api::client::MacroFactorClient;

// Login (use cached refresh token in production to avoid re-authing)
let mut client = MacroFactorClient::login(email, password).await?;
```

## Methods

### get_goals() -> Goals
Returns daily calorie/macro targets per day of week (Mon=0..Sun=6).

```rust
let goals = client.get_goals().await?;
let today_idx = chrono::Local::now().weekday().num_days_from_monday() as usize;
let today_cal = goals.calories.get(today_idx).copied().unwrap_or(0.0);
```

**Goals struct:**
```rust
pub struct Goals {
    pub calories: Vec<f64>,     // per day of week
    pub protein: Vec<f64>,
    pub carbs: Vec<f64>,
    pub fat: Vec<f64>,
    pub tdee: Option<f64>,
    pub program_style: Option<String>,
    pub program_type: Option<String>,
}
```

### get_food_log(date) -> Vec<FoodEntry>
Returns individual food entries for a given date.

```rust
let entries = client.get_food_log(NaiveDate::from_ymd_opt(2026, 2, 18).unwrap()).await?;
let total_cal: f64 = entries.iter().filter_map(|e| e.calories()).sum();
```

**FoodEntry helpers:**
```rust
entry.calories() -> Option<f64>   // actual_cal = c * (y * w) / g
entry.protein()  -> Option<f64>
entry.carbs()    -> Option<f64>
entry.fat()      -> Option<f64>
```

### get_nutrition(start, end) -> Vec<NutritionSummary>
Returns daily nutrition summaries (may lag food log for same-day data).

### log_food(logged_at, name, calories, protein, carbs, fat) -> ()
Logs a food entry. Creates the day's food document if it doesn't exist.
The caller provides the local datetime — the library does not call `now()` internally.

```rust
// Log at current local time
client.log_food(chrono::Local::now(), "NADC Wagyu Burger", 1000.0, 45.0, 50.0, 65.0).await?;

// Log at a specific time
let logged_at = chrono::NaiveDate::from_ymd_opt(2026, 2, 18).unwrap()
    .and_hms_opt(20, 30, 0).unwrap()
    .and_local_timezone(chrono::Local).unwrap();
client.log_food(logged_at, "Dinner", 800.0, 40.0, 60.0, 30.0).await?;
```

### log_weight(date, weight_kg, body_fat) -> ()
Logs a scale entry.

### get_weight_entries(start, end) -> Vec<ScaleEntry>
Returns weight/body fat entries for a date range.

## Firestore Layout

```
users/{uid}/
  food/{YYYY-MM-DD}       — food log (one doc per day, fields = timestamp IDs)
  nutrition/{year}         — daily summaries (MMDD keys)
  scale/{year}             — weight entries (MMDD keys)
  steps/{year}             — step counts (MMDD keys)
```

### Food Entry Field Keys
| Key | Meaning |
|-----|---------|
| `t` | name |
| `b` | brand |
| `c` | calories per serving |
| `p` | protein per serving (g) |
| `e` | carbs per serving (g) |
| `f` | fat per serving (g) |
| `g` | grams per serving |
| `y` | user quantity |
| `w` | grams per unit |
| `h` | hour logged |
| `mi` | minute logged |

Actual calories = `c × (y × w) / g`
