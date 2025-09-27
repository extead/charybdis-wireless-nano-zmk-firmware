# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a ZMK firmware repository for wireless Charybdis keyboards - a split ergonomic keyboard with an integrated trackball. The firmware supports both Bluetooth/USB and dongle configurations using the PMW3610 trackball sensor.

## Build Commands

**Local Development (Recommended)**
```bash
cd local-build
docker-compose run --rm builder
```

This builds all firmware variants locally using Docker. Output files are placed in `firmwares/` directory organized by format (bt/dongle) and keymap.

**Enable USB Logging for Debugging**
Edit `local-build/build_setup.sh` and set `ENABLE_USB_LOGGING="true"` before building.

**GitHub Actions Build**
Push changes to trigger automatic builds. Artifacts are available under the Actions tab.

## Architecture

**Shield Structure**
- `boards/shields/charybdis_bt/` - Bluetooth configuration shields
- `boards/shields/charybdis_dongle/` - Dongle configuration shields
- Each shield contains overlay files for left, right, and (dongle-only) dongle devices

**Configuration Files**
- `config/charybdis_pointer.dtsi` - Trackball input processor configuration (speed, acceleration, scrolling)
- `config/charybdis_pmw3610.dtsi` - PMW3610 sensor configuration
- `config/charybdis-layouts.dtsi` - Physical key layout definitions for ZMK Studio
- `config/keymap/` - Contains keymap files and behavioral components (behaviors.dtsi, combos.dtsi, macros.dtsi)

**Keymap System**
- Default keymaps: `config/keymap/qwerty.keymap`, `config/keymap/colemak_dh.keymap`
- Additional keymaps: `extra-keymaps/` directory
- Keymaps are automatically discovered and built for all formats
- Features timeless home-row mods, thumb-scroll, precision cursor mode, and mouse-click behaviors

**Build Matrix**
- `build.yaml` defines the build matrix for GitHub Actions
- `scripts/generate_matrix.py` generates dynamic build combinations
- Supports building specific keymap/shield combinations

## Development Workflow

**Adding New Keymaps**
1. Create `.keymap` file in `config/keymap/` directory
2. Keymap will be automatically built for all shield formats

**Modifying Trackball Behavior**
Edit `config/charybdis_pointer.dtsi` to adjust tracking speed, acceleration, or scrolling behavior.

**Customizing Physical Layout for ZMK Studio**
Modify `config/charybdis-layouts.dtsi` for key position changes. Use the [ZMK physical layouts converter](https://zmk-physical-layout-converter.streamlit.app/) to convert matrix transforms.

**Building Single Format**
Remove unwanted shield directories from `config/boards/shields/` to build only specific formats (bt or dongle).

## Key Features

- **ZMK Studio Support**: Enabled on BT builds for runtime keymap editing
- **PMW3610 Driver**: Low-power trackball sensor with wake optimization
- **Input Processors**: Modular system for cursor movement, scrolling, and precision modes
- **Home-row Mods**: Timeless implementation with balanced timing (280ms tapping-term)
- **Advanced Thumb Behaviors**: Multi-function thumb keys with tap/hold/double-tap actions

## Firmware Flashing

1. Download firmware from Releases or build artifacts
2. Double-press reset button on device
3. Copy appropriate `.uf2` file to mounted NICENANO device
4. Flash `settings_reset.uf2` first when switching between bt/dongle formats

## Important Notes

- The codebase uses west manifest (`config/west.yml`) for dependency management
- External PMW3610 driver module is loaded via `zmk-pmw3610-driver/`
- Firmware artifacts are organized by format and keymap for easy identification
- USB logging significantly impacts battery life - only enable for debugging