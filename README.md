# ESPHome LVGL Panel (Guition 4848S040 Edition)

This project is a customised LVGL-based touchscreen panel built with ESPHome, specifically tailored for the **Guition 4848S040 (480x480) display**.

It is heavily based on the excellent work from:

https://github.com/agillis/esphome-modular-lvgl-buttons

This repository began as a fork of that project and gradually evolves toward a Guition-focused, opinionated panel configuration with simplified styling, refined layout decisions, and tighter integration with Home Assistant.

---

## Purpose

The goal of this repository is to provide a clean, minimal wall-panel UI designed for real-world household use.

Primary focus:

1. “What’s the weather doing today?”
2. “Does the car need charging?”

Everything else is secondary.

The interface is designed to be:

- Glanceable  
- Calm  
- Uncluttered  
- Balanced  
- Fast  

Alerting (for example heating activity) is contextual and subtle — not full-screen intrusive.

---

## Hardware Target

This configuration is specifically tuned for:

- Guition 4848S040  
- 480×480 resolution  
- ESP32-based  
- Capacitive touch  
- LVGL via ESPHome  

While components may work on similar displays, this project assumes the Guition layout and dimensions.

---

## Typography

The upstream project used Nunito.  
This version replaces it with Inter and introduces semantic font IDs to decouple:

- Font file  
- Font size  
- Font purpose  

This makes it easy to change typography later without rewriting layout code.

---

## Weather Icons

Weather icons are based on the excellent **Weather Icons** project by Erik Flowers:

https://github.com/erikflowers/weather-icons

Icons used in this project are derived from that set and have been:

- Converted to SVG assets for ESPHome use  
- Recoloured to match the panel’s light/dark theme system  
- Normalised for consistent sizing within a 480×480 layout  
- Slightly adapted where necessary (e.g. `unknown` and `unavailable` states)

All credit for the original icon designs belongs to Erik Flowers.  
This project only adapts and styles the assets for embedded display use.

---

## Key Differences From Upstream

- Integer temperature display (no decimal clutter)
- Custom font system
- Simplified theme styling
- Removal of unnecessary descriptive text in favour of icon-driven UI
- Layout adjustments to improve visual balance on a 480×480 square display
- Preparation for EV charge/range display
- Preparation for room climate indicators
- Gradual internalisation of upstream components for hardware-specific refinement

Over time, dependency on the original repository will reduce as components are customised and owned locally.

---

## Home Assistant Requirements

- ESPHome integration
- Device allowed to call Home Assistant services (required for weather forecast calls)
- A configured weather entity
- Optional: Met Office integration for UK-focused accuracy

---

## Credits

Massive thanks to **agillis** for creating the original modular LVGL button framework.  
Weather icon designs by **Erik Flowers** (Weather Icons project).

This project builds upon both foundations with hardware-specific refinement and a more opinionated UI direction.

---

## Status

Active development.  
Iterative UI experimentation.  
Focused on building a clean, reliable, real-world wall panel.