This repository contains the guideline to use On-Device Power Rails Monitor (ODPM) on Google Pixel phones. It explains how to configure and start Perfetto tracing via ADB, pull the resulting .perfetto-trace files from the device, and process them in Python notebooks to derive power and energy metrics from the recorded power rails data.

# Android ADB Initial Setup Guide (One-Time)

This guide explains how to install and configure **ADB (Android Debug Bridge)** on Windows.

---


## 1️⃣ Install Android Platform Tools

Download the latest **Android SDK Platform Tools** (which include `adb`) from the official Android developer website:

https://developer.android.com/studio/releases/platform-tools

Extract the ZIP file to a convenient location, for example:
```
C:\Android\platform-tools
```

---

## 2️⃣ Add ADB to Windows PATH

1. Open the **Windows Start Menu**
2. Search for **Environment Variables**
3. Click **Edit the system environment variables**
4. In the dialog, click **Environment Variables…**
5. Under **System variables**, select the `Path` variable
6. Click **Edit**
7. Click **New** and add:
```
C:\Android\platform-tools
```
8. Click **OK** on all dialogs to save changes.

---

## 3️⃣ Verify ADB Installation

Open **Command Prompt** or **PowerShell** and run:

```bash
adb version
```

If installed correctly, the adb version will be printed.
This confirms that the PATH is configured properly.

## 4️⃣ Connect Your Phone

1. Connect your phone via USB.
2. Enable **Developer Options** on the phone.
3. Enable **USB Debugging**.
4. Run:

```bash
adb devices
```

If prompted on the phone, accept the debugging permission.

You should see your device listed as:

device

This confirms that ADB is working correctly.

# Perfetto_trace_analyzer setup
These steps assume you are in the root directory of your repository (where config.pbtx and the Perfetto_traces/ folder live).

## 0. Push the Perfetto config file to the phone (one-time or when updated)
Perfetto needs a configuration file on the device to know what to record (e.g., power rails, CPU, etc.).
If you haven’t pushed it yet, or if you have changed config.pbtx, run:

```bash
adb push config.pbtx /
```
Details:

config.pbtx is your Perfetto text‑format config file.

The command copies it to the root directory (/) on the device so that later you can pipe it into perfetto from the shell.

You only need to repeat this when the config changes.

## 1. Record and pull a trace in one command
You can generate a Perfetto trace on the phone and pull it back to your PC in a single command.
From PowerShell, run:

```powershell
$fname = '100mbps_mimo_0.perfetto-trace';
adb shell "cat /data/local/tmp/config.pbtx | perfetto --txt -c - -o /data/misc/perfetto-traces/$fname";
adb pull "/data/misc/perfetto-traces/$fname" "./Perfetto_traces/."
```
Explanation of each part:

- **`$fname = '100mbps_mimo_0.perfetto-trace';`** - Defines the output trace filename (you can change this per experiment). The .perfetto-trace extension is used so tools recognize the format.

- **`adb shell "cat /data/local/tmp/config.pbtx | perfetto --txt -c - -o /data/misc/perfetto-traces/$fname"`** - Opens a shell on the device. `cat /data/local/tmp/config.pbtx` reads the Perfetto config text file from the device. The output is piped into perfetto with these options:
  - `--txt` tells Perfetto the config is in text format
  - `-c -` means "read the config from stdin" (the pipe)
  - `-o /data/misc/perfetto-traces/$fname` sets the trace output file path on the device
  - Perfetto runs for the duration specified in your config and then exits, writing the trace


- **`adb pull "/data/misc/perfetto-traces/$fname" "./Perfetto_traces/."`** - Copies the generated trace file from the device to your PC. The trace is saved into the local Perfetto_traces/ folder in your repository. That folder should be listed in .gitignore so traces are not committed to version control.

2. After the trace is pulled

Once the .perfetto-trace file is in Perfetto_traces/, you can:

- Open it in your Python notebooks for analysis (e.g., using Perfetto Trace Processor Python API).
- Optionally open it in the Perfetto UI (https://ui.perfetto.dev) by dragging and dropping the file, to visually inspect timelines and power rails before or after running notebooks.

