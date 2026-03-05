# Buttons Module

The button implementations in this project are largely **imported from the original**  
`esphome-modular-lvgl-buttons` project.

These buttons provide ready-made LVGL components for interacting with Home Assistant
entities such as lights, switches, scenes, and covers.

---

## Purpose

Rather than re-implementing a full button system, this project **reuses the proven
button components from `esphome-modular-lvgl-buttons`** and adapts them to fit the
structure of the `esphome-lvgl-panel` framework.

The buttons are designed to be:

- **Self-contained components**
- Easily included in any LVGL page
- Bound directly to Home Assistant entities
- Compatible with the existing project theme and color palette

---

## Changes from the Original Project

The button files have been **imported largely unchanged**.  
Only minimal modifications were made to ensure compatibility with the
layout conventions used in this project.

### Layout Variable Renaming

The original project used:

```
row
column
```

To match the rest of the `esphome-lvgl-panel` widgets, these were renamed to:

```
row_pos
column_pos
```

This ensures consistent layout parameters across all modules.

Example:

Original:

```yaml
grid_cell_row_pos: ${row}
grid_cell_column_pos: ${column}
```

Modified:

```yaml
grid_cell_row_pos: ${row_pos}
grid_cell_column_pos: ${column_pos}
```

---

## Why the Buttons Are Not Split into Core/Widget Files

Most modules in this project follow a **core + widget** separation pattern.

Buttons are an exception.

Each button include contains:

- LVGL widget layout
- Home Assistant service calls
- Entity state bindings
- Visual state updates

Splitting these into separate files would add unnecessary complexity and
provide little benefit, so buttons remain **self-contained components**.

---

## Usage

Buttons are typically included directly inside a page layout.

Example:

```yaml
- !include
    file: esphome-lvgl-panel/modules/buttons/switch_button.yaml
    vars:
      uid: kitchen_light
      page_id: main_page
      row_pos: 0
      column_pos: 0
      row_span: 2
      column_span: 2
      text: "Kitchen"
      icon: "${mdi_lightbulb}"
      entity_id: light.kitchen_main
```

Each button instance must have a **unique `uid`**, which is used to generate
internal ESPHome IDs.

---

## Supported Button Types

The following button types are currently available:

| Button | Purpose |
|------|------|
| `switch_button` | Toggle switch/light entities |
| `scene_button` | Activate Home Assistant scenes |
| `dimmer_light_button` | Light toggle with brightness slider |
| `cover_button` | Control covers (blinds/garage) |
| `color_picker` | RGB light color control |
| `sensor_button` | Display sensor values |
| `time_button` | Time display button |
| `page_button` | Navigate between LVGL pages |
| `local_relay_button` | Control ESPHome local relays |

---

## Design Philosophy

Buttons are treated as **reusable UI components**, while the **page layout**
determines which buttons appear on a given panel.

Because each panel may represent a different room, **button placement and
entity bindings are defined in the device configuration**, not in shared pages.

This allows each display to have a **custom layout while sharing the same
button implementations**.