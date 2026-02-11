# Typeractive Corne Wireless - Dongle Configuration

ZMK firmware configuration for a Typeractive Corne 6-column wireless split keyboard using a Nice Nano as a USB dongle.

## Architecture

```
┌─────────────────┐     BLE      ┌─────────────┐     BLE      ┌──────────────────┐
│  Left Keyboard  │ ───────────► │   Dongle    │ ◄─────────── │  Right Keyboard  │
│  (Peripheral)   │              │  (Central)  │              │   (Peripheral)   │
└─────────────────┘              └──────┬──────┘              └──────────────────┘
                                        │ USB
                                        ▼
                                  ┌──────────┐
                                  │ Computer │
                                  └──────────┘
```

- **Dongle**: Nice Nano v2 connected to computer via USB, acts as BLE central
- **Left/Right Halves**: Nice Nano v2 boards on keyboard halves, act as BLE peripherals

## Features

- Wireless dongle setup with both keyboard halves as peripherals
- ZMK Studio support for real-time keymap editing
- 5 Bluetooth profiles for connecting to multiple devices
- Deep sleep support on keyboard halves for battery savings

## Repository Structure

```
├── .github/workflows/build.yml     # GitHub Actions build workflow
├── boards/shields/corne_dongle_setup/
│   ├── Kconfig.defconfig           # Shield configuration defaults
│   ├── Kconfig.shield              # Shield definitions
│   ├── corne.dtsi                  # Shared devicetree configuration
│   ├── corne.keymap                # Keymap (edit this for layout changes)
│   ├── corne_dongle.overlay        # Dongle devicetree overlay
│   ├── corne_peripheral_left.overlay   # Left half overlay
│   └── corne_peripheral_right.overlay  # Right half overlay
├── config/
│   ├── corne_dongle.conf           # Dongle-specific settings
│   ├── corne_peripheral_left.conf  # Left half settings
│   ├── corne_peripheral_right.conf # Right half settings
│   └── west.yml                    # West manifest
├── zephyr/module.yml               # ZMK module configuration
└── build.yaml                      # Build matrix configuration
```

## Firmware Files

After building, download from GitHub Actions artifacts:

| File | Device | Description |
|------|--------|-------------|
| `corne_dongle-nice_nano.uf2` | Dongle | Central device firmware |
| `corne_left-nice_nano.uf2` | Left keyboard | Left peripheral firmware |
| `corne_right-nice_nano.uf2` | Right keyboard | Right peripheral firmware |
| `settings_reset-nice_nano.uf2` | All devices | Clears pairing data |

## Initial Setup

### 1. Flash Settings Reset (All Devices)

Clear existing pairing data on all three devices:

1. Double-tap reset button to enter bootloader
2. Copy `settings_reset-nice_nano.uf2` to the NICENANO drive
3. Repeat for dongle, left half, and right half

### 2. Flash Dongle

1. Double-tap reset on the dongle Nice Nano
2. Copy `corne_dongle-nice_nano.uf2` to the drive
3. Connect dongle to computer via USB

### 3. Flash Keyboard Halves

**Left half:**
1. Double-tap reset
2. Copy `corne_left-nice_nano.uf2` to the drive

**Right half:**
1. Double-tap reset
2. Copy `corne_right-nice_nano.uf2` to the drive

### 4. Pairing

After flashing all devices:
1. Ensure dongle is connected to computer via USB
2. Power on both keyboard halves
3. They should automatically pair with the dongle

## Default Keymap

### Base Layer
```
┌─────┬─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┬─────┐
│ TAB │  Q  │  W  │  E  │  R  │  T  │   │  Y  │  U  │  I  │  O  │  P  │BKSP │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│CTRL │  A  │  S  │  D  │  F  │  G  │   │  H  │  J  │  K  │  L  │  ;  │  '  │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│SHIFT│  Z  │  X  │  C  │  V  │  B  │   │  N  │  M  │  ,  │  .  │  /  │ ESC │
└─────┴─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┴─────┘
                  │ GUI │LWR  │ SPC │   │ ENT │RSE  │ ALT │
                  └─────┴─────┴─────┘   └─────┴─────┴─────┘
```

