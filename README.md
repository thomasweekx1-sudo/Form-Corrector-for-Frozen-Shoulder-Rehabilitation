# Form-Corrector-for-Frozen-Shoulder-Rehabilitation

**Vik Mouling & Thomas Weekx — KU Leuven, B-KUL-T4lMD2 Haptic Interfaces Experience, 2026**

---

A wearable haptic armband that acts as a physical therapist's guiding hand during home rehabilitation for frozen shoulder (adhesive capsulitis). The device tracks the 3D movement of the arm in real time using an IMU and delivers intuitive directional vibration feedback to correct form errors before the patient even realizes they are cheating.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Supplies (Bill of Materials)](#supplies-bill-of-materials)
3. [Methods](#methods)
   - [Step 1 — System Architecture](#step-1--system-architecture)
   - [Step 2 — Hardware Assembly](#step-2--hardware-assembly)
   - [Step 3 — The Three Exercises & Cheat Detection](#step-3--the-three-exercises--cheat-detection)
   - [Step 4 — Software & Programming](#step-4--software--programming)
4. [Discussion](#discussion)
5. [Conclusion & Future Work](#conclusion--future-work)
6. [References](#references)

---

## Introduction

### The Medical Challenge

Frozen shoulder, clinically known as adhesive capsulitis, is a painful inflammatory condition of the glenohumeral joint that causes progressive stiffness and a dramatic reduction in range of motion. It affects roughly 2–5% of the general population, is more prevalent in women, and most commonly strikes people between the ages of 40 and 60 [1, 2]. The condition is self-limiting but stubbornly slow to resolve — full recovery can take anywhere from 18 to 36 months [3]. During that window, patients are prescribed daily stretching exercises to gradually reclaim their mobility. These exercises hurt. That is the whole point: the joint needs to be stretched past its comfortable range. The problem is what happens at home, without a therapist in the room.

### The Core Problem: Unsupervised Compensation

When a movement causes pain, the human body instinctively finds a way around it. In shoulder rehabilitation, this takes the form of compensatory movements: shrugging the shoulder toward the ear, swinging the arm forward out of the correct plane, internally rotating the humerus, or letting the elbow flare out from the ribcage. These are not conscious choices, they are reflexive strategies to avoid pain [4]. In a clinical setting, a physiotherapist spots and corrects these deviations immediately. At home, there is nobody watching.

Studies confirm this is a serious and widespread issue. Research on unsupervised home exercise programs for shoulder rehabilitation shows that adherence to prescribed exercise protocols is typically around 50%, and that quality of execution degrades even more dramatically than adherence [5]. A key study on unsupervised shoulder abduction exercises found that after just two weeks, the majority of participants were no longer performing the exercise with correct form, even when they had been carefully instructed [6]. A patient who diligently practices bad form every day is not recovering; they are reinforcing a compensation pattern that can cause secondary injuries and delay healing [7].

### Existing Solutions and Their Limitations

Researchers and clinicians have explored several approaches to extend therapist oversight into the home environment, each with meaningful trade-offs.

**Mirror therapy and visual feedback** use a patient's own reflection to increase body awareness during exercise. Studies in frozen shoulder populations show short-term improvements in pain and range of motion, and visual feedback slightly outperforms pure mirror therapy [8, 9]. However, these approaches have a fundamental limitation in the home context: they require the patient to simultaneously perform a painful exercise and visually monitor their own form. This cognitive dual-task is difficult, especially when focusing on a stiff and painful joint. The patient also needs to be positioned correctly relative to the mirror, which is impractical for all three of the relevant exercises.

**Smartphone camera-based pose estimation** apps use computer vision (MediaPipe, BlazePose, OpenPose) to track body keypoints and evaluate exercise quality [10]. These systems are accessible and promising, one study achieved 89% accuracy for range-of-motion classification and 98% for compensatory pattern detection in a controlled setting [11]. But real-world performance degrades substantially with occlusion, poor lighting, suboptimal camera angles, and the inherent difficulty of tracking subtle joint rotations in 2D video [12]. More importantly, camera-based systems deliver after-the-fact feedback (a score or color code after a set), not real-time correction during the movement. And they require the phone to be propped up at a specific angle and distance, adding setup friction that erodes compliance.

**Wearable IMU-based systems** with visual or audio biofeedback represent the closest prior work to our approach. Systems using accelerometers and gyroscopes placed on the upper arm can accurately track range of motion and flag deviations, and IMU sensors have become the dominant choice in wearable rehabilitation research precisely because they are compact, robust, and body-fixed [13]. However, most existing systems deliver feedback as a number on a screen or a post-exercise summary, modalities that require the patient to look away from what they are doing. One study found statistically significant differences in feedback accuracy across exercises using the same smartphone-based approach, highlighting that the feedback method needs to match the exercise geometry [10].

**Laser pointer and alignment guides** — a low-tech approach, sometimes used in clinics, involve the patient keeping a laser dot on a fixed target on a wall. This works for single-plane movements in a supervised setting but provides no feedback about rotational deviations, requires specific room setup, and offers no memory of what a "correct" path looks like for a specific patient.

### Why Haptic Feedback Is Different

Vibrotactile (haptic) feedback is a fundamentally different communication channel. It requires no visual attention, no after-the-fact reflection, and no cognitive overhead. When a motor on the inside of the arm buzzes, the patient instinctively moves away from it, the corrective response is reflexive, not deliberate. This closed-loop, body-centered feedback approach has been validated in rehabilitation contexts: a wearable haptic system for ergonomic posture training using IMU data showed that real-time vibration feedback successfully reduced adverse arm movements in participants performing repetitive tasks [14]. The key insight is that vibration applied at the exact location of the deviation provides spatial information that a screen-based alert simply cannot match.

The DRV2605L haptic driver used in our system is specifically engineered for this kind of application, providing a closed-loop actuator control system with a built-in library of over 100 tactile effects for high-quality, consistent vibration patterns [15].

### Our Approach

We built a smart armband strapped to the upper bicep. Four vibration motors are positioned around the arm, front, back, underside, and outside. A single MPU6050 IMU tracks the arm's 3D position and drift in real time. The device operates in two modes, toggled by a physical switch: a **Record Mode** where the correct path for a specific patient is recorded once (ideally guided by the therapist), and a **Compare Mode** where the device monitors each subsequent repetition and fires the appropriate motor the moment a deviation exceeds a threshold. Three key exercises are supported, selectable via a potentiometer: shoulder abduction (sideways lift), forward flexion, and external rotation. Each exercise targets a different compensation pattern with a different subset of motors.

---

## Supplies (Bill of Materials)

| Component | Specification | Quantity | Part Number / Link | Unit Cost (€) | Total Cost (€) |
|-----------|--------------|----------|-------------------|--------------|---------------|
| Microcontroller | Arduino Micro | 1 | | | |
| IMU | MPU-6050 6-axis accelerometer + gyroscope | 1 | | | |
| Haptic driver | Adafruit DRV2605L breakout | 4 | | | |
| Haptic actuator | Drake coin vibration motor (ERM) | 4 | | | |
| I²C multiplexer | TCA9548A 8-channel | 1 | | | |
| Potentiometer | 10 kΩ rotary | 1 | | | |
| Toggle switch | SPDT breadboard switch | 1 | | | |
| Breadboard | Full-size | 2 | | | |
| Jumper wires | Male-to-male, assorted | 1 set | | | |
| Armband | Velcro / fabric sports armband | 1 | | | |
| USB cable | Micro-USB (for programming) | 1 | | | |
| **Total** | | | | | |

---

## Methods

### Step 1 — System Architecture

The system centers on a single MPU6050 IMU strapped to the **lateral (outside) of the right upper bicep**. This placement is maintained for all three exercises. In this orientation:

- **Y-axis** points straight down toward the elbow.
- **X-axis** points straight backward behind the patient.
- **Z-axis** points straight outward to the right.

Four DRV2605L haptic drivers control four vibration motors placed around the arm:

| Motor | Position | Cue meaning |
|-------|----------|-------------|
| Motor 1 | Inside the arm (front of bicep / rib side) | Arm is drifting forwar: move backward |
| Motor 2 | Underside of the arm (tricep side) | Used in exercise-specific feedback |
| Motor 3 | Outside of the arm (back of bicep / lateral) | Arm is drifting backward: move forward |
| Motor 4 | Outside of the arm (tricep side) | Used in exercise-specific feedback |

The feedback principle is intentionally intuitive: **the motor that buzzes is on the side the arm is drifting toward**. The patient naturally moves away from the sensation, self-correcting without conscious thought.

The four DRV2605L drivers share the I²C bus via a TCA9548A multiplexer, since all four drivers have the same fixed I²C address. The multiplexer is also used to address the IMU on a separate channel. All components are mounted on two breadboards strapped to the arm alongside the motors.

A potentiometer selects between the three exercises. A toggle switch controls the Record/Compare state machine. Full wiring details are in [schematics](./visuals/schematics)
---

### Step 2 — Hardware Assembly

1. Wire the TCA9548A multiplexer to the Arduino Micro's I²C pins (SDA, SCL). Power it from the 3.3 V rail.
2. Connect the MPU6050 to multiplexer channel 5.
3. Connect the four DRV2605L breakout boards to multiplexer channels 1–4 respectively.
4. Wire each DRV2605L's motor output pins to its corresponding Drake ERM motor. Also connect the motor's enable pin to Arduino digital pins 10–13 for direct PWM control alongside the DRV2605L trigger.
5. Connect the potentiometer (exercise selector) to analog pin A0.
6. Connect the toggle switch to analog pin A3 with INPUT_PULLUP.
7. Secure both breadboards, the Arduino, and all four motors to the armband. Motors are positioned at the four anatomical locations described above. The IMU is placed flat against the lateral bicep.

See [schematics](./visuals/schematics) for the full wiring diagram.

---

### Step 3 — The Three Exercises & Cheat Detection

All three exercises use the same underlying **Record & Compare** logic. The therapist (or the patient on first use) performs one correct repetition in Record Mode. The device stores the arm's natural drift profile across the full range of motion. In Compare Mode, every subsequent repetition is measured against that stored profile. Only deviations that exceed the threshold, not the raw drift itself, trigger feedback. This means the device adapts to each individual patient's anatomy and natural movement pattern rather than enforcing a rigid, population-average "correct path."

---

#### Exercise A: Shoulder Abduction (Sideways Lift)

**Goal:** Lift the arm straight out to the side up to 90°, parallel to the wall, in the strict frontal plane.

**The cheat:** To avoid glenohumeral impingement pain, patients swing the arm forward (like a door opening) or internally rotate (twist) the humerus.Both movements move the top part of the upper arm bone farther away from the shoulder tip, reducing pain but also defeating the therapeutic purpose of the exercise.

**Tracking:**
- *Primary movement (elevation):* The X-gyro is integrated over time and fused with the accelerometer reading (atan2 of AccZ and AccY). This anchors the elevation measurement to gravity and prevents gyroscope drift from accumulating over the duration of a set [16].
- *Cheat detection:* Because the sensor rotates 90° into the air as the arm rises, a forward swing and an internal rotation each project onto different gyro axes depending on arm height. Adding the Y-gyro and Z-gyro integrals together creates a single robust "drift" value that catches both compensation types regardless of arm elevation.

**Feedback:**
- Drift forward → Motor 1 (inside) pulses.
- Drift backward → Motor 3 (outside) pulses.

---

#### Exercise B: External Shoulder Rotation

**Goal:** With the elbow pinned to the ribcage and bent at 90°, rotate the forearm outward (like opening a door), keeping the upper arm strictly vertical.

**The cheat:** The patient flares the elbow outward away from the ribcage to compensate for a stiff joint that cannot rotate cleanly.

**Tracking:**
- *Primary movement:* Pure Y-gyro integration (rotation around the humerus bone's long axis).
- *Cheat detection:* Strictly the X-gyro. If the elbow flares, the upper arm rotates around the backward-pointing X-axis. The Z-gyro is deliberately excluded to avoid false positives from minor positional shifts.
- Note: Only outward elbow flare is penalized. Keeping the elbow close to the body is correct form; there is no "too tight" compensation in this exercise.

**Feedback:**
- Elbow flares outward → Motor 3 (outside) pulses, cueing the patient to tuck the elbow back in.

---

#### Exercise C: Forward Flexion (Shrug Detector)

**Goal:** Lift the arm straight forward in front of the body.

**The cheat:** When pain limits true forward elevation, patients shrug the shoulder upward and allow the humerus to internally rotate. This results in the arm rotating inward to support the humerus, which results in a wrong exercise.

**Tracking:**
- *Primary movement:* atan2 applied to the accelerometer to calculate the exact angle of elevation from gravity, no gyro integration needed, giving a highly stable 0–90° reading.
- *Cheat detection:* Strictly the Y-gyro. A shrug causes the humerus to twist, which maps directly onto the Y-axis. Side-to-side sway is completely ignored to prevent false alarms from small lateral postural shifts.

**Feedback:**
- Shrug or internal twist detected → Motor 1 (inside) or Motor 3 (outside) pulses. (mostly inside)

---

### Step 4 — Software & Programming

The full Arduino code is in [`/code`](./code). Below are the key programming concepts.

#### 4.1 Record & Compare State Machine

A physical toggle switch flips the device between two modes (record and compare). This avoids hardcoding any "ideal" arm path.

**Record Mode:** The therapist guides the arm through one perfect repetition. At each degree of elevation (0–89°), the current combined drift value is saved into a 90-slot array `perfectPath[90]`. This captures the patient's individual anatomy their natural, microscopic drift at each height.

**Compare Mode:** On every subsequent repetition, the device reads the current elevation, looks up the stored reference drift for that exact elevation, and computes the error:

```
error = currentDrift - perfectPath[currentElevation]
```

Feedback fires only when `|error| > threshold`. This means the patient is not penalized for doing what they always do, only for doing something meaningfully different from the recorded correct path. The threshold was manually tweeked to find a sweetspot that only detects valid errors. Based on the progress of the patient, the threshold can be lowered.

#### 4.2 The Haptic Tunnel Array

Using the primary movement angle (0–89°) as the array index is the core insight of the system. Every degree of elevation acts as a lookup key for the exact allowed drift at that height. This creates what we call a **haptic tunnel**: a personalized 3D corridor around the correct movement path, rather than a rigid geometric plane.


#### 4.4 I²C Multiplexing

All four DRV2605L drivers share the same I²C address (0x5A). To address them individually, a TCA9548A multiplexer routes I²C traffic to one channel at a time via the `tcaselect(channel)` helper function. The IMU is on channel 5; the four haptic drivers are on channels 1–4.

#### 4.5 Exercise Selection via Potentiometer

A potentiometer on analog pin A0 maps its 0–1023 ADC range to three exercise modes. The selected exercise determines which axes are used for cheat detection and which motors are activated for feedback, while the core Record & Compare logic remains identical.

---

## Discussion

The device successfully demonstrated the core concept: by recording a personalized reference path and comparing each repetition against it, the system avoids the fragility of hardcoded thresholds and adapts to each patient's individual anatomy. In testing, the directional haptic feedback was intuitive, the motor buzz on the "wrong" side produced an immediate corrective response without instruction, confirming the body-centered feedback principle described in the literature [14].

Several limitations are worth noting. The current prototype uses breadboards and jumper wires, making it bulky and mechanically fragil, acceptable for a proof of concept but not for patient use. The threshold values were set empirically; clinical validation with actual frozen shoulder patients would be needed to determine optimal sensitivity for different stages of recovery or another potentiometer to adjust treshold is also an option. At early stages (high pain, large compensations), a more forgiving threshold may be appropriate; at later stages, tighter correction may accelerate recovery.

The complementary filter handles pitch and roll accurately, but yaw (rotation around the vertical axis) is tracked solely by gyroscope integration and will drift over longer sessions. For the targeted exercises, which typically take under 60 seconds per set, this is acceptable, but it is a fundamental limitation of IMU-only tracking without a magnetometer [17]. Adding a magnetometer would close this gap at the cost of sensitivity to nearby ferromagnetic objects. Another option is an extra push button that re-calibrates the IMU after every movement. 

The Record & Compare model also assumes that the recorded reference path is genuinely correct. If the therapist records the first repetition while the patient is already compensating slightly, the system will consider that compensation normal. A more robust future version could record multiple repetitions and average them, or flag if the recorded path itself deviates from expected biomechanical norms.

---

## Conclusion & Future Work

We built a working haptic armband prototype that provides real-time directional vibrotactile feedback to correct compensatory movements during frozen shoulder rehabilitation exercises at home. The system adapts to individual patient anatomy through a record-once, compare-always approach, supports three clinically relevant exercises, and delivers feedback through a intuitive channel, a buzz on the side the arm is drifting toward.

Key lessons for anyone building on this work:

- **Sensor placement geometry matters more than algorithm complexity.** Choosing which IMU axes encode which compensations requires careful biomechanical reasoning. The decision to add Y-gyro and Z-gyro for shoulder abduction cheat detection, for example, emerged from thinking about what happens to the sensor's frame as the arm rotates 90° into the air.
- **Personalized reference paths outperform fixed thresholds.** Frozen shoulder patients have highly variable anatomy and compensation patterns. A system that learns from each patient is more robust than one tuned to a population average.
- **Haptic feedback through vibrotactile actuators is intuitive.** No instruction was needed for users to understand and react to directional motor cues.

**Suggested future directions:**
- Replace breadboard with a custom PCB for a compact, wearable form factor.
- Add Bluetooth connectivity to log session data for remote physiotherapist review and a laptop does not have to hooked up to the armband.
- Integrate a magnetometer to eliminate yaw drift for longer sessions.
- Conduct a clinical pilot study to validate threshold values and measure rehabilitation outcomes.
- Extend to the left arm by mirroring the motor feedback logic.
- Explore progressive feedback intensity (gentle buzz for small errors, stronger for large ones) to reduce habituation.
- Because only motor 1 and 3 have been used in these exercises, finding other exercises or giving more precise feedback with the other motors could be benificial.
  

---

## References

[1] A. T. Tufekci et al., "The Effect of PNF and Shoulder Stabilization Exercises on Pain, Quality of Life and Functionality in Patients with Adhesive Capsulitis," *ClinicalTrials.gov*, NCT05797311, 2023.

[2] American Academy of Orthopaedic Surgeons, "Frozen Shoulder (Adhesive Capsulitis)," *OrthoInfo*, [Online]. Available: https://orthoinfo.aaos.org/en/diseases--conditions/frozen-shoulder. [Accessed: May 2026].

[3] J. E. Kuhn et al., "Arthroscopy and Manipulation vs a Home Therapy Program in the Treatment of Adhesive Capsulitis," *ClinicalTrials.gov*, NCT00160784.

[4] National Academy of Sports Medicine (NASM), "Preventing Shoulder and Rotator Cuff Injuries Through Corrective Exercise Programming," *NASM Blog*, [Online]. Available: https://blog.nasm.org. [Accessed: May 2026].

[5] A. Scott, P. H. Goga-Eppenstein et al., "Shoulder Physiotherapy Exercise Recognition: Machine Learning the Inertial Signals from a Smartwatch," *arXiv*, 1802.01489, 2018.

[6] C. T. Møller, N. E. Johannsen, M. K. Jensen, and L. L. Andersen, "The Majority Are Not Performing Home Exercises Correctly Two Weeks After Their Initial Instruction — An Assessor-Blinded Study," *PLOS ONE*, PMC4517955, 2015.

[7] M. X. Chua, Y. Okubo, S. Peng, T. N. Do, C. H. Wang, and L. Wu, "Analysis of Fatigue-Induced Compensatory Movements in Bicep Curls: Gaining Insights for the Deployment of Wearable Sensors," *arXiv*, 2402.11421, 2024.

[8] Ö. Hekim et al., "The Effect of Mirror Therapy in Patients with Frozen Shoulder," *Journal of Back and Musculoskeletal Rehabilitation*, PMC10078819, 2023.

[9] M. Karasuyama et al., "Effect of Mirror Therapy for Patients With Shoulder Pain: A Scoping Review," *Cureus*, PMC12675967, 2025.

[10] D. Pereira et al., "A Machine Learning App for Monitoring Physical Therapy at Home," *Sensors*, PMC10781250, 2024.

[11] Rehab-SPIA Study Group, "Construction of an AI System for the Automatic Supervision of Shoulder's Rehabilitation Exercises," *ClinicalTrials.gov*, NCT05026346.

[12] N. Ferreira et al., "A Real Time Action Scoring System for Movement Analysis and Feedback in Physical Therapy Using Human Pose Estimation," *PMC12749503*, 2025.

[13] A. Georgiou et al., "Interactive Wearable Systems for Upper Body Rehabilitation: A Systematic Review," *Journal of NeuroEngineering and Rehabilitation*, PMC5346195, 2017.

[14] P. Aaberg et al., "A Wearable Sensor System for Physical Ergonomics Interventions Using Haptic Feedback," *Sensors*, vol. 20, no. 21, 6010, 2020.

[15] Texas Instruments, "DRV2605L Low-Voltage Haptic Driver for ERM and LRA with Built-In Library and Smart-Loop Architecture," *Datasheet*, [Online]. Available: https://www.ti.com/product/DRV2605L. [Accessed: May 2026].

[16] P. Kulkarni and A. Bhatt, "Implementation of Complementary Filter on MPU6050," *World Scientific News*, vol. 199, pp. 218–234, 2025.

[17] R. Mahony, T. Hamel, and J.-M. Pflimlin, "Cascaded Complementary Filter Architecture for Sensor Fusion in Attitude Estimation," *Sensors*, PMC7998881, 2021.
