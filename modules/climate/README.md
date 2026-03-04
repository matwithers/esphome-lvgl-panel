# Climate Module (Temperature + Humidity)

The **Climate module** provides two UI widgets designed for ESPHome LVGL wall panels:

- **Temperature / Heating widget**
- **Humidity widget**

Both widgets are designed to work with a Home Assistant **`climate` entity** while optionally allowing **room sensor overrides**.

This allows the panel to support multiple deployment stages:

1. Whole-house thermostat (e.g. Nest)
2. Room sensors without room heating control
3. Full room-by-room heating control

The module handles all rendering logic internally so widgets remain simple.

---

# Display Layout

The panel UI uses a **12 × 12 grid layout**.

Each widget occupies:

```
4 rows × 3 columns
```

This means a full screen can contain up to **12 widgets**.

Example layout:

```
+----+----+----+----+
| Wx | Wx | Cl | Cl |
| Wx | Wx | Cl | Cl |
| Wx | Wx | Hu | Hu |
| Wx | Wx | Hu | Hu |
+----+----+----+----+
```

Example screen:

```
Weather     Climate
Forecast    Climate

Battery     Humidity
Network     Energy
```

This layout keeps widgets:

- readable at a distance
- visually balanced
- easy to extend

---

# Temperature Widget

Displays:

```
Climate
🔥
18° / 21°
```

Where:

| Element | Meaning |
|------|------|
| 🔥 | Heating active |
| 🌡 | Heating idle/off |
| Current temperature | Room or thermostat |
| Target temperature | Only visible while heating |

When heating is **not active**, the widget hides the target temperature:

```
Climate
🌡
18°
```

This keeps the display cleaner and closer to the behaviour of a Nest thermostat.

---

# Humidity Widget

Displays room humidity and warns if levels are too high.

Normal:

```
Humidity
💧
55%
```

Warning state:

```
Humidity
💧⚠
75%
```

When humidity exceeds the configured threshold:

- the icon changes to **`mdi_water_percent_alert`**
- the percentage turns **blue**

This provides an easy visual indicator of potential condensation or mould risk.

---

# Configuration

The module is configured via **substitution variables**.

Example:

```yaml
packages:
  climate:
    !include
    file: modules/climate/climate_core.yaml
    vars:
      climate_entity: "climate.nest"

      enable_temp_widget: "true"
      enable_humidity_widget: "true"

      humidity_warn_level: "70"
```

---

# Room Sensor Overrides

The module supports an intermediate configuration where:

- **Heating state and target temperature** come from a thermostat
- **Room temperature and humidity** come from local sensors

Example bedroom configuration:

```yaml
vars:
  climate_entity: "climate.nest"

  enable_temp_override: "true"
  temp_override_entity: "sensor.bedroom_temperature"

  enable_humidity_override: "true"
  humidity_override_entity: "sensor.bedroom_humidity"
```

Result:

| Data | Source |
|-----|------|
| Heating state | Nest |
| Target temp | Nest |
| Current temperature | Bedroom sensor |
| Humidity | Bedroom sensor |

This allows panels to show **room conditions** even before room-by-room heating control is implemented.

---

# Future Room Heating

Once each room has its own `climate` entity, overrides are no longer needed.

Example:

```yaml
vars:
  climate_entity: "climate.bedroom"
```

The panel will automatically use:

- room heating state
- room target temperature
- room humidity

No widget or module changes are required.

---

# Widget Requirements

The following LVGL widget IDs must exist if the widgets are enabled.

Temperature widget:

```
climate_state_indicator
climate_temp_current
climate_temp_target
climate_temp_separator
```

Humidity widget:

```
humidity_state_indicator
room_humidity
```

---

# Refresh Behaviour

The module updates the display when:

- Home Assistant values change
- ESPHome boots
- Home Assistant reconnects
- The configured refresh interval elapses

Default refresh interval:

```
30 seconds
```

---

# Design Goals

The climate module is designed to be:

- **Room-agnostic**
- **Thermostat-agnostic**
- **Simple to configure**
- **Future-proof for per-room heating**

The goal is to allow the same panel firmware to run in **every room**, with only configuration variables changing.

```
Living room panel
Bedroom panel
Kitchen panel
Bathroom panel
```

All using the same module and widgets.

---

# Summary

The Climate module provides:

- clean heating status display
- humidity monitoring with warning indicators
- optional room sensor overrides
- compatibility with both single-thermostat and per-room heating systems
- a consistent UI layout across the entire panel system