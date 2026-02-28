# Time + Date Module (LVGL Panel)

## Overview

The Time + Date module provides a simple, glanceable clock widget for ESPHome LVGL panels.

It displays:

- Current time (`HH:MM`)
- Month + day (e.g. `February 28`)

The module is split into:

- `time_date_core.yaml` → Logic (update script)
- `time_date_widget.yaml` → LVGL layout (widgets only)

This follows the same pattern as other modules (e.g. EV): **behaviour in core, UI in widget**.

---

## Layout Assumptions

This widget is designed for a **12x12 LVGL grid layout**.

Typical footprint:

- **Row span:** 4
- **Column span:** 6

So it occupies a **4×6 block** in a 12×12 grid.

It should also work on similar grid layouts, as long as the page defines a grid and you provide compatible spans/positions.

Example page grid:

    lvgl:
      pages:
        - id: main_page
          layout: 12x12
          widgets:
            - !include
                file: modules/time_date/time_date_widget.yaml
                vars:
                  row_pos: "0"
                  column_pos: "0"
                  row_span: "4"
                  column_span: "6"

---

## What This Module Includes

### `time_date_core.yaml`

Defines a single script:

- `time_update`
  Updates:
  - `current_time` label with `HH:MM`
  - `month_day` label with `Month D`

### `time_date_widget.yaml`

Provides a UI container containing:

- `current_time` label (large)
- `month_day` label (smaller)

This snippet must be included inside an existing `lvgl` page `widgets:` list.

---

## Requirements

### A `time:` source providing `system_time`

The module assumes you already have:

    time:
      - platform: homeassistant   # or sntp
        id: system_time

This normally comes from one of:

- `backlight_time.yaml`
- `time_homeassistant.yaml`
- `time_sntp.yaml`

### A periodic trigger calling `time_update`

This module does **not** schedule itself.
The timer trigger usually lives in the shared time/backlight file, for example:

    # Update time display every minute
    - seconds: 0
      minutes: '*'
      then:
        - script.execute: time_update

That’s intentionally kept outside the module so you can control update frequency globally.

---

## Fonts Used

This widget expects the following font IDs to already exist:

- `font_display_72` (time)
- `font_body_24` (month/day)

---

## Usage

### 1) Include the core module once

    packages:
      time_date_core: !include modules/time_date/time_date_core.yaml

### 2) Include the widget where you want it on a page

Inside the target page’s `widgets:` list:

    - !include
        file: modules/time_date/time_date_widget.yaml
        vars:
          row_pos: "0"
          column_pos: "0"
          row_span: "4"
          column_span: "6"

---

## Notes

- If the time never updates: your `system_time` source exists, but nothing is calling `script.execute: time_update` on a schedule.
- Keeping the scheduler in a shared time/backlight module avoids duplicated timers when multiple pages/modules use the same `system_time`.

---

## Status

Stable and minimal.

Intended as a base building block for the panel’s main “glance” page.