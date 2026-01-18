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
- Switch OFF: ______ mA (expect 0)
- Switch ON, idle: ______ mA (expect <1)

### Test 3: Isolate Peripherals (if Test 2b shows high current)
- OLED removed: ______ mA
- Encoder removed: ______ mA

## Diagnosis
[To be filled based on results]

## Resolution
[To be filled after fix]
