# Backlight Module (`backlight_core.yaml`)

## Overview

The **Backlight Module** controls display brightness and idle behaviour for LVGL-based ESPHome panels.

It provides:

- 🌞 Sun-driven Day / Evening brightness modes  
- ⏱ Configurable idle timeout  
- 🌙 Optional idle behaviour (off / dim / none)  
- 👆 Wake-on-touch handling via `!extend`  
- 🔁 Periodic sun state polling  

This module is designed to be fully modular and configurable via `packages:` `vars:`.

---

## Requirements

This module requires:

- A configured `sun:` component (typically defined centrally in `environment.yaml`)
- A backlight light entity:
  ```yaml
  light:
    - platform: ledc
      id: display_backlight
  ```
- A touchscreen defined as a list item with:
  ```yaml
  touchscreen:
    - platform: gt911
      id: my_touchscreen
  ```

⚠️ The touchscreen must be defined as a **list item** (using `-`) to allow `!extend`.

---

## Substitutions

These can be overridden per device via `packages:` → `vars:`.

```yaml
touchscreen_daytime_brightness: "100%"
touchscreen_evening_brightness: "50%"
touchscreen_idle_timeout: "30s"

# Idle behavior when in Evening mode:
# - off  -> backlight off + lvgl.pause
# - dim  -> backlight to touchscreen_idle_brightness
# - none -> do nothing
touchscreen_idle_behavior: "dim"
touchscreen_idle_brightness: "5%"

# How often to poll sun state (e.g. "30s", "5min", "10min")
touchscreen_poll_interval: "5min"
```

### Notes

- `touchscreen_poll_interval` accepts standard ESPHome time formats:
  - `"30s"`
  - `"1min"`
  - `"5min"`
- Polling is lightweight and safe.

---

## Behaviour Model

### Day Mode
- Backlight set to `touchscreen_daytime_brightness`
- No idle dimming/off

### Evening Mode
- Backlight set to `touchscreen_evening_brightness`
- On idle:
  - `off` → backlight OFF + LVGL paused
  - `dim` → backlight set to `touchscreen_idle_brightness`
  - `none` → no change

### Touch Wake
On touchscreen interaction:

- LVGL resumes
- Screen redraws
- Brightness restores to current mode (Day / Evening)

---

## Example Package Usage

```yaml
packages:
  hardware: !include esphome-lvgl-panel/hardware/guition-esp32-s3-4848s040.yaml

  backlight:
    !include
      file: esphome-lvgl-panel/modules/backlight/backlight_core.yaml
      vars:
        touchscreen_daytime_brightness: "66%"
        touchscreen_evening_brightness: "33%"
        touchscreen_idle_behavior: "dim"
        touchscreen_idle_brightness: "25%"
        touchscreen_poll_interval: "5min"
```

---

## Architecture Notes

- Sun transitions are determined via polling and only update brightness mode if the mode changes.
- This prevents unwanted wake-ups while in idle/off state.
- No globals are used.
- No hardware edits required (module extends `my_touchscreen`).

---

## Recommended Profiles

### Living Areas
```yaml
touchscreen_daytime_brightness: "60-70%"
touchscreen_evening_brightness: "30-40%"
touchscreen_idle_behavior: "dim"
touchscreen_idle_brightness: "20-25%"
```

### Bedrooms
```yaml
touchscreen_idle_behavior: "off"
```

---

## Design Goals

- Stable modular architecture
- No random wake-ups
- Minimal LED wear
- Clean separation of hardware and behaviour
- Safe multi-room deployment

---

Backlight module is designed for long-term always-on wall panel use.