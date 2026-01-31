# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Browser-based Web Bluetooth application for connecting to FTMS (Fitness Machine Service) bike trainers and Zwift Click virtual shifting controllers. Single-file vanilla JavaScript application with no build system.

## Development

No build or test commands - open `index.html` directly in Chrome/Edge with Web Bluetooth enabled.

For testing with real hardware:
- FTMS trainer (e.g., Van Rysel D100)
- Zwift Click controller (optional)

## Architecture

Single `index.html` file containing:
- **HTML**: UI with metrics display, gear controls, resistance/grade sliders
- **CSS**: Dark theme styling (embedded in `<style>`)
- **JavaScript**: All logic in single `<script>` block

### Key Components

**FTMS Trainer Connection** (`connectTrainer()`):
- Uses Web Bluetooth API to connect to service UUID `0x1826`
- Subscribes to Indoor Bike Data (`0x2AD2`) for metrics
- Uses Control Point (`0x2AD9`) for resistance/simulation commands

**Zwift Click Connection** (`connectClick()`):
- Supports both legacy (`00000001-19ca-...`) and new (`0xFC82`) service UUIDs
- Performs "RideOn" handshake on characteristic `0x0003`
- Parses button bitmap from message `0x23` for shift detection

**Data Parsing**:
- `parseIndoorBikeData()`: Parses FTMS flags and extracts speed/cadence/power
- `handleClickInput()`: Decodes Zwift Click button presses with edge detection

### Protocol Notes

FTMS Indoor Bike Data uses inverted flag logic - bit 0 = 0 means speed IS present.

Zwift Click button bitmap uses inverted logic - bit = 0 means pressed:
- `0x0200`: Shift down (easier)
- `0x2000`: Shift up (harder)
