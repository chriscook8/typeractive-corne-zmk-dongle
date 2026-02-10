# Corne Wireless with Dongle - ZMK Configuration

This configuration sets up a Typeractive Corne wireless split keyboard with a Nice Nano dongle acting as the central device.

## Architecture

```
┌─────────────┐     BLE      ┌─────────────┐     BLE      ┌─────────────┐
│  Left Half  │ ───────────► │   Dongle    │ ◄─────────── │ Right Half  │
│ (Peripheral)│              │  (Central)  │              │ (Peripheral)│
└─────────────┘              └──────┬──────┘              └─────────────┘
                                    │ USB
                                    ▼
                              ┌──────────┐
                              │ Computer │
                              └──────────┘
```

- **Dongle**: Nice Nano connected to computer via USB, acts as BLE central
- **Left/Right Halves**: Nice Nano boards on keyboard, act as BLE peripherals

## Files Generated

```
corne/
├── .github/workflows/build.yml    # GitHub Actions build workflow
├── boards/shields/corne_dongle/   # Dongle shield definition
│   ├── Kconfig.defconfig
│   ├── Kconfig.shield
│   ├── corne_dongle.conf
│   └── corne_dongle.overlay
├── config/
│   ├── corne.conf                 # Shared configuration
│   ├── corne.keymap              # Your keymap (customize this!)
│   ├── corne_left.conf           # Left peripheral config
│   ├── corne_right.conf          # Right peripheral config
│   └── west.yml                  # West manifest
├── build.yaml                    # Build matrix
└── README.md
```

## Setup Instructions

### 1. Create GitHub Repository

1. Create a new repository on GitHub
2. Push this configuration to the repository
3. GitHub Actions will automatically build the firmware

### 2. Flash Settings Reset (CRITICAL - Do This First!)

Before flashing the new firmware, you MUST clear existing pairing data on ALL THREE devices:

1. Download `settings_reset-nice_nano_v2.uf2` from the build artifacts
2. For each device (dongle, left half, right half):
   - Double-tap the reset button to enter bootloader mode
   - Copy `settings_reset-nice_nano_v2.uf2` to the NICENANO drive
   - Wait for the device to reboot

### 3. Flash Dongle Firmware

1. Download `corne_dongle-nice_nano_v2.uf2`
2. Double-tap reset on the dongle Nice Nano
3. Copy the `.uf2` file to the NICENANO drive

### 4. Flash Peripheral Firmware

1. Download `corne_left-nice_nano_v2-peripheral.uf2`
2. Double-tap reset on the left keyboard half
3. Copy the `.uf2` file to the NICENANO drive

Repeat for the right half with `corne_right-nice_nano_v2-peripheral.uf2`

### 5. Pairing

After flashing:
1. Connect the dongle to your computer via USB
2. Power on both keyboard halves
3. They should automatically pair with the dongle

If pairing fails, repeat step 2 (settings reset) on all devices.

## Customization

### Keymap

Edit `config/corne.keymap` to customize your layout. The default includes:
- Layer 0: QWERTY base
- Layer 1: Numbers and navigation
- Layer 2: Symbols

### Bluetooth Profiles

The dongle supports 5 BT profiles (in addition to USB). Use `BT_SEL 0-4` to switch profiles and `BT_CLR` to clear the current profile.

### Display Support

Uncomment the display options in `config/corne.conf` if using OLED or nice!view displays.

## Local Building (Optional)

If you prefer to build locally instead of using GitHub Actions:

```bash
# Install west
pip install west

# Initialize workspace
cd corne
west init -l config
west update

# Build dongle
west build -s zmk/app -b nice_nano_v2 -- \
  -DSHIELD=corne_dongle \
  -DZMK_CONFIG="$(pwd)/config" \
  -DZMK_EXTRA_MODULES="$(pwd)/boards" \
  -DCONFIG_ZMK_SPLIT=y \
  -DCONFIG_ZMK_SPLIT_ROLE_CENTRAL=y

# Build left peripheral
west build -s zmk/app -b nice_nano_v2 --pristine -- \
  -DSHIELD=corne_left \
  -DZMK_CONFIG="$(pwd)/config" \
  -DCONFIG_ZMK_SPLIT=y \
  -DCONFIG_ZMK_SPLIT_ROLE_CENTRAL=n

# Build right peripheral
west build -s zmk/app -b nice_nano_v2 --pristine -- \
  -DSHIELD=corne_right \
  -DZMK_CONFIG="$(pwd)/config" \
  -DCONFIG_ZMK_SPLIT=y \
  -DCONFIG_ZMK_SPLIT_ROLE_CENTRAL=n
```

## Troubleshooting

**Keyboard halves not connecting to dongle:**
- Flash settings reset on all three devices
- Ensure dongle is powered via USB before powering keyboard halves
- Check that all devices are running the correct firmware (dongle vs peripheral)

**Latency issues:**
- Uncomment `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` in `corne_dongle.conf` for increased range

**Only one half works:**
- Verify both halves are flashed with peripheral firmware (not central)
- Check `ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS=2` in dongle config
