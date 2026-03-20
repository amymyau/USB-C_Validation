
##  Full-Stack Root Cause Analysis: USB-C Power Integrity
**Project Type:** Hardware Pathfinding & Power Integrity (PI) Validation


### 🛠 The Challenge: Intermittent "Sink" Detection & System Resets
In a high-scale automation environment, edge devices exhibited erratic power negotiation and "unresponsive" states. Standard debugging had failed to isolate the root cause between the USB-C physical layer and the system power delivery network.

### Pathfinding & Root Cause Analysis (RCA)
1. **Architectural Validation (Logic Layer):** * Pathfound hardware-level "Sink" detection failures by implementing precision **$5.1k\Omega$ pull-down resistors** on CC-pins. 
   * This ensured deterministic power negotiation, proving the upstream source was failing to resolve the resistor divider correctly.

2. **Power Integrity (PI) Characterization:** * Conducted high-resolution characterization of the **$5V$ VBUS** rail using a 500MHz oscilloscope (AC-coupled). 
   * Isolated a **$22kHz$ switching ripple** that exceeded the $100mV$ peak-to-peak safety margin, causing downstream logic resets.

3. **The "Path" to Resolution:** * Traced the ripple frequency to **PMIC switching harmonics**. 
   * Provided the architectural "Path" for a hardware redesign of the decoupling network and a firmware adjustment to the switching frequency.

### Technical Impact
* **Operational Excellence:** Eliminated intermittent system resets for 24/7 edge-computing reliability.
* **Cost Savings:** Prevented broad hardware recalls by isolating the issue to a specific decoupling/PMIC harmonic interaction.
* **Validation Standard:** Established a new PI testing protocol for all future USB-C "Sink" hardware deployments.


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
