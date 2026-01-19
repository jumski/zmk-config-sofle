# Keyboard Power Debug with RD6006

## Purpose

Use RD6006 lab PSU to test Sofle keyboard power consumption, identify battery drain issues, and validate repairs.

---

## Setup

### Connections

```
RD6006 Output Terminals           Sofle PCB
┌─────────────────┐
│  (+) ──┐       │
│          ├──────→ PCB battery (+) pad
│  (-) ──┘       │
└─────────────────┘
                  └──→ PCB battery (-) pad / GND
```

### Important: Remove Battery First

Before connecting RD6006:
1. **Desolder battery positive wire** from PCB
2. Leave battery disconnected during testing
3. RD6006 will power the keyboard directly

---

## Test 1: Baseline Current with No nice!nano

### Purpose
Verify PCB has no shorts (already done with multimeter, confirm with RD6006)

### Settings

| Parameter | Value  |
| --------- | ------ |
| **Voltage**  | 3.7V   |
| **Current**  | 0.200A (200mA) - set as limit |
| **OVP**     | 3.8V    |

### Procedure

1. Remove nice!nano from sockets
2. Connect RD6006 to PCB battery pads
3. Set voltage to 3.7V, current limit to 0.2A
4. Turn output ON
5. Switch keyboard power switch **ON**

### Expected Results

| Switch Position | Expected Current |
| -------------- | --------------- |
| OFF            | 0.000A          |
| ON             | ~0.000A (or near zero) |

| Actual Reading | Meaning                             |
| ------------ | ----------------------------------- |
| **0.000A**     | PCB clean, no shorts ✅                  |
| **Any current**   | Short on PCB - find and fix          |

---

## Test 2: Current with nice!nano Inserted (Idle)

### Purpose
Measure baseline current draw of working nice!nano at idle (sleep mode)

### Settings

| Parameter | Value    |
| --------- | ------- |
| **Voltage**  | 3.7V     |
| **Current**  | 0.100A (100mA) - set as limit |
| **OVP**     | 3.8V      |

### Procedure

1. Insert nice!nano into sockets
2. Connect RD6006 to PCB battery pads
3. Set voltage to 3.7V, current limit to 0.1A
4. Turn output ON
5. Switch keyboard power switch **ON**
6. Wait 10-20 seconds for nice!nano to boot and enter sleep mode
7. **Read current value**

### Expected Results

| State              | Expected Current | Good Range    |
| ------------------ | --------------- | ------------- |
| Booting           | 10-20 mA       | Normal        |
| Settled/Sleeping  | < 1 mA (0.5-0.8 mA) | Normal |
| OLED active        | 5-10 mA        | Normal if OLED on |

| Actual Reading | Diagnosis                               |
| -------------- | ---------------------------------------- |
| **0.5-1 mA**     | ✅ Nice!nano sleeping properly              |
| **10-20 mA**      | ✅ Nice!nano running (not sleeping)          |
| **> 50 mA**       | ❌ Not sleeping - firmware/configuration issue |
| **0 mA**          | ❌ Not booting - no power to MCU           |
| **Variable/jumpy**  | ❌ Intermittent connection              |

---

## Test 3: Current in Active Use

### Purpose
Measure current when typing, using LEDs, encoders

### Settings

| Parameter | Value    |
| --------- | ------- |
| **Voltage**  | 3.7V     |
| **Current**  | 0.100A (100mA) |
| **OVP**     | 3.8V      |

### Procedure

1. Start from Test 2 (nice!nano inserted and sleeping)
2. Start typing or pressing keys
3. **Watch current on RD6006 display**
4. Test with:
   - RGB underglow ON (if equipped)
   - OLED active
   - Encoder spinning

### Expected Results

| Activity          | Expected Current Peak |
| ----------------- | ------------------- |
| Typing normal keys | 10-15 mA          |
| Encoder spin       | 5-10 mA            |
| OLED change       | 5-10 mA            |
| RGB on           | 20-50 mA           |

| Observation     | Meaning                           |
| -------------- | --------------------------------- |
| **Spikes 10-15 mA during typing** | ✅ Normal                         |
| **Constant high current (>20 mA)**   | ❌ RGB/OLED not turning off, draining battery |
| **Current 0 mA, no response**      | ❌ Not booting                      |

