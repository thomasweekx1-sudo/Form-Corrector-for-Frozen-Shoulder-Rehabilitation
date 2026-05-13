# Form-Corrector-for-Frozen-Shoulder-Rehabilitation
# 3D Haptic Form Corrector for Adhesive Capsulitis Rehabilitation

**Authors:** Thomas Weekx & Vik Mouling  
**Course:** B-KUL-T41MD2 Haptic Interfaces Experience, KU Leuven  

---

## 1. Introduction

Adhesive capsulitis, clinically known as "frozen shoulder," is a pervasive musculoskeletal condition characterized by idiopathic, spontaneous pain and a progressive restriction of both active and passive glenohumeral motion. While pain management is the priority in early stages, the restoration of functional range of motion (ROM) through home exercise programs (HEP) is the gold standard for long-term recovery.

**The Medical Challenge:** The primary obstacle in successful rehabilitation is high irritability and pain, which leads patients to subconsciously adopt compensatory movements (CMs) or "cheating" mechanisms. These include scapular elevation (shrugging), trunk rotation, and lateral torso flexion. While CMs allow patients to complete a movement task in the short term, they reinforce maladaptive coordination patterns and can lead to secondary chronic injuries. 

**The Haptic Solution:** Existing visual feedback systems cause cognitive overload, and passive lasers offer no physical enforcement. Our device shifts the feedback to the cutaneous (tactile) channel. By using continuous 3D tracking, we create a localized "haptic tunnel" directly on the limb. If the patient deviates from the correct movement plane, the device provides immediate, intuitive directional vibration—mimicking the "guiding hands" of a physical therapist to correct their form before they even realize they are cheating.

---

## 2. Supplies (Bill of Materials)

To ensure full reproducibility, the following components were utilized:

| Component | Quantity | Specification / Part Number | Estimated Cost (USD) | Source |
| :--- | :--- | :--- | :--- | :--- |
| Microcontroller | 1 | Arduino Micro (ATmega32U4) | $22.80 | Official Arduino |
| Motion Sensor | 1 | MPU6050 (6-DOF Accel/Gyro) | $12.95 | Adafruit |
| Haptic Actuators | 4 | Coin Cell Vibration Motors (ERM) | $6.00 | Generic |
| Haptic Drivers | 4 | DRV2605L I2C Driver | $31.80 | Adafruit |
| I2C Multiplexer | 1 | TCA9548A 8-Channel | $6.95 | Adafruit |
| Potentiometer | 1 | 10k Ohm Linear | $1.50 | Generic |
| Toggle Switch | 1 | SPDT Physical Toggle Switch | $1.00 | Generic |
| Tactile Button | 1 | 6mm Momentary Push | $0.50 | Generic |
| Sports Armband | 1 | Neoprene Velcro Sleeve | $12.00 | Generic |
| **TOTAL** | | | **$95.50** | |

---

## 3. Methods

### Step 1: Hardware & System Architecture
The device utilizes a closed-loop control algorithm. The **MPU6050 sensor** is strapped to the lateral side (outside) of the right bicep with a strict orientation:
* **Y-Axis:** Points straight down toward the elbow.
* **X-Axis:** Points straight backward behind the patient.
* **Z-Axis:** Points straight outward to the right.

Four **DRV2605-driven coin cell motors** are strategically placed around the arm to provide intuitive, directional feedback (the patient instinctively moves away from the buzz):
* **Motor 1:** Inside the arm (Front of bicep / Rib side).
* **Motor 2:** Underside of the arm (Triceps).
* **Motor 3:** Outside of the arm (Back of bicep / Lateral).
* **Motor 4:** Outside of the arm (Triceps side).

### Step 2: Biomechanical Exercise Tracking
The potentiometer selects between three primary exercises. For each, the system tracks the **Primary Movement** (0° to 90°) and the **Error/Cheat** independently.

* **Exercise A: Sideways Lift (Shoulder Abduction)**
  * **Goal:** Lift arm straight out to the right side up to 90°.
  * **Cheat:** Swinging the arm forward or twisting to avoid impingement.
  * **Physics:** Primary Movement is tracked via Accelerometer + X-Gyro. Error is tracked by combining Y-Gyro and Z-Gyro. Because the sensor rotates 90° into the air, this combination creates a foolproof tracker for both twisting and forward swing.
  * **Feedback:** Drift Forward triggers Motor 1. Drift Backward triggers Motor 3.

