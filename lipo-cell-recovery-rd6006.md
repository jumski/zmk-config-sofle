# LiPo Cell Recovery with RD6006

## Safety Warnings

⚠️ **CRITICAL SAFETY NOTES:**

- **Never charge LiPo above 4.20V** - risk of fire/explosion
- **Never leave charging unattended** - especially recovering damaged cells
- **Work on fire-resistant surface** (concrete, tile, fireproof mat)
- **Have fire extinguisher nearby** - CO2 or dry powder
- **Do NOT recover cells that are swollen/hot/leaking** - dispose immediately
- **Stop if cell gets warm (over 40°C)** - this is danger sign

---

## Quick Triage: Check All 16 Cells

### Step 1: Measure Open Voltage

1. Set RD6006 **OUTPUT OFF**
2. Connect battery to terminals
3. Read voltage on display (no current flows)
4. Record voltage for each cell
5. Label/mark cells with their voltage

### Step 2: Sort by Voltage

| Voltage Range       | Action                              | Expected Survivors |
| ------------------ | ----------------------------------- | ----------------- |
| **< 2.5V**         | Likely dead, do NOT charge           | 0%                |
| **2.5V - 3.0V**    | Slow recovery attempt (10-20mA)       | 10-20%            |
| **3.0V - 3.5V**    | Normal charge attempt (50-100mA)     | 60-80%            |
| **> 3.5V**         | Normal charge (100mA)                 | 90-100%           |

---

## Recovery Procedure (Cells 2.5V - 3.0V)

### Settings

| Parameter | Value          | Notes                                    |
| --------- | -------------- | ---------------------------------------- |
| **Voltage (CV)** | 4.20V          | Critical - NEVER exceed 4.20V!          |
| **Current (CC)** | 0.010-0.020A  | Start with 10-20mA (very low)          |
| **OVP**        | 4.25V          | Safety override                          |

### Steps

1. **Set RD6006:**
   - Press `SET` → enter voltage → `4.20` → `ENTER`
   - Press `CURRENT` → enter current → `0.01` → `ENTER`
   - Verify OVP limit (usually auto-set to +0.05V)

2. **Connect battery:**
   - Red to positive (+)
   - Black to negative (-)

3. **Turn output ON**
   - Watch voltage and current carefully

4. **Monitor for 5-10 minutes:**
   - Voltage rising? ✅ Cell recovering
   - Current dropping as voltage rises? ✅ Normal CC/CV behavior
   - No change? ❌ Cell may be dead
   - Voltage stuck below 3.0V for >10 min? ❌ Likely dead

5. **If voltage reaches 3.0V:**
   - Increase current to 0.05A (50mA)
   - Monitor for 10 minutes
   - If stable, increase to 0.1A (100mA)

6. **Full charge to 4.20V:**
   - Current will drop near 0mA when complete
   - Total time: 1-2 hours

---

## Normal Charging Procedure (Cells > 3.0V)

### Settings

| Parameter | Value  |
| --------- | ------ |
| **Voltage**  | 4.20V  |
| **Current**  | 0.10A (100mA) |

### Steps

1. Set voltage to 4.20V
2. Set current to 0.10A
3. Connect battery
4. Turn output ON
5. Wait for current to drop to < 5mA (completed)
6. Expected time: 30-60 minutes

---

## Identifying Dead Cells

### Signs of Dead/Damaged Cell

| Symptom                      | Action          |
| ---------------------------- | --------------- |
| Voltage rises to 3.0V, then stalls | Dead - discard  |
| Current spikes randomly            | Damaged - discard|
| Voltage never rises from <2.5V    | Dead - discard  |
| Cell gets warm (>40°C)            | STOP - dangerous|
| Cell is swollen/leaking           | Do NOT charge    |

### Safe Disposal

Dead LiPo cells are hazardous waste. Dispose at:
- Electronic waste recycling center
- Battery recycling drop-off
- Hardware store with battery recycling

**Do NOT throw in regular trash!**

---

## Storage of Recovered Cells

### Long-Term Storage (Months to Years)

1. **Charge to 3.7-3.8V** (40-60%)
2. **Disconnect** from any load/charger
3. **Store at room temperature** (20-25°C)
4. **Check every 6 months** - recharge to 3.7V if below 3.6V

### Short-Term Storage (Days to Weeks)

- Any charge level is fine
- Store at room temperature
- Check before use

---

## RD6006 Quick Reference for LiPo

| Task                   | Voltage | Current | Time        |
| ---------------------- | -------- | -------- | ----------- |
| Charge 110mAh cell     | 4.20V    | 0.10A    | 30-60 min   |
| Recover damaged cell     | 4.20V    | 0.01-0.02A | 1-2 hours   |
| Measure cell voltage    | OFF      | N/A      | Instant      |
| Test cell capacity     | 4.20V    | 0.10A    | Until empty  |

---

## Troubleshooting

| Problem              | Solution                               |
| ------------------- | ------------------------------------- |
| "CURRENT LIMIT" displayed | Battery needs more current than set - increase limit |
| Voltage won't rise    | Cell dead - discard                     |
| RD6006 gets hot     | Reduce current, check cooling            |
| Battery gets hot      | **STOP IMMEDIATELY** - discard         |

---

## Notes

- Your old batteries: Purchased Feb 2023, stored ~3 years
- Expected survival: 1-2 cells at best
- Cells that work: Test full charge cycle, measure actual capacity
- Good cell should deliver close to rated mAh (110mAh)
