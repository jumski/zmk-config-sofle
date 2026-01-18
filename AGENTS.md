# AGENTS.md - ZMK Config for Sofle Keyboard

## Project Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration repository for a
Sofle split keyboard with Nice!Nano v2 controllers. It uses a custom ZMK fork
(infused-kim/zmk) that adds improved encoder, display, and RGB underglow support.

**This is NOT a traditional code project** - it contains keyboard firmware configuration
files written in Devicetree (DTS) and Kconfig formats.

## File Structure

```
config/
  sofle.keymap    # Main keymap - layers, key bindings, behaviors (Devicetree)
  sofle.conf      # Feature toggles - OLED, encoders, RGB (Kconfig)
  west.yml        # West manifest - ZMK source repository reference
build.yaml        # GitHub Actions build matrix (left/right shields)
.github/workflows/build.yml  # CI pipeline configuration
```

## Build Commands

### Primary Method: GitHub Actions (Recommended)

Builds are triggered automatically on push/PR. To build:

1. Commit and push changes to GitHub
2. Go to Actions tab in the repository
3. Download firmware artifacts (`.uf2` files) from the completed workflow

### Manual Trigger

You can manually trigger a build from GitHub Actions → Build → "Run workflow"

### Local Build (Advanced)

Local builds require West (Zephyr meta-tool) and the ARM toolchain. Not covered here,
but the workflow uses Docker image `zmkfirmware/zmk-build-arm:stable` which can be
used locally:

```bash
# Initialize West workspace
west init -l config
west update
west zephyr-export

# Build left half
west build -s zmk/app -b nice_nano_v2 -- -DZMK_CONFIG=$PWD/config -DSHIELD=sofle_left

# Build right half
west build -s zmk/app -b nice_nano_v2 -- -DZMK_CONFIG=$PWD/config -DSHIELD=sofle_right
```

## Validation

There is no linter or test suite. Validation happens at build time:
- **Syntax errors**: Build will fail with Devicetree parsing errors
- **Invalid keycodes**: Build will fail with undefined reference errors
- **Logic errors**: Must be tested on actual hardware after flashing

Always check GitHub Actions results after pushing changes.

## Keymap File Style Guide (sofle.keymap)

### File Format
- Devicetree syntax (`.dts`/`.dtsi` dialect)
- C-style comments: `/* block */` and `// line`
- SPDX license header required at top

### Includes (order matters)
```c
#include <behaviors.dtsi>
#include <dt-bindings/zmk/keys.h>
#include <dt-bindings/zmk/bt.h>
#include <dt-bindings/zmk/rgb.h>
#include <dt-bindings/zmk/ext_power.h>
```

### Layer Definitions
```c
#define BASE 0
#define LOWER 1
#define RAISE 2
#define ADJUST 3
```
- Use `#define` for layer indices
- Use ALL_CAPS for layer names
- Keep layer numbers sequential starting from 0

### Key Binding Formatting

**Row alignment**: Align keys visually to match physical keyboard layout
```c
bindings = <
&kp GRAVE &kp N1 &kp N2   &kp N3   &kp N4    &kp N5                      &kp N6 &kp N7    &kp N8    &kp N9   &kp N0   &none
&kp ESC   &kp Q  &kp W    &kp E    &kp R     &kp T                       &kp Y  &kp U     &kp I     &kp O    &kp P    &kp BSPC
>;
```

**Spacing conventions**:
- Use spaces to align columns visually
- Large gap between left and right halves (split keyboard)
- Each physical row on its own line

