# HOAS-REMOTE
# Universal Remote — Home Assistant

## Architecture

This project implements a universal remote control as a Home Assistant OS dashboard.
No real hardware is required. All device state is simulated through HA helpers and
template entities.

### Entity model
- `input_select.active_source` — tracks which of the 4 sources is active
- `input_boolean.tv_power`, `input_boolean.avr_power` — power state per device
- `input_number.avr_volume` — volume level (0–100)
- Template `media_player` entities for TV, AVR, and all 4 sources (state is derived
  from the helpers above, so pressing a button in the UI causes real entity state changes)

### Mock logic
When a source button is pressed, it calls `script.select_source` with a `source`
parameter. The script turns on both devices (if off), then sets `active_source`.
All 6 template media_players re-evaluate their state templates immediately — the
selected source becomes `playing`, the rest return to `standby`.

### Dashboard
Two views:
1. **Remote** — the main control surface (power, source, volume, navigation, playback)
2. **Debug** — live entity states and logbook for verifying backend changes

### How to extend
- Add a 5th source: add the option to `input_select.active_source`, add a button-card
  in the dashboard, and add a new template media_player following the same pattern.
- Add real device control: replace the script actions with the appropriate service
  calls (e.g. `media_player.select_source` on a real Apple TV integration).
- Add input-specific navigation: use `conditional` cards that only show when
  `active_source` equals a given value.

## Setup

1. Install HAOS in a VM (VirtualBox/UTM, 2+ GB RAM, bridged networking)
2. Paste `configuration.yaml` additions and restart
3. Install HACS → install button-card, mushroom, card-mod
4. Paste dashboard YAML into raw config editor
5. Done — access at `http://<vm-ip>:8123`
