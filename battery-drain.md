# Battery Drain - Right Half

## Problem
Battery drains in ~24h even with power switch OFF. Battery replaced, still drains.

## Hardware
- Sofle V2 PCB (42. Keebs v2.1)
- nice!nano v2 in sockets
- SMD power switch

## Test Results

### Test 1: Power Switch Continuity
Battery+ pad to nice!nano B+ pin, switch OFF: **No continuity** ✅
**Conclusion:** Switch works. Issue is elsewhere.

### Test 2a: Current Draw (nice!nano removed)
**Setup:** Battery+ desoldered, multimeter in series
- Switch OFF: **0 µA** ✅
- Switch ON: **0 µA** ✅
**Conclusion:** PCB has no short or parasitic drain. Issue is with nice!nano or peripherals.

### Test 2b: Current Draw (nice!nano inserted)
- Switch ON, nice!nano inserted: **3-5 µA** (not booting)
- **Issue:** Battery voltage too low to provide boot current

### Test: B+ Socket Voltage (Multimeter, USB charging)
- nice!nano B+ pin while USB charging: **4.2V** ✅
- **Conclusion:** B+ socket connection is working correctly

### Battery Charging Test
**Date:** 2026-01-18, 22:20
- Battery charged via USB-C for 1.5 hours
- Battery voltage after charging: **4.13V**
- Orange LED did not turn off (may need longer charge time or battery damaged)
- **Test plan:** Leave battery disconnected overnight, measure voltage drop

**Next morning measurement (2026-01-19, ~08:00):**
- Voltage: **4.00V**
- Voltage drop: **0.13V** in ~10 hours
- ❌ Drop > 0.1V → Battery has high self-discharge (damaged)

### Test: Bluetooth Visibility (2026-01-19)
- Device appears in bluetoothctl scan: **"Sofle"** at CA:BB:C3:DA:C8:01
- **Conclusion:** Keyboard boots, nice!nano works, Bluetooth advertising active

## Diagnosis Status: INCOMPLETE

### Components Verified Working
| Component      | Status                         |
| -------------- | ------------------------------ |
| Power switch   | ✅ Working correctly            |
| PCB            | ✅ No shorts or parasitic drain |
| B+ socket      | ✅ Good connection (4.2V seen)  |
| nice!nano      | ✅ Works, Bluetooth functional  |

### Unresolved Questions

**Critical issue:** All batteries tested were from 3-year storage. We cannot determine:
1. Did the keyboard drain batteries excessively from the start (hardware issue)?
2. Or did all batteries simply die from 3 years of storage self-discharge?

**User reports:** "The right half never functioned properly in the long run" - suggests issue existed from the beginning.

### Batteries Tested
| Battery                      | Source              | Result              |
| ---------------------------- | ------------------- | ------------------- |
| Original (in keyboard)       | Used for ~3 years   | Drains quickly      |
| "Fresh" replacement          | Stored 3 years      | Dead (-0.04V)       |
| Current test battery         | Stored 3 years      | 0.13V drop overnight (damaged) |

**All tested batteries are from 3-year storage - no known-good battery available for comparison.**

## Next Steps Required

1. **Order fresh battery** - 301230 110mAh 3.7V LiPo
   - Temu: 8.56 zł (2-3 week delivery)
   - Allegro: 22.62 zł (Tuesday delivery)

2. **Test with RD6006 lab PSU** (see keyboard-debug-rd6006.md)
   - Measure actual idle current draw (should be <1mA)
   - If >5mA at idle → keyboard has hardware drain issue
   - If <1mA → old batteries were just all dead from storage

3. **Overnight test with fresh battery**
   - Fully charge to 4.2V
   - Connect to keyboard, switch ON
   - Measure voltage drop after 24h
   - If drop >0.2V → keyboard has excessive drain
   - If drop <0.1V → keyboard is fine

## Additional Issue Discovered

During testing, discovered **keys `yuiop` on right half do not work** on any layer.
This is a separate matrix/hardware issue. See: `yuiop-keys-broken.md`
