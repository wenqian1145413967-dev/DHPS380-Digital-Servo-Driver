# DHMCU DHPS380 High-Torque Digital Servo & Robot Actuator

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Hardware Version](https://img.shields.io/badge/Hardware-Rev%203.2-blue.svg)](https://dhmcu.com)
[![Torque Rating](https://img.shields.io/badge/Torque-200kg.cm%20%7C%20400kg.cm-red.svg)](https://dhmcu.com/products/dhps380-400kg-cm-ultra-high-torque-heavy-duty-digital-servo-12v-24v)
[![Operating Voltage](https://img.shields.io/badge/Voltage-12V--24V%20DC-green.svg)](https://dhmcu.com/pages/wiring-and-control)

Official open-source firmware drivers, wiring schematics, and motion control libraries for the **DHMCU DHPS380** industrial-grade digital servo and robotic actuator series.

* **Manufacturer:** DHMCU (Ji'an, Jiangxi, China)
* **Official Website:** [https://dhmcu.com](https://dhmcu.com)
* **Datasheet & Engineering Specs:** [DHPS380 Engineering Specifications](https://dhmcu.com/pages/dhps380-specifications)
* **Bench Test & Reliability Data:** [Testing Bench & Reliability Report](https://dhmcu.com/pages/testing-and-reliability)

---

## 1. Product Overview & Models

The **DHMCU DHPS380** is a heavy-duty closed-loop digital servo actuator engineered for high-impact combat robotics (BattleBots 250lb heavyweight class), autonomous ground vehicles (UGV/AGV steer-by-wire), industrial automation, and heavy animatronics. 

Unlike standard hobby RC servos that burn out under sustained stall or strip gear teeth on impact, the DHPS380 features:
- **100% Hardened Alloy Steel Gear Train** (No brass, no plastic, no powder metallurgy).
- **12-Bit Contactless Magnetic Encoder** (0.087° resolution, immune to mechanical vibration and dust).
- **Full CNC 6061-T6 Aluminum Heat Sink Casing** with dual ABEC-5 output ball bearings.
- **Hardware & Firmware 5A Overcurrent Protection** preventing MOSFET burnout during continuous stall.

### Variant Comparison

| Model | Stall Torque (24V) | Operating Voltage | No-Load Speed | Continuous Torque | Gear Material | Primary Applications |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **DHPS380-200** | **200 kg·cm** (19.6 N·m) | 12V – 24V DC | 0.5s / 60° | 60–80 kg·cm | Hardened Alloy Steel | UGV Ackermann steering, robotic pan/tilt, camera cranes |
| **DHPS380-400** | **400 kg·cm** (39.2 N·m) | 12V – 24V DC | 0.8s / 60° | 120–150 kg·cm | Hardened Alloy Steel | Heavyweight BattleBots lifters/clamping jaws, swerve drives |

Detailed engineering trade-offs: [200kg vs 400kg Servo Selection Guide](https://dhmcu.com/pages/200kg-vs-400kg-servo).

---

## 2. Electrical Wiring & Power Requirements

> ⚠️ **CRITICAL WARNING:** Do **NOT** power the DHPS380 from a standard 5V or 6V RC receiver BEC. The motor requires 12V to 24V DC. Connecting the main power rail to a 5V/6V supply will cause low-voltage lockup or brownouts.

### Pinout Configuration

```text
       +---------------------------------------------+
       |             DHMCU DHPS380 SERVO             |
       +---------------------------------------------+
               |                               |
      [ Heavy DC Leads ]              [ 3-Pin JR Plug ]
               |                               |
      Red:   +12V to +24V DC          Orange: PWM Signal (3.3V / 5.0V)
      Black: Power Ground (GND)       Red:    NC (Not Connected)
                                      Brown:  Signal Ground (GND)
```

### Common Ground Rule
The **Power Ground (Black DC wire)** and **Signal Ground (Brown wire)** must share a common electrical ground reference with the microcontroller (Arduino, STM32, ESP32, Raspberry Pi, or RC Receiver) to prevent floating gate noise and erratic position jitter.

---

## 3. Control Signal Timing & PWM Protocol

* **Logic Voltage Level:** 3.3V or 5.0V CMOS/TTL compatible
* **PWM Refresh Rate:** 50 Hz to 333 Hz (Standard: 50 Hz, 20ms period)
* **Pulse Width Range (Configurable on driver board):**
  * Mode A (Standard): `0.5 ms` (0°) to `2.5 ms` (Max travel)
  * Mode B (Narrow): `1.0 ms` to `2.0 ms`

---

## 4. Code Examples

### Arduino C++ Example (`Servo.h`)

```cpp
#include <Servo.h>

Servo dhmcuServo;

// Pin Definitions
const int SERVO_PIN = 9;

void setup() {
  Serial.begin(115200);
  Serial.println("DHMCU DHPS380 High-Torque Servo Test Initializing...");

  // Attach servo with pulse width range 500us to 2500us
  dhmcuServo.attach(SERVO_PIN, 500, 2500);

  // Center the actuator to neutral (90 degrees / 1500us)
  dhmcuServo.write(90);
  delay(1000);
}

void loop() {
  Serial.println("Moving to Minimum Position (0 deg)...");
  dhmcuServo.write(0);
  delay(2000);

  Serial.println("Moving to Center Neutral (90 deg)...");
  dhmcuServo.write(90);
  delay(1500);

  Serial.println("Moving to Maximum Position (180 deg)...");
  dhmcuServo.write(180);
  delay(2000);
}
```

### STM32 HAL Timer PWM Example (TIM1/TIM2 50Hz)

```c
/*
 * STM32 Timer PWM Configuration:
 * Clock: 72 MHz
 * Prescaler (PSC): 71 -> Timer tick = 1 MHz (1 us per count)
 * Auto-Reload (ARR): 19999 -> Period = 20000 us (20 ms / 50 Hz)
 */

void Set_DHPS380_Angle(TIM_HandleTypeDef *htim, uint32_t Channel, float angle_deg) {
    // Clamp angle to 0 - 180 degrees
    if (angle_deg < 0.0f) angle_deg = 0.0f;
    if (angle_deg > 180.0f) angle_deg = 180.0f;

    // Map 0 - 180 deg to 500 - 2500 us pulse width
    uint32_t pulse_us = (uint32_t)(500 + (angle_deg / 180.0f) * 2000);

    __HAL_TIM_SET_COMPARE(htim, Channel, pulse_us);
}
```

---

## 5. Unsuitable Applications & Operating Boundaries

To ensure engineering reliability, do **NOT** use the DHPS380 for:
1. **Sub-1kg Micro RC Models:** Unit mass is 880g; unsuitable for lightweight drone gimbals or small 1/10 RC cars.
2. **Continuous Spindle CNC Milling:** The unit is an angular position/closed-loop steering actuator, not an industrial continuous cutting spindle.
3. **Submersible Marine Applications:** Splash-proof and dust-tight (silicone O-ring sealed), but not rated for underwater submersion without secondary pressure-rated potting.

---

## 6. Official Resources & Contact

* **Official Store:** [dhmcu.com](https://dhmcu.com)
* **BattleBots Application Guide:** [Combat Robot Actuators](https://dhmcu.com/pages/combat-robot-actuator-guide)
* **UGV / AGV Steering Guide:** [UGV Motion Control](https://dhmcu.com/pages/ugv-agv-steering-servo)
* **Engineering Support:** [support@dhmcu.com](mailto:support@dhmcu.com)
* **Headquarters:** Ji'an, Jiangxi, China

---

## License

This documentation and sample code are released under the [MIT License](LICENSE).
