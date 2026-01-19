# YUIOP Keys Not Working - Right Half

## Problem
Keys `y`, `u`, `i`, `o`, `p` on the right half do not register on any layer.
All other keys on the right half work correctly.

## Hardware
- Sofle V2 PCB (42. Keebs v2.1)
- nice!nano v2 in sockets
- Hotswap sockets (Kailh)
- SMD diodes

## Symptoms

| Observation                     | Result     |
| ------------------------------- | ---------- |
| Keys `yuiop` on BASE layer      | ❌ No output |
| Keys `yuiop` on RAISE layer     | ❌ No output |
| Keys `yuiop` on LOWER layer     | ❌ No output |
| All other right half keys       | ✅ Working   |
| Right half connected via USB-C  | Same issue |

## Testing Done

### RAISE Layer Test
Held RAISE (right inner thumb) + pressed affected keys:
- `y` position (should be PG_UP): **No output**
- `i` position (should be UP arrow): **No output**

### Physical Key Test
- Pressed keys firmly: **No response**
- Keys not tested: reseating switches

## Analysis

### Keyboard Matrix Layout
The affected keys (`yuiop`) are:
- Row 2 (second row from top) of the right half
- Consecutive positions in the same row
- Suggests a **row-related issue** rather than individual key failures

### Likely Causes (In Order of Probability)

| Cause                              | Probability | Repair Difficulty |
| ---------------------------------- | ----------- | ----------------- |
| Cold solder joint on row pin       | High        | Easy              |
| Broken PCB trace for row           | Medium      | Medium-Hard       |
| Bad SMD diode (if row shares one)  | Medium      | Medium            |
| nice!nano socket pin bad connection | Medium      | Easy              |
| Multiple bad hotswap sockets       | Low         | Easy              |
| Damaged nice!nano GPIO pin         | Low         | Hard (replace MCU)|

## Diagnosis Plan

### Step 1: Visual Inspection
- [ ] Check SMD diodes near `yuiop` key positions for damage/cold joints
- [ ] Check nice!nano socket pins for cold solder joints
- [ ] Look for damaged/broken PCB traces

### Step 2: Identify Matrix Row/Column
- [ ] Consult SofleKeyboard schematic (in `SofleKeyboard/Sofle_v2/PCB/`)
- [ ] Identify which GPIO pin controls the row for `yuiop`
- [ ] Identify which diodes are in the circuit for those keys

### Step 3: Continuity Testing (Multimeter)
- [ ] Test continuity from nice!nano row pin to first key in row
- [ ] Test each diode in the affected row
- [ ] Test hotswap socket connections

### Step 4: Repair
Based on findings:
- Cold solder joint → Reflow with soldering iron + flux
- Broken trace → Solder jumper wire
- Bad diode → Replace SMD diode
- Bad socket → Replace hotswap socket

## Tools Needed
- Soldering iron with fine tip
- Flux
- Multimeter (continuity mode)
- Magnifying glass or microscope
- Possibly: replacement SMD diodes, thin wire (30AWG)

## Reference Files
- Schematic: `SofleKeyboard/Sofle_v2/PCB/SofleKeyboard.sch`
- PCB layout: `SofleKeyboard/Sofle_v2/PCB/SofleKeyboard.kicad_pcb`
- Debug guide: `keyboard-debug-rd6006.md`

## Notes
- Issue discovered during battery drain debugging (2026-01-19)
- User unsure if keys ever worked - possibly broken since initial build
- Right half has never functioned reliably long-term
