# EV Module (LVGL Panel)

## Overview

The EV module provides a clean, Material-inspired electric vehicle status widget for ESPHome LVGL panels.

It displays:

- Battery percentage
- Estimated range (miles)
- Charging state indicator (lightning bolt + blue text when charging)

The module is split into:

- `ev_core.yaml` → Logic, sensors, scripts, images
- `ev_widget.yaml` → LVGL layout and visual structure

This keeps behaviour and presentation cleanly separated.

---

## Layout Assumptions

This widget is designed for a **12x12 LVGL grid layout**.

Default footprint:

- **Row span:** 4
- **Column span:** 6

So it occupies a **4×6 block** in a 12×12 grid.

It is intended to sit comfortably as a half-width panel section.

However:

- It should work in any similar grid-based layout
- You can adjust `row_pos`, `column_pos`, `row_span`, and `column_span`
- It assumes the page defines a grid layout

Example grid definition:

```yaml
lvgl:
  pages:
    - id: main_page
      layout: 12x12
```

---

## Features

### Battery Percentage

- Displays as `XX%`
- Colour-coded when not charging:
  - ≥ 40% → Green
  - ≥ 15% → Amber
  - < 15% → Red
- Turns **Material Blue** when charging

### Charging Indicator

- Lightning bolt icon appears when charging
- Uses Material Design Icons font
- Icon hides automatically when not charging
- Layout remains visually centred

### Range Display

- Displays as `XXX miles`
- Rounded to nearest whole number

---

## Required Home Assistant Entities

| Purpose        | Example Entity                      |
|---------------|-------------------------------------|
| Battery level | `sensor.leaf_battery_level`         |
| Range         | `sensor.leaf_range_ac_off`          |
| Charging      | `binary_sensor.leaf_charging`       |

These can be overridden via package variables.

---

## Installation

### 1️⃣ Include Core Logic

```yaml
packages:
  ev_core: !include modules/ev/ev_core.yaml
```

### 2️⃣ Add Widget to a Page

Inside a page’s `widgets:` section:

```yaml
- !include
    file: modules/ev/ev_widget.yaml
    vars:
      row_pos: "4"
      column_pos: "0"
      row_span: "4"
      column_span: "6"
```

---

## Available Variables

| Variable | Default | Description |
|----------|----------|------------|
| `battery_level_entity` | `sensor.leaf_battery_level` | Battery % sensor |
| `range_entity` | `sensor.leaf_range_ac_off` | Range sensor |
| `charging_state_entity` | `binary_sensor.leaf_charging` | Charging state |
| `row_pos` | `"0"` | Grid row position |
| `column_pos` | `"0"` | Grid column position |
| `row_span` | `"4"` | Grid row span |
| `column_span` | `"6"` | Grid column span |

---

## Font Requirements

Requires:

- Inter font for battery text
- Material Design Icons font including:
  - `mdi_lightning_bolt`
  - `mdi_lightning_bolt_outline`

Ensure the glyphs are included in your font definition.

---

## Design Notes

- Built for a 480×480 Guition display
- Optimised for visual balance over the car icon
- Uses flex layout for dynamic centring
- Lightning bolt does not shift layout when hidden

---

## Philosophy

This module follows the panel architecture pattern:

- `*_core.yaml` → Behaviour
- `*_widget.yaml` → Presentation

The goal is composability, reusability, and clean separation of concerns.

---

## Status

Stable and modular.

Ready for reuse across multiple pages or panel variants.