---

## Test 4: Current with Peripherals Isolated

### Purpose
Identify which peripheral is causing excessive drain (if Test 2 shows high idle current)

### Procedure

1. **Test 4a: OLED removed**
   - Disconnect OLED from PCB
   - Measure idle current
   - Expected: 0.3-0.5 mA (drop from 0.8 mA)

2. **Test 4b: Encoder removed**
   - Disconnect encoder
   - Measure idle current
   - Expected: ~0.8 mA (no change)

3. **Test 4c: Both removed**
   - Measure with only nice!nano
   - Expected: 0.2-0.4 mA

### Diagnosis Table

| Test 4 Result          | Diagnosis                          |
| --------------------- | --------------------------------- |
| **Idle drops > 0.3 mA**  | OLED or connected peripheral causing drain |
| **No change**          | Issue is with nice!nano firmware/hardware |

---

## Test 5: Switch Functionality with RD6006

### Purpose
Verify power switch cuts power correctly (already tested with multimeter)

### Procedure

1. Set RD6006: 3.7V, 0.1A limit
2. Connect to PCB battery pads
3. Turn output ON
4. Switch keyboard power **ON** → Note current
5. Switch keyboard power **OFF** → Current should drop to 0.000A

### Expected

| Switch | Current   |
| ------- | -------- |
| ON      | ~1 mA (idle) or as per Test 2 |
| OFF     | 0.000A        |

---

## RD6006 Quick Reference for Keyboard Testing

| Test               | Voltage | Current Limit | Expected Current |
| ------------------ | -------- | ------------- | --------------- |
| PCB only (no MCU) | 3.7V     | 0.2A          | ~0 mA            |
| MCU idle (sleep)   | 3.7V     | 0.1A          | 0.5-1 mA         |
| MCU active         | 3.7V     | 0.1A          | 10-20 mA         |
| RGB on            | 3.7V     | 0.1A          | 20-50 mA         |

---

## Troubleshooting Guide

### Symptom: Current 0 mA, keyboard won't boot

| Possible Cause     | Test/Solution                          |
| ---------------- | ------------------------------------- |
| No power to MCU   | Check battery+ pad → B+ pin connection   |
| Dead nice!nano    | Test with USB-C, if works → power issue |
| Switch issue      | Test continuity (Test 5)               |

### Symptom: High idle current (> 5 mA)

| Possible Cause          | Test/Solution                     |
| --------------------- | --------------------------------- |
| RGB not turning off    | Check ZMK config RGB auto-off settings|
| OLED not sleeping       | Check ZMK config display timeout    |
| Short on peripheral    | Isolate with Test 4               |

### Symptom: Intermittent/jumpy current

| Possible Cause     | Test/Solution                          |
| ---------------- | ------------------------------------- |
| Bad socket pin    | Reseat nice!nano, check pin contact    |
| Cold solder joint  | Reflow solder on battery+/GND pads     |
| Bad switch        | Test switch continuity, replace if needed |

---

## Reference: Our Test Results (Jan 18, 2026)

### Test 1: Switch Continuity (Multimeter)
- Battery+ to B+ with switch OFF: **No continuity** ✅
- Switch breaks circuit correctly

### Test 2a: PCB Current (Multimeter, no nice!nano)
- Switch OFF: **0 µA** ✅
- Switch ON: **0 µA** ✅
- PCB has no shorts

### Test 2b: Current with nice!nano (Multimeter)
- Switch ON, nice!nano inserted: **3-5 µA** (not booting)
- **Suspected cause:** Battery too low to provide boot current

### Test: B+ Voltage (Multimeter, USB charging)
- nice!nano B+ pin while charging: **4.2V** ✅
- B+ socket connection is working

### Current Status
- Battery charging (waiting for orange LED off)
- **Next step:** Test with RD6006 once battery charged or with PSU directly

---

## Notes

- This procedure uses RD6006 as controlled power source to:
  - Simulate battery behavior
  - Measure exact current draw in real-time
  - Identify power consumption issues
- Always set current limit **BEFORE** turning output ON
- Start with conservative limits (0.1A for 110mAh battery equivalent)
- Keyboard expects 3.7V ±10% (3.33V - 4.07V) - use 3.7V standard
