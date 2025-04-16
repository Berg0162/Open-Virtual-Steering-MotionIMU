# OpenVirtualSteering-MotionIMU

The **Romans** knew it already: _"Sterzare Necesse Est!"_ (free after Plutarch), and it still holds true in the world of **virtual cycling**.

<details>
<summary>What it means</summary>
  
The original quote **Navigare Necesse Est** ("To Sail is Necessary") was humorously adapted here by replacing _Navigare_ with the Italian verb for [**steering**](https://en.wiktionary.org/wiki/sterzare), aligning it with the project's theme.
</details>

This repo is part of the **Open Virtual Steering** initiative, a research-driven open-source project exploring **alternative input methods** for **steering in virtual cycling platforms**.<br>

## 🧭 Motion-Based Steering with _Inertial Measurement Unit_ (IMU)

This repository focuses on using an **MPU-6050 IMU** sensor, a 3-axis accelerometer + 3-axis gyroscope, to detect the rider's **handlebar turning and/or bike leaning** and convert that motion into **steering commands**. [See Wikipedia for Natural Bike Dynamics](https://en.wikipedia.org/wiki/Bicycle_and_motorcycle_dynamics).
- 🌀 **MPU-6050**: Low-cost, I²C-compatible motion sensor
- 🎮 **Use case**: _Shift your weight and lean the bike_ and/or turn the handlebar, to steer the avatar
- ⚙️ **Filtering** and **smoothing** algorithms provided

![Open Virtual Steering Diagram](/media/OpenVirtualSteering_Overview.png)

## 📚 Related Repositories
- [`BLE-Steering-Server`](https://github.com/Berg0162/BLE-Steering-Server) - Critical Shared Backend (Arduino library)
- [`OpenVirtualSteering-DiscreteHIDs`](https://github.com/Berg0162/OpenVirtualSteering-DiscreteHIDs) – Buttons, Joysticks, Rotary Encoder  
- [`OpenVirtualSteering-VoiceControl`](https://github.com/Berg0162/OpenVirtualSteering-VoiceControl) – Voice input via ML on MEMS microphones

## 🔧 How it Works
This firmware implements a **BLESteeringServer** using a standardized protocol observed in commercial steering devices.
It configures a BLE server to:
1. Pair with a BLE client application using a common **steering profile**
2. Continuously sample motion data from the MPU6050
3. Interpret movement into **steering angles**
4. Send steering data to the client at regular intervals

### 🌀 MPU-6050
The MPU-6050 is a 6-axis (combines 3-axis Gyroscope plus 3-axis Accelerometer) motion tracking devices. Changes in motion, acceleration and rotation can be detected. It is commonly used in robotics, gaming controllers, and other electronic devices that require motion detection. Its high accuracy and low cost make it very popular among the DIY community. <br>
<p align=left><img src="./media/QuadOrientation.png" width="250" height="250" alt="Quad Orientation" align="left"></p><br>
Put MPU-6050 flat on the table, assure that the face with label is upward and a dot on this surface is on the top left corner. Then the upright direction upward is the z-axis of the chip. The direction from left to right is regarded as the X-axis. Accordingly the direction from back to front is defined as the Y-axis. The MPU-6050’s onboard Digital Motion Processor (<b>DMP</b>) offloads processing that would normally have to take place on the microprocessor. It maintains an internal buffer that combines data from the gyro and accelerometer and computes orientation. The <b>DMP</b> also takes care of the applying the offsets.<br>
<br clear="left">

A comprehensive description of the MPU-6050 can be found on [MPU-6050 Explained](https://mjwhite8119.github.io/Robots/mpu6050).<br>
The <b>DMP</b> pre-processed MPU-6050 data is next handled in firmware code using:
- Complementary filtering
- Motion thresholds
- Optional dead zones for stable centering

## 🧱 Dependencies

+ [Arduino core for ESP32](https://github.com/espressif/arduino-esp32)
+ [NimBLE-Arduino version 2.x](https://github.com/h2zero/NimBLE-Arduino)
+ [BLESteeringServer](https://github.com/Berg0162/BLE-Steering-Server)
+ [MPU6050_light](https://github.com/rfetick/MPU6050_light)
+ **Supported MCU's** with **NimBLE-Arduino**
    - Espressif: ESP32, ESP32C3, ESP32S3
    - Nordic: nRF51, nRF52 series (**Requires** using [n-able arduino core](https://github.com/h2zero/n-able-Arduino))

## 🧪 Testing & Validation

Testing was conducted using [**nRF Connect for Mobile**](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-mobile), a BLE inspection app used to:
- Scan and connect to the BLE server
- Monitor characteristic notifications
- Confirm responsive and smooth steering behavior

The IMU’s angle mapping was adjusted to ensure **stable, realistic steering behavior** while avoiding overcorrection or jitter from natural bike motion.

---

## ⚠️ Disclaimer

<details>
<summary>💡 <b>Research & Independence</b></summary>
This project is <b>not affiliated with, endorsed by, or associated with any commercial virtual cycling platform or steering device manufacturer</b>. It is a <b>research and interoperability</b> initiative designed to explore <b>alternative human interface methods</b> in the context of indoor cycling. All development is conducted independently for <b>educational and experimental purposes</b>.
</details>

<details>
<summary>✅ <b>Compliance & Responsibility</b></summary>
This repository does <b>not include or promote any circumvention of technological protection measures</b>, reverse engineering of proprietary software, or unauthorized access to restricted systems. Users are <b>solely responsible</b> for ensuring that their use of this code complies with <b>local laws, software licenses, and platform terms of service</b>.
</details>

<details>
<summary>🔍 <b>Copyright & Contact</b></summary>
If you are a <b>rights holder</b> and believe this project includes content that <b>violates your intellectual property rights</b>, please <b>open an issue</b> in this repository. We are committed to responding promptly and respectfully to legitimate concerns.
</details>

---
## 🧠 Motion-Based Steering Processing Pipeline

This device is enabling natural steering movements with <b>your handlebars</b> and <b>your body position</b>! Shifting body position (a.k.a. **leaning**) is only possible when your indoor bike setup allows for, **with a rocker plate**! The present code is optimized for natural steering even during the **heavy wobbling conditions** of a rocker plate! `Wobbling -> Noise -> Spurious Sensor Drift!`<br> 
The code can detect the difference between **leaning and rocking the bike**. It was a challenge to separate actual steering from natural cycling movements. Afterall, the body movement to keep the pedals turning, induces distinctive bike movements! With a **rocker-plate-bike-setup**, steering is as natural as possible. Leaning to the right or left is detected in combination with turning the handlebars and amplify the effect on the avatar!<br>

The `getMPU6050State()` function processes motion data from the MPU6050 sensor to produce a **steering value** based on the rider’s **handlebar movements (Yaw)** and **leaning (Roll)**. Here’s a high-level breakdown of the processing steps:

1. **Data Acquisition**  
   - The sensor is sampled in a short loop (≈44 ms total).  
   - Raw angle readings are taken from the **Z-axis (Yaw)** and **X-axis (Roll)**.

2. **Noise Reduction**  
   - Each reading is processed through a **Kalman filter** and an **Exponential Moving Average (EMA)** filter.  
   - This reduces measurement noise and improves stability for steering detection.

3. **Statistical Filtering**  
   - The **standard deviation** between current and previous angles is calculated.  
   - This helps differentiate between real steering input and unwanted noise or spontaneous drift.

4. **Direction and Validity Check**  
   - Yaw and Roll data are compared to determine **coherent movement** (i.e., leaning and turning in the same direction).  
   - If both indicate a turn (left or right), the movement is considered intentional steering.

5. **Yaw Offset Update**  
   - If yaw deviation exceeds a defined threshold, the baseline yaw angle is updated (to account for drift or re-orientation).

6. **Rocking Detection**  
   - Excessive roll deviation is flagged as **rocking motion**, which disables steering output to avoid false input from out-of-saddle riding.

7. **Final Output**  
   - If the motion is valid and not considered rocking, a **combined yaw + roll steering value** is returned.  
   - This value is later encoded and sent to the BLE client as **steering control data**.

---
<img src="./media/MPU-6050 2-600x600w.jpg" width="200" height="200" alt="MPU6050" align="left">

The very low cost MPU6050 is connected to the board with I2C:

```C++
#include <MPU6050_light.h>
// Instantiate MPU6050 sensor
MPU6050 mpu(Wire);
```
<br clear="left">
A SSD1306 display is connected the same way:

```C++ 
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include "Adafruit_SSD1306_Icons.h"

// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define SCREEN_WIDTH 128              // OLED display width, in pixels
#define SCREEN_HEIGHT 64              // OLED display height, in pixels
#define OLED_RESET     -1             // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);
```

<details>
<summary>⚖️ <b>Legal Notice (EU Context)</b></summary>

This project is developed and published in accordance with **EU directives** that recognize the right to study, test, and develop software components for the purpose of achieving **interoperability** (e.g., Directive 2009/24/EC on the legal protection of computer programs, Article 6).  

No part of this project is intended to **infringe upon intellectual property rights** or violate technological protection measures. All content is shared in good faith under the belief that it falls within the bounds of **legitimate research, reverse engineering for interoperability, and fair use under EU law**.  

Users must ensure their own compliance with **national implementations of EU directives**, and are responsible for how they apply or modify this code.

</details>

> 🧠 Contributions and discussions are welcome! Feel free to fork, experiment, or share your ideas for smarter indoor cycling interaction.
