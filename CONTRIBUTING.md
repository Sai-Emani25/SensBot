# 🤝 Contributing to SensBot

Welcome to **SensBot**! We're thrilled you're interested in contributing to this educational IoT rover project. Whether you're a student just starting your embedded systems journey, a hackathon participant at OSCG '26, or an experienced maker—you belong here.

> **Our Philosophy:** Every contribution matters. Fixing a typo in documentation is just as valuable as adding a new sensor module. We learn by doing, and we grow by sharing.

---

## 📋 Table of Contents

- [🛠️ Development Environment Setup](#️-development-environment-setup)
  - [Arduino IDE Installation](#arduino-ide-installation)
  - [ESP8266 Board Manager](#esp8266-board-manager)
  - [Required Libraries](#required-libraries)
  - [USB Drivers (CP210x / CH340)](#usb-drivers-cp210x--ch340)
  - [Linux Serial Port Permissions](#linux-serial-port-permissions)
- [⚠️ Hardware & Safety Guidelines](#️-hardware--safety-guidelines)
- [🔒 Secrets Management (CRITICAL)](#-secrets-management-critical)
- [🚀 Contribution Workflow](#-contribution-workflow)
- [📝 Coding Standards](#-coding-standards)
- [🐛 Reporting Issues](#-reporting-issues)
- [💬 Getting Help](#-getting-help)

---

## 🛠️ Development Environment Setup

### Arduino IDE Installation

1. Download the [Arduino IDE](https://www.arduino.cc/en/software) (version 1.8.x or 2.x)
2. Install it following the instructions for your operating system

### ESP8266 Board Manager

The ESP8266 (NodeMCU) isn't included in Arduino IDE by default. Here's how to add it:

1. Open Arduino IDE → **File** → **Preferences**
2. In the **"Additional Board Manager URLs"** field, paste:
   ```
   http://arduino.esp8266.com/stable/package_esp8266com_index.json
   ```
3. Click **OK**
4. Go to **Tools** → **Board** → **Boards Manager**
5. Search for **"ESP8266"** and install **"ESP8266 by ESP8266 Community"**
6. Select your board: **Tools** → **Board** → **ESP8266 Boards** → **NodeMCU 1.0 (ESP-12E Module)**

### Required Libraries

Install these libraries via **Sketch** → **Include Library** → **Manage Libraries**:

| Library | Author | Purpose |
|---------|--------|---------|
| `ESP8266WiFi` | ESP8266 Community | Built-in with ESP8266 board package |
| `Blynk` | Volodymyr Shymanskyy | Mobile app integration |
| `DHT sensor library` | Adafruit | Temperature & humidity sensor |
| `Adafruit Unified Sensor` | Adafruit | Required dependency for DHT library |

> **Tip:** Search for the exact library names in the Library Manager. The DHT library requires the Adafruit Unified Sensor library as a dependency—install both!

### USB Drivers (CP210x / CH340)

Your NodeMCU board uses **one of two USB-to-Serial chips**. If your board isn't detected, install the appropriate driver:

| Chip | How to Identify | Driver Download |
|------|----------------|-----------------|
| **CP210x** (Silicon Labs) | Usually marked "CP2102" on the board | [Silicon Labs CP210x Drivers](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers) |
| **CH340** | Usually marked "CH340G" on the board | [CH340 Drivers](http://www.wch-ic.com/downloads/CH341SER_EXE.html) |

**How to check if drivers are installed:**
- **Windows:** Device Manager → Ports (COM & LPT) → Look for "Silicon Labs CP210x" or "USB-SERIAL CH340"
- **macOS:** Terminal → `ls /dev/cu.*` → Look for `cu.SLAB_USBtoUART` or `cu.wchusbserial`
- **Linux:** Terminal → `ls /dev/ttyUSB*` → Should show `/dev/ttyUSB0`

### Linux Serial Port Permissions

On Linux (Ubuntu, Linux Mint, Debian, etc.), you may encounter a "Permission denied" error when uploading. Fix this by adding your user to the `dialout` group:

```bash
sudo usermod -a -G dialout $USER
```

> ⚠️ **Important:** You must **log out and log back in** (or reboot) for this change to take effect!

**Verify it worked:**
```bash
groups | grep dialout
```
---

## Project Structure 

``` 

SensBot/
│
├── Prototype_Rover.ino                 # Main ESP8266 firmware
├── README.md                           # Project overview and setup guide
├── CONTRIBUTING.md                     # Contribution guidelines
├── LICENSE                             # Project license
│
├── Schematic_Sensbot_2025-03-16.pdf    # Hardware wiring schematic
├── PCB_PCB_Sensbot_2025-03-16.pdf      # PCB design files
├── Gerber_PCB_Sensbot_2025-03-16.pdf   # Manufacturing Gerber files
│ 
├── SensBot v3.f3d                      # Fusion 360 3D model
├── SensBot v3.obj                      # Exported 3D model
├── SensBot Wheel.dwg                   # Mechanical drawing
├── SensBot Wheel.pdf                   # Wheel PDF drawing
│
├── SensBot Drafting.pdf                # Mechanical drafting documentation
├── SensBot explode view.gif            # Exploded assembly animation
├── SensBot explode view.avi            # Exploded assembly video
│
├── IMG_.jpg                            # Hardware images
└── Screenshot_.png                     # UI / testing screenshots

```
---

### 🔎 Key Notes for Contributors

- All firmware changes should be made inside `Prototype_Rover.ino`
- PLease do not modify PCB or Gerber files unless working on hardware revision
- 3D model updates should maintain version naming (e.g., v4, v5)
- The large media files should be optimized before committing
- Keep file naming consistent with existing structure

---

---

## 🛠️Troubleshooting 

**This section is based on the full SensBot System and common issues related to the SensBot rover hardware, firmware, and cloud connectivity.**

### 1️⃣ ESP8266 Boots But Does Not Connect to Wi-Fi

Root Causes:
- Incorrect credentials
- 5GHz network
- Power instability
- Blocked firewall

Steps:

- Verify SSID and password inside `Prototype_Rover.ino`
- Ensure router supports 2.4GHz
- Open Serial Monitor and check connection logs
- Ensure Wi-Fi initialization occurs before Blynk initialization
- Confirm adequate power supply (minimum 5V regulated input)

If Wi-Fi fails repeatedly:
- Reduce distance from router
- Check for hidden SSID configuration
- Restart router

---

### 2️⃣ Wi-Fi Connects But Blynk Does Not Connect

Root Causes:
- Invalid Auth Token
- Template mismatch
- Blynk.begin() order incorrect
- Internet connectivity issue

Steps:

- Verify Auth Token in firmware
- Confirm Template ID matches Blynk dashboard
- Ensure Wi-Fi connection is established before calling Blynk.begin()
- Check internet availability
- Print Blynk connection status in Serial Monitor

If still failing:
- Regenerate Auth Token
- Re-upload firmware

---

### 3️⃣ Temperature / Humidity Readings Return NaN or Zero

Root Causes:
- Sensor initialization failure
- Incorrect GPIO assignment
- Voltage mismatch
- Library conflict

Steps:

- Confirm correct DHT pin definition in `Prototype_Rover.ino`
- Verify sensor library installation
- Ensure sensor powered with compatible voltage
- Add delay after sensor initialization if required
- Print raw sensor values to Serial Monitor

If values fluctuate heavily:
- Check wiring length
- Ensure stable 3.3V supply

---

### 4️⃣ Gas Sensor Values Are Unstable or Always Zero

Root Causes:
- Insufficient warm-up time
- Incorrect analog pin configuration
- Improper scaling logic
- Noisy power line

Steps:

- Allow gas sensor warm-up (initial stabilization time)
- Verify analog input pin configuration
- Check scaling formula in firmware
- Ensure clean ground connection
- Add filtering logic if needed

If readings spike randomly:
- Check for voltage drop
- Separate motor power from sensor power

---

### 5️⃣ Obstacle Detection Not Working

Root Causes:
- Incorrect trigger/echo pin assignment
- Signal voltage mismatch
- Faulty sensor

Steps:

- Confirm GPIO pin mapping in firmware
- Verify wiring against schematic
- Check sensor orientation
- Print measured distance in Serial Monitor
- Ensure correct delay timing between trigger pulses

If always detecting obstacle:
- Check echo pin floating
- Add pull-down resistor if necessary

---

### 6️⃣ Motors Not Moving

Root Causes:
- Motor driver not powered
- No common ground
- Incorrect GPIO control pins
- Insufficient current supply

Steps:

- Confirm motor driver supply voltage
- Ensure common ground between ESP8266 and motor driver
- Verify correct control pins in firmware
- Test motor driver independently
- Check enable pins (if used)

If motors jitter:
- Check PWM logic
- Ensure adequate battery current capacity

---

### 7️⃣ Motors Cause ESP8266 Reset

Root Causes:
- Voltage drop during motor start
- Shared unstable power rail

Steps:

- Use separate power supply for motors
- Add decoupling capacitors
- Ensure regulated 5V input to ESP8266
- Monitor Serial output for reset reason

ESP8266 is sensitive to voltage dips.

---

### 8️⃣ Firmware Upload Successful But Rover Is Inactive

Root Causes:
- setup() not completing
- loop() blocked
- Sensor initialization failure
- Wi-Fi blocking loop

Steps:

- Add Serial prints in setup()
- Confirm all initialization functions execute
- Remove long blocking delays
- Ensure Blynk.run() exists inside loop()
- Verify no infinite blocking logic

---

### 9️⃣ Custom PCB Version Malfunction

Root Causes:
- It could be Wrong pin routing
- Short circuits
- Incorrect component orientation

Steps:

- Compare PCB layout with schematic
- Verify ESP8266 pin mapping
- Inspect solder joints
- Check for short circuits between adjacent pads
- Test continuity before powering

---

### 🔟 Random Behavior or Intermittent Failure

Root Causes:
- Floating GPIO pins
- Power noise
- Overheating regulator

Steps:

- We would suggest you to avoid using restricted ESP8266 boot pins incorrectly
- Please Ensure proper pull-up/pull-down configuration
- Make sure you Inspect regulator temperature
- Also  Improve grounding layout
- Reduce wire length where possible

---

**If issue persists, open a GitHub issue including:**

- Board version
- Power supply specifications
- Serial Monitor logs
- Description of exact failure point

---

## ⚠️ Hardware & Safety Guidelines

Working with electronics requires caution. Please follow these safety practices:

### 🔌 Before Making Any Wiring Changes

> **⚡ ALWAYS disconnect power (USB cable AND battery) before changing any wires or connections!**

Failing to do so can:
- Damage your ESP8266 board permanently
- Short-circuit components
- Create fire hazards with Li-Po batteries

### 📐 Wiring Reference

All wiring information is documented in the schematic file included in this repository:

📄 **[`Schematic_Sensbot_2025-03-16.pdf`](./Schematic_Sensbot_2025-03-16.pdf)**

**Please review the schematic carefully before:**
- Connecting sensors (DHT11/DHT22, MQ-2)
- Wiring motor driver connections
- Adding new components

### 🔋 Battery Safety

If using Li-Po/Li-Ion batteries:
- Never short-circuit the battery terminals
- Use batteries with built-in protection circuits
- Never leave charging batteries unattended
- Dispose of damaged batteries properly

### 📌 Pin Reference Quick Guide

| Component | Pin(s) | Notes |
|-----------|--------|-------|
| Motor Driver IN1 | D1 | Left motor |
| Motor Driver IN2 | D2 | Left motor |
| Motor Driver IN3 | D3 | Right motor |
| Motor Driver IN4 | D4 | Right motor |
| DHT11/DHT22 | D5 (GPIO 14) | Temperature & humidity |
| MQ-2 Gas Sensor | A0 | Analog input |
| Proximity Sensor | D6 | Digital input |
| Status LED | D7 | Optional indicator |

---

## 🔒 Secrets Management (CRITICAL)

> **🚨 WARNING: This section is about protecting YOUR passwords. Read it carefully.**

The codebase requires you to enter your personal Wi-Fi and Blynk credentials directly into `Prototype_Rover.ino`. This is necessary to run SensBot, but it creates a serious security risk if you're not careful.

---

### Step 1: Enter Your Credentials

Open `Prototype_Rover.ino` and locate these lines near the top of the file:

```cpp
char auth[] = "YOUR_BLYNK_AUTH_TOKEN";  // Enter your Blynk auth token
char ssid[] = "YOUR_WIFI_NAME";          // Enter your Wi-Fi SSID
char pass[] = "YOUR_WIFI_PASSWORD";      // Enter your Wi-Fi password
```

Replace the placeholder values with your actual credentials.

You'll also need to update the Blynk template defines:

```cpp
#define BLYNK_TEMPLATE_ID "YOUR_TEMPLATE_ID"
#define BLYNK_DEVICE_NAME "YOUR_DEVICE_NAME"
#define BLYNK_AUTH_TOKEN  "YOUR_AUTH_TOKEN"
```

---

### Step 2: STOP — Do NOT Commit This File

> **⛔ CRITICAL: After adding your real credentials, you must NEVER commit `Prototype_Rover.ino` to Git.**

If you push your credentials to GitHub:
- **Your Wi-Fi password becomes public** — anyone can see it
- **Your Blynk token is compromised** — others can control your device
- **Bots actively scan GitHub** for leaked credentials

---

### Step 3: Tell Git to Ignore Your Local Changes

Since `Prototype_Rover.ino` is already tracked by Git, adding it to `.gitignore` won't help. Instead, use this command to tell Git to **ignore your local modifications**:

```bash
git update-index --assume-unchanged Prototype_Rover.ino
```

**What this does:**
- Git will stop tracking changes you make to this file
- Your credentials stay local and won't appear in `git status` or `git diff`
- You won't accidentally commit your passwords

**To verify it worked:**
```bash
git status
```
The file should no longer appear as modified, even after you've added your credentials.

---

### Reverting (If You Need to Commit Other Changes)

If you later need to commit **legitimate code changes** (not credentials) to `Prototype_Rover.ino`:

1. **Remove your credentials** from the file first (replace with placeholders)
2. **Re-enable tracking:**
   ```bash
   git update-index --no-assume-unchanged Prototype_Rover.ino
   ```
3. Make your code changes and commit
4. Re-add your credentials and run `--assume-unchanged` again

---

### 🔐 Security Checklist Before Every PR

- [ ] I have **NOT** added my real Wi-Fi SSID/password to my commits
- [ ] I have **NOT** added my real Blynk auth token to my commits
- [ ] I have run `git diff` and verified no credentials appear
- [ ] I have run `git update-index --assume-unchanged Prototype_Rover.ino`

> **If you accidentally commit credentials:** Immediately [rotate your Blynk token](https://docs.blynk.io/) and change your Wi-Fi password. Consider the old credentials compromised.

---

## 🚀 Contribution Workflow

### Getting Started

1. **Fork the repository** on GitHub
2. **Clone your fork:**
   ```bash
   git clone https://github.com/YOUR-USERNAME/SensBot.git
   cd SensBot
   ```
3. **Create a feature branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```

### Making Changes

1. Make your changes in the feature branch
2. **Test your code on actual hardware** if possible
3. Ensure your code compiles without errors:
   - Arduino IDE: **Sketch** → **Verify/Compile** (Ctrl+R / Cmd+R)

### Submitting a Pull Request

1. **Commit your changes:**
   ```bash
   git add .
   git commit -m "feat: Add ultrasonic sensor support"
   ```
   
2. **Push to your fork:**
   ```bash
   git push origin feature/your-feature-name
   ```

3. **Open a Pull Request** on GitHub with:
   - A clear title describing the change
   - Description of what the PR does and why
   - Screenshots/videos if it's a visual change
   - Hardware tested on (if applicable)

### Commit Message Guidelines

We follow a simple convention:

| Prefix | Use for |
|--------|---------|
| `feat:` | New features |
| `fix:` | Bug fixes |
| `docs:` | Documentation changes |
| `refactor:` | Code improvements without feature changes |
| `test:` | Adding or updating tests |
| `chore:` | Maintenance tasks |

**Examples:**
```
feat: Add OLED display support for sensor readings
fix: Correct motor direction for backward movement
docs: Add wiring diagram for ultrasonic sensor
```

---

## 📝 Coding Standards

To keep the codebase clean and maintainable:

### General Guidelines

- **Comment your code:** Especially for hardware-specific logic
- **Use meaningful variable names:** `motorLeftSpeed` not `mls`
- **Keep functions small:** One function, one responsibility
- **Test before committing:** Compile **and** test on hardware when possible

### Arduino/C++ Specifics

```cpp
// ✅ Good: Clear pin definitions with comments
#define MOTOR_LEFT_A  D1  // Left motor forward
#define MOTOR_LEFT_B  D2  // Left motor backward

// ❌ Avoid: Magic numbers without context
digitalWrite(4, HIGH);

// ✅ Good: Use constants
digitalWrite(MOTOR_LEFT_A, HIGH);
```

### File Organization

- Keep library includes at the top
- Define pins and constants after includes
- Global variables after constants
- Setup and loop functions
- Helper functions at the bottom

---

## 🐛 Reporting Issues

Found a bug or have a suggestion? [Open an issue](../../issues/new) with:

### For Bugs:
- **Description:** What happened?
- **Expected behavior:** What should have happened?
- **Steps to reproduce:** How can we recreate this?
- **Hardware:** Which board/sensors are you using?
- **Environment:** OS, Arduino IDE version, library versions

### For Feature Requests:
- **Description:** What would you like to see?
- **Use case:** How would this be used?
- **Alternatives:** Have you considered other approaches?

---

## 💬 Getting Help

Stuck? We're here to help!

- **🐛 GitHub Issues:** For bugs and feature requests
- **💡 GitHub Discussions:** For questions and ideas
- **📚 Arduino Forums:** For general ESP8266/Arduino questions

---

## 🎉 Thank You!

Every contribution, no matter how small, helps make SensBot better for students and makers worldwide. We appreciate you taking the time to contribute!

**Happy tinkering! 🤖**

---

<div align="center">

*Made with ❤️ by the SensBot community*

</div>
