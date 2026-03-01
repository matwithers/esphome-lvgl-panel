# Boot Screen Module

This module provides a simple **boot / connection overlay** for the LVGL panel.

It displays a full-screen splash UI while the device starts up, connects to Home Assistant, and (optionally) runs a short “sync” animation before revealing the main UI.

---

## What It Includes

### `boot_screen_core.yaml`
Contains everything needed for the boot overlay:

- `esphome.on_boot` hook (runs early at boot)
- LVGL `top_layer` overlay UI (covers the whole screen)
- Home Assistant connection detection via `api.on_client_connected`
- Scripts to update status text and run a simple progress animation
- Boot assets (Home Assistant + ESPHome logos)

---

## How It Works

1. On boot, `update_boot_screen` runs and sets initial labels:
   - “Connecting to Home Assistant..”
   - “Synchronizing data...”

2. When the API client connects and the client string matches Home Assistant:
   - `on_ha_connected` marks the panel as connected
   - Updates the label to “Home Assistant Connected!”
   - Hides the spinner
   - Switches to a “sync” stage and runs a simple bar animation

3. The boot overlay is finally hidden, revealing the main UI.

The overlay can also be dismissed manually by tapping it (useful during development).

---

## Requirements

- Uses `${screen_width}` and `${screen_height}` substitutions
- Expects `font_body_20` to exist
- Expects `main_page` to exist (used by `lvgl.page.show: main_page`)
- Requires images under:
  - `modules/boot_screen/assets/images/homeassistant.png`
  - `modules/boot_screen/assets/images/esphome.png`

---

## Usage

Include the module in your main YAML:

```yaml
packages:
  boot_screen: !include esphome-lvgl-panel/modules/boot_screen/boot_screen_core.yaml