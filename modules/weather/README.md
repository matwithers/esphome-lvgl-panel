# Weather Module (LVGL Panel)

Weather display module for ESPHome LVGL panels.

This module fetches data directly from Home Assistant using `weather.get_forecasts` and renders:

- 🌤 **Now / Today** weather (icon + temperature)
- 📅 **4-Day Forecast** (day, icon, hi/lo)

No Home Assistant template sensors are required.

---

# 📦 Module Structure

This weather system is split into:

## 1️⃣ weather_core.yaml
Handles:
- Periodic refresh (default every 15 minutes)
- Home Assistant API call
- JSON parsing
- Updating LVGL widgets

## 2️⃣ weather_now_widget.yaml
Displays:
- “Now” label
- Current weather icon
- Current temperature

## 3️⃣ weather_forecast_widget.yaml
Displays:
- 4 forecast days in a horizontal row
- Each day shows:
  - Day name
  - Weather icon
  - High / Low temperature

## 4️⃣ weather_icons.yaml (REQUIRED)
Defines the image assets used by the module.

This **must** be included in `packages:` before the core or widgets.

---

# 🖼 Icons Requirement

You **must include**:

```yaml
packages:
  weather_icons: !include esphome-lvgl-panel/modules/weather/weather_icons.yaml
```

Icons are rasterised using:

```yaml
resize: 80x80
```

Icon sizing is handled at compile time (recommended).

The core expects the following image IDs:

```
clear_night
cloudy
exceptional
fog
hail
lightning
lightning_rainy
partlycloudy
pouring
rainy
snowy
snowy_rainy
sunny
windy
unknown
unavailable
```

---

# ⚙ weather_core Configuration

Include in `packages:`:

```yaml
weather_core: !include
  file: esphome-lvgl-panel/modules/weather/weather_core.yaml
  vars:
    weather_entity: weather.home
    enable_today: "true"
    enable_forecast: "true"
    refresh_minutes: "15"
```

### Variables

| Variable | Description | Default |
|-----------|-------------|----------|
| weather_entity | HA weather entity ID | weather.home |
| enable_today | Enable Now widget updates | "true" |
| enable_forecast | Enable Forecast updates | "true" |
| refresh_minutes | Refresh interval | "15" |

---

# 🧱 Widget Placement (12×12 Layout)

This module is designed for a **12×12 LVGL grid layout**, but will work on similar grid-based layouts.

## 🌤 Weather Now Widget

Typical usage (half-width):

```yaml
- !include
    file: esphome-lvgl-panel/modules/weather/weather_now_widget.yaml
    vars: { row_pos: "4", column_pos: "0", row_span: "4", column_span: "6" }
```

### Layout Options (12×12 grid)

| Layout | Rows × Cols | Use Case |
|---------|-------------|-----------|
| Half-width | 4 × 6 | As used in main layout |
| Single cell | 4 × 3 | Compact layout option |

The widget scales cleanly in either configuration.

---

## 📅 Weather Forecast Widget

Designed as a full-width horizontal strip:

```yaml
- !include
    file: esphome-lvgl-panel/modules/weather/weather_forecast_widget.yaml
    vars: { row_pos: "8", column_pos: "0", row_span: "4", column_span: "12" }
```

### Layout (12×12 grid)

| Layout | Rows × Cols |
|---------|-------------|
| Full width | 4 × 12 |

The forecast widget is intended to occupy an entire row.

---

# 🔄 Refresh Behaviour

Weather updates occur:

- On API connection (boot)
- Every `refresh_minutes`
- Via Home Assistant `weather.get_forecasts`

Only one HA call updates both:
- Now
- Forecast

---

# 🧩 Expected Widget IDs

## Required for "Now"

```
weather_condition_icon
weather_temperature_today
```

## Required for Forecast

```
forecast_day_0..3
forecast_temperature_hi_0..3
forecast_temperature_lo_0..3
forecast_condition_icon_0..3
```

If a section is disabled via `enable_today` or `enable_forecast`, those widget IDs are not required.

---

# 🧠 Design Philosophy

- Core logic is separated from layout
- Icons are assets only
- Widgets are purely visual
- Single HA service call
- No YAML spaghetti includes
- Compile-time icon sizing
- Designed specifically for 4848S040 12×12 LVGL panel

---

# ✅ Example Main Page Layout

```yaml
lvgl:
  pages:
    - id: main_page
      layout: 12x12
      widgets:

        - !include
            file: modules/weather/weather_now_widget.yaml
            vars: { row_pos: "4", column_pos: "0", row_span: "4", column_span: "6" }

        - !include
            file: modules/weather/weather_forecast_widget.yaml
            vars: { row_pos: "8", column_pos: "0", row_span: "4", column_span: "12" }
```

---

# 🛠 Notes

- Icons are resized at compile time using `resize:`.
- Forecast uses flex layout internally.
- No runtime image scaling.
- All temperatures rounded to integer degrees.
- Works with any HA weather integration returning standard condition strings.

---

Weather module is now fully modularised and layout-driven.