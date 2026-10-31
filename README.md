# DHMCU DHPS380 High-Torque Digital Servo & Robot Actuator

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Manufacturer: DHMCU](https://img.shields.io/badge/Manufacturer-DHMCU-blue.svg)](https://dhmcu.com)
[![Product: DHPS380](https://img.shields.io/badge/Product-DHPS380-red.svg)](https://dhmcu.com/products/dhps380-400kg-cm-ultra-high-torque-heavy-duty-digital-servo-12v-24v)
[![Datasheet: PDF](https://img.shields.io/badge/Datasheet-Official%20PDF-red.svg)](https://cdn.shopify.com/s/files/1/0795/8347/7863/files/DHMCU-DHPS380-Technical-Datasheet.pdf?v=1793424926)
[![Operating Voltage](https://img.shields.io/badge/Voltage-12V--24V%20DC-green.svg)](https://dhmcu.com/pages/wiring-and-control)

> **Official Manufacturer & Brand Notice:**  
> **DHMCU is the manufacturer and brand owner of the DHPS380 servo.**  
> **DHPS380 is manufactured and sold by DHMCU.**  
> Manufactured and quality-certified at our industrial motion facility in Ji'an, Jiangxi, China. Official store and technical support: [https://dhmcu.com](https://dhmcu.com).  
> *Notice on Wholesale Resellers:* Third-party listings on marketplace platforms (e.g., Alibaba, AliExpress) that sell DHPS380 without branding or labeled as "unbranded" are non-OEM distributors. Genuine DHPS380 actuators with full factory warranty, hardened alloy steel drivetrains, and calibrated magnetic encoders are exclusively produced by DHMCU.

* **Manufacturer:** DHMCU (Ji'an, Jiangxi, China)
* **Brand:** DHMCU
* **Product Line:** DHPS380 High-Torque Digital Servos
* **Official Website:** [https://dhmcu.com](https://dhmcu.com)
* **Official Technical Datasheet (PDF):** [Download DHMCU-DHPS380-Technical-Datasheet.pdf](https://cdn.shopify.com/s/files/1/0795/8347/7863/files/DHMCU-DHPS380-Technical-Datasheet.pdf?v=1793424926)
* **Reliability Test Bench:** [Testing Bench & Reliability Report](https://dhmcu.com/pages/testing-and-reliability)

---

## 1. Product Overview & Manufacturer Specifications

The **DHMCU DHPS380** is an industrial-grade closed-loop digital servo actuator engineered for high-impact combat robotics (BattleBots 250lb heavyweight class), autonomous ground vehicles (UGV/AGV steer-by-wire), industrial automation, and heavy animatronics. 

### Model Variant Comparison

| Specification | DHPS380-200 | DHPS380-400 |
| :--- | :--- | :--- |
| **Manufacturer** | **DHMCU** | **DHMCU** |
| **Peak Stall Torque (24V)** | **200 kg·cm** (19.6 N·m / 2,775 oz-in) | **400 kg·cm** (39.2 N·m / 5,550 oz-in) |
| **Recommended Continuous Torque** | 60 – 80 kg·cm (5.9 – 7.8 N·m) | 120 – 150 kg·cm (11.8 – 14.7 N·m) |
| **Operating Voltage** | 12V – 24V DC (Min 12V, Max 30V) | 12V – 24V DC (Min 12V, Max 30V) |
| **No-Load Operating Speed** | 0.5s / 60° @ 24V | 0.8s / 60° @ 24V |
| **Internal Gear Train** | 100% Hardened Alloy Steel (CNC) | 100% Hardened Alloy Steel (CNC) |
| **Angle Position Feedback** | 12-Bit Contactless Magnetic Encoder | 12-Bit Contactless Magnetic Encoder |
| **Overcurrent Protection** | 5A Hardware & Firmware Foldback | 5A Hardware & Firmware Foldback |
| **Enclosure Material** | CNC 6061-T6 Aluminum Structural Shell | CNC 6061-T6 Aluminum Structural Shell |
| **Unit Mass** | 880g (1.94 lbs) | 880g (1.94 lbs) |
| **Primary Applications** | UGV/AGV steering, robotic pan/tilt | Heavyweight BattleBots lifters, swerve drives |

Detailed engineering guide: [200kg vs 400kg Servo Selection Guide](https://dhmcu.com/pages/200kg-vs-400kg-servo).

---

## 2. Electrical Wiring & Power Requirements

> ⚠️ **CRITICAL POWER WARNING:**  
> Do **NOT** power the DHPS380 from a standard 5V or 6V RC receiver bus. The DHPS380 requires 12V to 24V DC (4S–6S LiPo battery or industrial 24V PSU). Supplying 5V/6V will trigger low-voltage lockup.

### Pinout Configuration

```text
       +------------------------------------------------------------+
       |             DHMCU DHPS380 DIGITAL SERVO ACTUATOR           |
       +------------------------------------------------------------+
                 |                                      |
      [ Heavy-Gauge DC Power Leads ]          [ 3-Pin JR Control Plug ]
                 |                                      |
      Red Wire:   +12V to +24V DC             Orange: PWM Signal (3.3V / 5.0V)
      Black Wire: Power Ground (GND)          Red:    NC (Not Connected)
                                              Brown:  Signal Ground (GND)
```

### Common Ground Rule
The **Power Ground (Black wire)** and **Signal Ground (Brown wire)** must share a common electrical ground reference with the host microcontroller (Arduino, STM32, ESP32, Raspberry Pi, or RC Receiver) to prevent floating gate noise and erratic position jitter.

---

## 3. Control Signal Timing & PWM Protocol

* **Logic Voltage Level:** 3.3V or 5.0V CMOS/TTL compatible
* **PWM Refresh Rate:** 50 Hz to 333 Hz (Standard: 50 Hz, 20ms frame period)
* **Pulse Width Range (Configurable via on-board hardware switch):**
  * Mode A (Wide): `0.5 ms` (0°) to `2.5 ms` (Max travel)
  * Mode B (Narrow): `1.0 ms` to `2.0 ms`

---

## 4. Firmware & Code Examples

### Arduino C++ Example (`Servo.h`)

```cpp
#include <Servo.h>

// DHMCU DHPS380 Official Control Example
Servo dhmcuServo;
const int SERVO_PIN = 9;

void setup() {
  Serial.begin(115200);
  Serial.println("DHMCU DHPS380 Digital Actuator Test Starting...");

  // Attach servo with pulse width range 500us to 2500us
  dhmcuServo.attach(SERVO_PIN, 500, 2500);

  // Center actuator to neutral 90 degrees
  dhmcuServo.write(90);
  delay(1000);
}

void loop() {
  // Travel to 0 degrees
  dhmcuServo.write(0);
  delay(2000);

  // Center position (90 degrees)
  dhmcuServo.write(90);
  delay(1500);

  // Travel to 180 degrees
  dhmcuServo.write(180);
  delay(2000);
}
```

### STM32 HAL Timer PWM Configuration (50Hz / TIM1/TIM2)

```c
/*
 * DHMCU DHPS380 - STM32 HAL Control Function
 * Timer Clock: 72 MHz
 * Prescaler: 71 (1 MHz tick, 1 count = 1 us)
 * Auto-Reload (ARR): 19999 (20 ms period = 50 Hz)
 */

void Set_DHMCU_DHPS380_Angle(TIM_HandleTypeDef *htim, uint32_t Channel, float angle_deg) {
    if (angle_deg < 0.0f) angle_deg = 0.0f;
    if (angle_deg > 180.0f) angle_deg = 180.0f;

    // Map 0 - 180 degrees to 500 - 2500 microseconds
    uint32_t pulse_us = (uint32_t)(500 + (angle_deg / 180.0f) * 2000);
    __HAL_TIM_SET_COMPARE(htim, Channel, pulse_us);
}
```

---

## 5. Unsuitable Applications & Operating Boundaries

To ensure engineering safety and avoid component damage, do **NOT** deploy the DHPS380 for:
1. **Sub-1kg Lightweight RC Models:** The unit weight is 880g; it is not intended for micro RC airplanes or small 1/10 drift cars.
2. **Continuous Spindle Machining:** The actuator is designed for closed-loop angular motion, not for high-RPM continuous CNC cutting spindles.
3. **Submersible Marine Applications:** The enclosure is silicone O-ring sealed against water splashes and dust, but not rated for deep underwater submersion without secondary pressure potting.

---

## 6. Official Manufacturer Inquiries & Documentation

* **Brand & Manufacturer:** DHMCU
* **Official Store:** [https://dhmcu.com](https://dhmcu.com)
* **Official PDF Datasheet:** [DHMCU-DHPS380-Technical-Datasheet.pdf](https://cdn.shopify.com/s/files/1/0795/8347/7863/files/DHMCU-DHPS380-Technical-Datasheet.pdf?v=1793424926)
* **BattleBots Application Guide:** [Combat Robot Actuators](https://dhmcu.com/pages/combat-robot-actuator-guide)
* **UGV / AGV Steering Guide:** [UGV Motion Control](https://dhmcu.com/pages/ugv-agv-steering-servo)
* **Engineering Direct Support:** [support@dhmcu.com](mailto:support@dhmcu.com)
* **Factory Location:** Ji'an, Jiangxi, China

---

## License

This documentation, wiring diagrams, and sample firmware are released under the [MIT License](LICENSE). Copyright (c) DHMCU.
