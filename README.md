# ESP32-WROOM-32E + CLion + PlatformIO Setup

## Overview

This guide explains how to set up an **ESP32-WROOM-32E** development environment using:

* **CLion**
* **PlatformIO**
* **Arduino framework**
* **Silicon Labs CP210x USB-to-UART driver**
* An ESP32 development board with a **CP2102** USB-to-UART chip

The goal is to get the development environment configured and ready to program the ESP32. The example application is intentionally not included.

---

## 1. Prerequisites

Before starting, make sure you have:

* CLion installed
* Python installed
* An ESP32-WROOM-32E development board
* A USB **data** cable
* Internet access
* PlatformIO support for CLion

---

## 2. Install PlatformIO Core

PlatformIO Core is required for CLion to build and upload PlatformIO projects.

Open **PowerShell** and run:

```powershell
python -m pip install -U platformio
```

Verify that PlatformIO was installed:

```powershell
python -m platformio --version
```

If `pio` is not recognized directly, locate the PlatformIO executable:

```powershell
Get-ChildItem "$env:APPDATA\Python\Python314" -Filter "pio.exe" -Recurse
```

The executable will typically be located somewhere similar to:

```text
C:\Users\<username>\AppData\Roaming\Python\Python314\Scripts\pio.exe
```

Keep this path available for the CLion PlatformIO configuration.

> The Python version in the path may be different depending on the version installed on your computer.

---

## 3. Configure PlatformIO in CLion

Open CLion and make sure the **PlatformIO** plugin is installed.

If CLion asks for the **PlatformIO executable path**, select the `pio.exe` executable found during the previous step.

After configuring it, CLion should be able to create and manage PlatformIO projects.

---

## 4. Install the CP210x USB Driver

Many ESP32 development boards use a **CP2102** USB-to-UART bridge.

Without the correct driver, Windows may recognize the board but fail to create a usable COM port.

Download the **CP210x Universal Windows Driver** from Silicon Labs:

[Silicon Labs CP210x USB-to-UART Drivers](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?utm_source=chatgpt.com)

Extract the downloaded ZIP file.

You should see files including:

```text
silabser.inf
arm
arm64
x64
x86
```

---

## 5. Install the CP210x Driver

Open PowerShell in the extracted driver directory.

Install the driver with:

```powershell
pnputil /add-driver .\silabser.inf /install
```

After installation, unplug and reconnect the ESP32.

---

## 6. Verify the ESP32 in Device Manager

Open **Device Manager**.

Look under:

**Ports (COM & LPT)**

The board should appear as something similar to:

```text
Silicon Labs CP210x USB to UART Bridge (COM3)
```

The COM number may be different on your computer.

For example:

* COM3
* COM4
* COM5
* COM6

Record the COM port assigned to the ESP32.

### If the CP2102 still shows an error

Run:

```powershell
Get-PnpDevice -PresentOnly | Where-Object { $_.FriendlyName -match "USB|Serial|CH340|CP210|UART" } | Format-Table Status, FriendlyName
```

A properly installed device should no longer show an `Error` status.

---

## 7. Create a PlatformIO Project

In CLion, create a new **PlatformIO Project**.

Use the following settings:

| Setting          | Value                            |
| ---------------- | -------------------------------- |
| Board            | Espressif ESP32 Dev Module       |
| Board ID         | `esp32dev`                       |
| Framework        | Arduino                          |
| Project location | Your preferred project directory |

Create the project.

PlatformIO will automatically create the required project structure.

---

## 8. Understand the Project Structure

A new PlatformIO project will generally look similar to:

```text
project/
├── include/
├── lib/
├── src/
│   └── main.cpp
├── test/
└── platformio.ini
```

### `src/`

Contains the main application source files.

For an Arduino-based ESP32 project, the primary file is normally:

```text
src/main.cpp
```

### `platformio.ini`

Contains the project's PlatformIO configuration.

This is where the board, framework, upload settings, and other project options are configured.

### `lib/`

Used for project-specific libraries.

### `include/`

Used for project header files.

### `test/`

Used for PlatformIO tests.

---

## 9. Configure the Upload Port

Open:

```text
platformio.ini
```

The project should be configured for:

* ESP32 Dev Module
* Arduino framework
* The COM port assigned to your ESP32

The upload port should correspond to the COM port shown in Device Manager.

For example, if Windows assigns the board COM3, configure the project to use **COM3**.

Do not assume that your computer will use the same COM number as another computer.

---

## 10. Build the Project

Before attempting to upload anything, build the project using the **PlatformIO Build** command in CLion.

A successful build confirms that:

* PlatformIO is installed correctly
* The ESP32 platform is installed
* The Arduino framework is available
* The project configuration is valid
* The source code compiles

The build process may download additional PlatformIO packages the first time it runs.

---

## 11. Upload to the ESP32

Once the project builds successfully:

1. Connect the ESP32 to the computer.
2. Confirm the correct COM port in Device Manager.
3. Make sure the COM port is configured in `platformio.ini`.
4. Use PlatformIO's **Upload** command in CLion.

PlatformIO will compile the project and transfer the resulting firmware to the ESP32.

---

## 12. If Upload Gets Stuck Connecting

Some ESP32 boards require the **BOOT** button to be held during the beginning of the upload process.

If PlatformIO repeatedly displays something similar to:

```text
Connecting........
```

try:

1. Start the upload.
2. Hold the **BOOT** button on the ESP32.
3. Continue holding it while the upload begins.
4. Release it once the upload process starts.

The exact behavior depends on the ESP32 development board.

---

## 13. Common Problems

### `pio` is not recognized

PlatformIO may be installed but its executable may not be on the Windows PATH.

Use the Python installation method and locate `pio.exe` manually, then provide that executable to CLion.

---

### `Please specify upload_port`

PlatformIO cannot determine which serial port to use.

Check Device Manager for the CP210x COM port and configure that port in `platformio.ini`.

---

### CP2102 appears with an error

The USB-to-UART driver is likely missing or incorrectly installed.

Install the Silicon Labs CP210x Universal Windows Driver and reconnect the board.

---

### No COM port appears

Check:

* The USB cable supports data transfer.
* The ESP32 is connected.
* The CP210x driver is installed.
* The board appears in Device Manager.
* The board is not connected through a faulty USB hub or cable.

---

### Upload reports that the port is busy

Close anything currently using the ESP32's serial port, such as:

* Serial monitors
* Terminal programs
* Other development tools

Then try the upload again.

---

## 14. Recommended Workflow

Once the environment is configured, the normal development workflow is:

```text
Connect ESP32
      ↓
Open PlatformIO project
      ↓
Edit source files
      ↓
Build
      ↓
Fix any compilation errors
      ↓
Upload
      ↓
Test on ESP32
      ↓
Repeat
```

---

## 15. Final Checklist

Before programming the ESP32, verify:

* [ ] CLion is installed
* [ ] PlatformIO plugin is installed
* [ ] PlatformIO Core is installed
* [ ] CLion knows the location of `pio.exe`
* [ ] CP210x driver is installed
* [ ] ESP32 appears in Device Manager
* [ ] A COM port is assigned to the ESP32
* [ ] PlatformIO project uses `esp32dev`
* [ ] Arduino framework is selected
* [ ] Main source file is located under `src/`
* [ ] The correct upload port is configured
* [ ] The project builds successfully
* [ ] The ESP32 can be uploaded to successfully

After completing these steps, the ESP32 development environment is ready for application development.
