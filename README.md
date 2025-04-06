# OpenVirtualSteering-MotionIMU

The **Romans** knew it already: **`"Sterzare Necesse Est"`** (free after Plutarch), and it still holds true in the world of **virtual cycling**.

<details>
<summary>What it means</summary>
The original quote **Navigare Necesse Est** ("Navigation is Necessary") was humorously adapted here by replacing _Navigare_ with the Italian verb for [**Steering**](https://en.wiktionary.org/wiki/sterzare), aligning it with the project's theme.
</details>

This repo is part of the [**Open Virtual Steering**](https://github.com/yourhubrepo) initiative, a research-driven open-source project exploring **alternative input methods** for **steering in virtual cycling platforms**.

---

## 🧭 Motion-Based Steering with IMUs

This repository focuses on using an **MPU6050** sensor, a 3-axis accelerometer + 3-axis gyroscope, to detect the rider's **handlebar tilt or lean** and convert that motion into **steering commands**.

- 🌀 **MPU6050**: Low-cost, I²C-compatible motion sensor
- 🎮 **Use case**: Lean or tilt the handlebar to steer the avatar
- ⚙️ **Filtering** and **smoothing** algorithms provided

![Open Virtual Steering Diagram](/media/OpenVirtualSteering_Overview.png)

---

## 🔧 How it Works

This firmware configures a BLE server to:
1. Pair with a BLE client using a common **steering profile**
2. Continuously sample motion data from the MPU6050
3. Interpret movement into **steering angles**
4. Send steering data to the client at regular intervals

The MPU6050 data is processed using:
- Complementary filtering
- Motion thresholds
- Optional dead zones for stable centering

---

## ⚙️ Supported Microcontroller Platforms

- **ESP32** (NimBLE-Arduino)
- **nRF52840** (Adafruit Bluefruit)

Tested boards:
- [Adafruit Feather ESP32 V2](https://www.adafruit.com/product/5400)
- [Adafruit Feather nRF52840 Express](https://www.adafruit.com/product/4062)

---

## 🧪 Testing & Validation

Testing was conducted using [**nRF Connect for Mobile**](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-mobile), a BLE inspection app used to:
- Scan and connect to the BLE server
- Monitor characteristic notifications
- Confirm responsive and smooth steering behavior

The IMU’s angle mapping was adjusted to ensure **stable, realistic steering behavior** while avoiding overcorrection or jitter from natural hand motion.

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

## 📚 Related Repositories

- [`OpenVirtualSteering-DiscreteHIDs`](https://github.com/yourname/OpenVirtualSteering-DiscreteHIDs) – Buttons, Joysticks, Rotary Encoder  
- [`OpenVirtualSteering-VoiceControl`](https://github.com/yourname/OpenVirtualSteering-VoiceControl) – Voice input via ML on MEMS microphones

---

> 🧠 Contributions and discussions are welcome! Feel free to fork, experiment, or share your ideas for smarter indoor cycling interaction.
