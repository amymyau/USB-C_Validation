## Technical Deep Dive: Overcoming Hardware & Tooling Constraints

### 1. The Challenge: Oscilloscope "Parameter Limited" Error
During high-resolution analysis of the **5V VBUS rail**, the Rigol DS1054Z displayed a **"Parameter Limited"** error. This occurred when attempting to set the trigger level to the target 4.8V while at a high vertical sensitivity (200mV/div).

**Root Cause:**
* **ADC Dynamic Range:** At 200mV/div, the 5V signal was 25 divisions away from the center, exceeding the internal Analog-to-Digital Converter's (ADC) visible window.
* **Vertical Offset Cap:** The scope's internal amplifier hardware capped the vertical offset at 1V when zoomed in, preventing the "centering" of the 5V rail on the grid.

### 2. The Solution: Engineering Workarounds
To bypass these hardware guardrails and achieve a high-precision measurement, I implemented a **Zero-DC Offset Strategy**:

* **AC Coupling Implementation:** Switched the channel from DC to **AC Coupling**. This blocked the steady-state 5V DC component, centering the signal "ripples" at the 0V grid line.
* **Relative Triggering:** Instead of an absolute 4.8V trigger, I calculated and set a **Falling Edge trigger at -200mV**. 
* **Bandwidth Optimization:** Enabled the **20MHz Bandwidth Limit** to filter high-frequency EMI, revealing the underlying 22.73kHz switching regulator sawtooth pattern.

### 3. Captured Hardware Configuration
| Parameter | Setting | Purpose |
| :--- | :--- | :--- |
| **Vertical Scale** | 200mV / div | Maximize resolution of the 5.6% ripple |
| **Horizontal Scale** | 100µs / div | Isolate switching regulator frequency |
| **Coupling** | AC | Bypass 1V Hardware Offset Limit |
| **Trigger Mode** | Normal / Single | Capture specific "Plug-in" transients |
| **Memory Depth** | 24M pts | Ensure high sample rate across long windows |

### 4. Key Lessons for System Validation
* **Tool Architecture:** Successful validation requires an understanding of the test equipment's internal constraints (ADC clipping and amplifier stages).
* **Legacy Power Handshaking:** Confirmed that when using **USB-A to USB-C Legacy cables**, the CC pin correctly remains at 0V,

* VBUS 22kHz Ripple
* ![VBUS 22kHz Ripple](https://github.com/user-attachments/assets/7ff522a0-dd4c-44aa-ad6f-13d86b632bb5)

* Advanced Bandwidth Limiting
* ![transient_test_no_sawtooth_pattern_shifts_downward](https://github.com/user-attachments/assets/6e79d8e1-fdef-41c6-a05b-6671702ba573)


# USB-C_Validation
USB-C_Validation_Technical_Notes