* **Exercise B: External Shoulder Rotation**
  * **Goal:** Elbow bent at 90°, rotate forearm outward.
  * **Cheat:** The "chicken wing" flare (lifting the elbow away from the ribcage).
  * **Physics:** Primary Movement is tracked by integrating the Y-Gyro. Error (elbow flare) is strictly tracked by the X-Gyro. The Z-axis is ignored to prevent false alarms.
  * **Feedback:** If the elbow flares outward, Motor 3 pulses to instruct the patient to tuck it back in.

* **Exercise C: Forward Flexion (The Shrug Detector)**
  * **Goal:** Lift arm straight forward in front of the body.
  * **Cheat:** Shrugging the shoulder upward, which forces the humerus bone to internally rotate (twist).
  * **Physics:** Primary Movement tracked via Accelerometer (`atan2`) for exact elevation angle. Error is a hyper-precise 1D tracker monitoring strictly the Y-Gyro. Side-to-side sway is ignored; if the humerus twists, the patient is shrugging.
  * **Feedback:** Shrug detected triggers Motor 1 or 3 to break the compensation habit.

### Step 3: Software Engineering & Data Processing
The system solves complex biomechanical mapping using highly efficient C++ concepts:
1. **The "Record & Compare" State Machine:** A physical switch toggles modes. In *Record Mode*, the physical therapist guides the patient's arm perfectly, saving the patient's natural micro-deviations into an array. In *Compare Mode*, the patient's real-time error is subtracted from the recorded error. Feedback only triggers if the difference exceeds the `SENSITIVITY_THRESHOLD`.
2. **The Haptic Tunnel Array (`perfectPath[90]`):** The Primary Movement (elevation) acts as the array index. Every degree of elevation holds the allowed drift for that specific height. The `maxRecordedElevation` variable ensures downward motions do not overwrite the upward data.
3. **Sensor Fusion (Gravity Anchoring):** To combat gyroscope positional drift, the Accelerometer is used to continuously calculate the angle of gravity ($\arctan(Acc_x, Acc_y)$). This anchors the measurements to the real world, preventing the state machine from failing during long holds.

---

## 4. Discussion

The implementation of the localized coordinate frame and the `perfectPath[90]` array successfully creates a dynamic "haptic tunnel" tailored to individual patient anatomy. Unlike visual feedback systems that suffer from line-of-sight issues or 15° tracking errors, the direct cutaneous feedback loop successfully suppressed compensatory shrugging and elbow flaring in real-time. 

A known constraint is the long-term drift of yaw tracking, as the system relies on a 6-DOF IMU without a magnetometer. However, because standard physical therapy sets last approximately 60 seconds, this limitation is effectively mitigated by the "Tare/Zero" function initiated by the tactile button before every set.

---

## 5. Conclusion and Future Work

The 3D Haptic Form Corrector proves that localized, intuitive vibrotactile feedback can successfully enforce correct biomechanical form during unsupervised rehabilitation, bridging the gap between clinical supervision and home exercise programs. 

**Future Work:**
1. **Wireless Operation:** Transitioning the microcontroller to an ESP32 or Bluetooth-enabled Arduino Nano combined with a LiPo battery would untether the patient from the computer, vastly improving the range of motion and usability.
2. **9-DOF IMU Integration:** Upgrading from the MPU6050 to a 9-DOF sensor (like the BNO085) with a built-in magnetometer would completely eliminate yaw drift, removing the need for the patient to frequently "Tare" the device.
3. **Clinical Companion App:** Utilizing wireless capabilities to send compliance data and maximum elevation records to a mobile application, allowing physical therapists to monitor progress and adjust the `SENSITIVITY_THRESHOLD` remotely.

---

## 6. References

[1] S. Lin et al., "Manual therapy and exercise in the management of adhesive capsulitis," PMC, 2024.  
[2] M. Berjis et al., "Upper-limb Compensatory Movement Detection," IEEE Trans. on Systems, 2025.  
[3] P. Abdollahipour et al., "Laser-guided external focus in proprioceptive training," PMC, 2023.  
[4] B. C. Vigaru et al., "A Robotic Platform to Assess, Guide and Perturb Movements," IEEE T-NSRE, 2013.  
[5] S. Shull et al., "Wearable haptic devices for clinical applications," IEEE Access, 2022.  
[6] A. Jardón et al., "Haptic Tunneling for Motor Learning," MDPI, 2024.  
[7] G. Lagomarsino et al., "Haptic Guidance in Immersive Virtual Reality," PMC, 2026.  
[8] Y. Matsuoka et al., "Visual and haptic feedback in the control of force," Exp. Brain Research, 1999.  
[9] "Arduino Micro Rev3 Official Documentation," Arduino Store, 2024.  
[10] "DRV2605L Haptic Motor Driver Specification," Adafruit Industries, 2023.