### Lower Layer (Hold LWR)
```
┌─────┬─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┬─────┐
│ TAB │  1  │  2  │  3  │  4  │  5  │   │  6  │  7  │  8  │  9  │  0  │BKSP │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│BTCLR│ BT1 │ BT2 │ BT3 │ BT4 │ BT5 │   │  ←  │  ↓  │  ↑  │  →  │     │ OUT │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│SHIFT│UNLCK│     │     │     │     │   │     │     │     │     │     │     │
└─────┴─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┴─────┘
                  │ GUI │     │ SPC │   │ ENT │     │ ALT │
                  └─────┴─────┴─────┘   └─────┴─────┴─────┘
```

### Raise Layer (Hold RSE)
```
┌─────┬─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┬─────┐
│ TAB │  !  │  @  │  #  │  $  │  %  │   │  ^  │  &  │  *  │  (  │  )  │BKSP │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│CTRL │     │     │     │     │     │   │  -  │  =  │  [  │  ]  │  \  │  `  │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│SHIFT│     │     │     │     │     │   │  _  │  +  │  {  │  }  │  |  │  ~  │
└─────┴─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┴─────┘
                  │ GUI │     │ SPC │   │ ENT │     │ ALT │
                  └─────┴─────┴─────┘   └─────┴─────┴─────┘
```

## ZMK Studio

This configuration includes ZMK Studio support for real-time keymap editing.

1. Connect the dongle to your computer
2. Open [ZMK Studio](https://zmk.studio)
3. Press **Lower + Z** to unlock the keyboard for editing
4. Make changes to your keymap in the web interface

## Bluetooth Profiles

The dongle supports 5 Bluetooth profiles. Use the Lower layer to manage them:

- **BT1-BT5**: Select Bluetooth profile
- **BTCLR**: Clear current profile's pairing

## Output Switching

The dongle can output to either USB or Bluetooth, allowing you to connect to a phone or tablet via Bluetooth even while USB is plugged in:

- **OUT** (Lower + '): Toggle between USB and Bluetooth output
- When set to Bluetooth, the dongle uses the currently selected BT profile

## Customization

### Editing the Keymap

Edit `boards/shields/corne_dongle_setup/corne.keymap` to customize your layout.

### Configuration Options

Edit the `.conf` files in `config/` to enable features:

- `corne_dongle.conf` - Dongle settings
- `corne_peripheral_left.conf` - Left half settings
- `corne_peripheral_right.conf` - Right half settings

## Troubleshooting

**Keyboard halves not connecting:**
- Flash `settings_reset` on all three devices
- Ensure dongle is powered via USB before powering keyboard halves
- Verify correct firmware on each device

**Only one half works:**
- Verify both halves have peripheral firmware (not dongle firmware)
- Check that `ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS=2` in dongle config

**Keys not responding:**
- Confirm the dongle is recognized as a USB HID device
- Check that the keyboard halves are paired (may take a few seconds)

**ZMK Studio not connecting:**
- Ensure you're using the dongle firmware with Studio support
- Press Lower + Z to unlock the keyboard

## Building Locally

```bash
# Install west
pip install west

# Initialize workspace
west init -l config
west update

# Build dongle
west build -s zmk/app -b nice_nano_v2 -- \
  -DSHIELD=corne_dongle \
  -DZMK_CONFIG="$(pwd)/config" \
  -DZMK_EXTRA_MODULES="$(pwd)"

# Build left peripheral
west build -s zmk/app -b nice_nano_v2 --pristine -- \
  -DSHIELD=corne_peripheral_left \
  -DZMK_CONFIG="$(pwd)/config" \
  -DZMK_EXTRA_MODULES="$(pwd)"

# Build right peripheral
west build -s zmk/app -b nice_nano_v2 --pristine -- \
  -DSHIELD=corne_peripheral_right \
  -DZMK_CONFIG="$(pwd)/config" \
  -DZMK_EXTRA_MODULES="$(pwd)"
```

## Credits

- [ZMK Firmware](https://zmk.dev)
- [Typeractive](https://typeractive.xyz)
- Based on the [Corne keyboard](https://github.com/foostan/crkbd) by foostan