**ASCII art comments**: Include layout diagrams above each layer
```c
// ------------------------------------------------------------------------------------------------------------
// |  `    |  1  |  2  |  3   |  4   |  5   |                   |  6   |  7    |  8    |  9   |   0   |       |
```

### Common Behaviors and Keycodes

| Behavior      | Usage                           | Description                       |
| ------------- | ------------------------------- | --------------------------------- |
| `&kp KEYCODE`   | `&kp A`                           | Key press                         |
| `&mo LAYER`     | `&mo LOWER`                       | Momentary layer                   |
| `&tog LAYER`    | `&tog 1`                          | Toggle layer                      |
| `&trans`        | `&trans`                          | Transparent (pass to lower layer) |
| `&none`         | `&none`                           | No action                         |
| `&bt BT_*`      | `&bt BT_CLR`                      | Bluetooth commands                |
| `&rgb_ug RGB_*` | `&rgb_ug RGB_TOG`                 | RGB underglow                     |
| `&ext_power *`  | `&ext_power EXT_POWER_TOGGLE_CMD` | External power                    |

### Keycode Naming
- Use ZMK keycode names from `<dt-bindings/zmk/keys.h>`
- Numbers: `N0`-`N9` (not `NUM_0`)
- Letters: `A`-`Z` (uppercase)
- Modifiers: `LCTRL`, `RCTRL`, `LSHFT`, `RSHFT`, `LALT`, `RALT`, `LGUI`, `RGUI`
- Special: `BSPC` (backspace), `DEL`, `RET` (enter), `ESC`, `TAB`, `SPACE`

## Config File Style Guide (sofle.conf)

### Format
- Kconfig syntax
- Comments start with `#`
- Boolean options: `CONFIG_*=y` or `CONFIG_*=n`
- No spaces around `=`

### Common Options
```kconfig
CONFIG_ZMK_DISPLAY=y              # OLED display
CONFIG_EC11=y                     # Rotary encoder support
CONFIG_EC11_TRIGGER_GLOBAL_THREAD=y
CONFIG_ZMK_RGB_UNDERGLOW=y        # RGB LEDs
CONFIG_ZMK_RGB_UNDERGLOW_EXT_POWER=n  # Separate RGB from ext power
CONFIG_ZMK_EXT_POWER_USB_ONLY=y   # Power saving
CONFIG_ZMK_RGB_UNDERGLOW_AUTO_OFF_IDLE=y
```

## Common Modifications

### Adding a New Key Binding
1. Find the position in the layer's `bindings` array
2. Replace existing binding (maintain alignment)
3. Use appropriate behavior (`&kp`, `&mo`, etc.)

### Adding a New Layer
1. Add `#define LAYER_NAME N` with next available number
2. Add new layer block in keymap section
3. Add `&mo LAYER_NAME` or `&tog LAYER_NAME` to access it

### Adding Conditional Layers
```c
conditional_layers {
    compatible = "zmk,conditional-layers";
    layer_name {
        if-layers = <LAYER1 LAYER2>;
        then-layer = <RESULT_LAYER>;
    };
};
```

### Modifying Encoder Behavior
```c
sensor-bindings = <&inc_dec_kp C_VOL_UP C_VOL_DN &inc_dec_kp PG_UP PG_DN>;
```
First pair: left encoder, Second pair: right encoder

## Important Notes

1. **This repo uses a ZMK fork** (infused-kim/zmk), not official ZMK. Some features
   may not exist in mainline ZMK.

2. **Split keyboard**: Left and right halves build separately. Both `.uf2` files
   must be flashed to respective halves.

3. **No local testing**: Changes can only be validated by building and flashing
   to physical hardware.

4. **Bluetooth pairing**: Use `&bt BT_CLR` to clear pairings, `&bt BT_SEL N` to
   select profile (0-4).

5. **Layer 0 is default**: The `default_layer` (BASE/0) is active on boot.

## References

- [ZMK Documentation](https://zmk.dev/docs)
- [ZMK Keycodes](https://zmk.dev/docs/codes)
- [ZMK Behaviors](https://zmk.dev/docs/behaviors)
- [Sofle Keyboard](https://github.com/josefadamcik/SofleKeyboard)
- [infused-kim ZMK Fork](https://github.com/infused-kim/zmk/tree/sofle)
