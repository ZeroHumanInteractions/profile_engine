# Profile Engine for NVIDIA GameStream

Profile Engine for [NVIDIA GameStream](https://www.nvidia.com/en-us/shield/support/shield-tv/gamestream/) with multi-monitor support and remote command execution.

## Table of Contents

- [Profile Engine for NVIDIA GameStream](#profile-engine-for-nvidia-gamestream)
  - [Table of Contents](#table-of-contents)
  - [About](#about)
  - [The Problem](#the-problem)
  - [The Solution](#the-solution)
  - [Features](#features)
  - [CLI Arguments](#cli-arguments)
    - [Host Commands](#host-commands)
    - [Remote CLI Commands](#remote-cli-commands)
  - [Configuration](#configuration)
    - [Overview](#overview)
    - [Logging](#logging)
    - [Monitoring](#monitoring)
    - [Remote CLI](#remote-cli)
    - [Explorer](#explorer)
    - [Keybinds](#keybinds)
    - [Playnite](#playnite)
    - [Profiles](#profiles)
  - [Downloads](#downloads)
  - [Installation](#installation)
  - [Limitations](#limitations)
  - [Feedback](#feedback)

## About

Profile Engine is a freely distributed software, with a goal of improving user experience while using  [NVIDIA GameStream](https://www.nvidia.com/en-us/shield/support/shield-tv/gamestream/) technology. To be more specific, this application is targeted at users with multi-monitor systems, who are tired of manually enabling/disabling displays, changing their resolution and other settings which interfere with a simple desire to just play video games.
People with single monitor host machines can also benefit from this tool since it comes with various quality of life features.

## The Problem

```text
In current state, there is an issue on NVIDIA end - GameStream technology lacks functionality and support
for devices with more than one monitor. In fact, having more than one monitor enabled during streaming
session completely breaks functionality for devices with touch (ex. Surface Pro) due the way their
technology is implemented.

Therefore, at this time responsibility falls on users shoulders to figure things out - be it disconnecting
monitors and TVs physically or via settings, writing SSH scripts to launch from client before starting
session, or even as far as building dedicated machines.

This is not a good option for those of us who use their computer in multiple ways - for work, playing
games on connected TV screen and ocasionally streaming games to a tablet which utilizes touchscreen.

TL;DR - No multi-monitor support, breaks touch, breaks heart.
```

## The Solution

```text
Write to NVIDIA to spread awareness of this issue and let them bring solution to this problem
on a professional level. Move the technology forward.

But meanwhile, I offer you Profile Engine - while it does not solve the core issues,
it sure makes life less miserable.
```

---

## Features

- ## Profile Engine

  - This software utilizes native Windows API for managing display configurations.
  - Saves/Loads display devices states.
  - Infinite amount of custom profiles, which later can be called by using CLI `-mode` argument.
  - DISCLAIMER: If you're using "Duplicate these displays" in display settings, Profile Engine functionality will not work.

- ## Active Monitoring

  - Will load `streaming` defined profile on GameStream session start.
  - On  GameStream session end, will load back the `default` defined profile.
  - Uses [CreateToolhelp32Snapshot](https://docs.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-createtoolhelp32snapshot) Windows API call as monitoring mechanism.

- ## Remote CLI Support

  A separate executable for your client device, which communicates with host.
  - Commands:
    - Switch Profile
    - Launch Playnite
    - Restart Explorer
    - Open Task Manager
    - Kill Process

- ## Keybinds for WinKey, Alt+Tab & Ctrl+Shift+Esc

  - F9 - WinKey.
  - F10 - Alt+Tab.
  - F11 - Launches Task Manager.
  - Can be enabled/disabled through system tray menu at any given time.

- ## Other

  - Theme Awareness: tray icon adjusts to proper color upon switching windows theme.
  - Playnite: support for opening/closing playnite automatically upon session start/end.
  - Logging: has easily accessible logfile to see what has been going on.
  - All-in-One - single executable which extracts all required files on first launch.

---

## CLI Arguments

Profile Engine provides the following commands:

### Host Commands

```text
-mode NAME | Profile switch mechanism, ex. -mode default
-save NAME | Save current display configuration as a new profile, ex. -save streaming
-kill NAME | Kill a specific process, ex. -kill notepad.exe
-list      | List currently known profiles
-playnite  | Launch Playnite
-taskmgr   | Open Task Manager
-rexp      | Restart explorer.exe
-help, -h  | Show a help message with command arguments.
```

Profile Engine comes with additional executable (extracted to `client` directory near `peng.exe`) which you should put on your client machine for Remote CLI functionality. Following commands are available:

### Remote CLI Commands

```text
-mode NAME | Profile switch mechanism, ex. -mode default
-kill NAME | Kill a specific process, ex. -kill notepad.exe
-playnite  | Launch Playnite
-taskmgr   | Open Task Manager
-rexp      | Restart explorer.exe
-help, -h  | Show a help message with command arguments.
```

---

## Configuration

Profile Engine comes as a single `.exe` file. On first launch it will extract additional files into root directory near main executable. You shold edit generated `config.yml` file and configure application before running.

For best practice, after downloading executable, place it in dedicated directory.

Example:

```cmd
C:\Software\ProfileEngine\peng.exe
```

After first time setup, structure of directories will look like this:

```text
# ROOT Folder/ ex. "C:\Software\ProfileEngine\"
# peng.exe                     ##### Primary executable
# keybinds.exe                 ##### Will be extracted, only if enabled.
# config.yml                   ##### Configuration file
# logfile.log                  ##### Logging informational file

# - profiles/ # Subfolder
# ---- default                 ##### Default display configuration

# - client/ # Subfolder
# ---- rpeng.exe               ##### Remote CLI file
# ---- config.yml              ##### Config for rpeng.exe
```

---

### Overview

This is `config.yml` file, which will be generated on first application launch:

```yml
logging:
  enabled: true
  logfile: logfile.log

monitoring:
  exe: nvstreamer.exe
  enabled: true
  delay: 1

server:
  enabled: true
  port: 8090

explorer_delay_ms: 500

keybinds:
  enabled: true

playnite:
  enabled: false
  mode: fullscreen
  path: C:\Users\USERNAME\AppData\Local\Playnite

profiles:
  default: default
  ```

### Logging

```yml
logging:
  enabled: true
  logfile: logfile.log
```

You can change the state of enabled/disabled by changing `true` to `false`. Additionally, you can change the name of the output file.

### Monitoring

```yml
monitoring:
  exe: nvstreamer.exe
  enabled: true
  delay: 1
```

This option is responsible for automatic profile switch upon new streaming session detection. If you do not need this and want only CLI functionality, it can be disabled.

```yml
exe: nvstreamer.exe
```

This line tells Profile Engine what process to monitor. This configuration should not be changed but is given to user for the chance NVIDIA will change process name in the future.

```yml
delay: 1
```

Delay in seconds for how often to scan for existence of active stream. It utilizies minimum amount of resources and is set to 1 by default.

### Remote CLI

```yml
server:
  enabled: true
  port: 8090
```

Enabled by default. This option tells Profile Engine if you want to receive Remote CLI commands from client app (rpeng.exe). Port configuration should match the port in client `config.yml`

### Explorer

```yml
explorer_delay_ms: 500
```

Delay in milliseconds for restarting explorer.exe. If `-rexp` argument is not restarting explorer.exe properly, increase this value.

### Keybinds

```yml
keybinds:
  enabled: true
```

When enabled, a `keybinds.exe` file will be extracted into root directory,  which will bind `F9`, `F10` & `F11` keys to `WinKey`, `Alt+Tab` and `Ctrl+Shift+Esc`.

 This option exists to help using these keys on your client device, because by default GameStream protocol restricts these key triggers.

 If you are not satisfied with default `F9`, `F10`, `F11` keys, you can download [AHK](https://www.autohotkey.com/) and write your own script, compile it into executable and replace `keybinds.exe` with it.

### Playnite

```yml
playnite:
  enabled: false
  mode: fullscreen
  path: C:\Users\USERNAME\AppData\Local\Playnite
```

Profile Engine comes with [Playnite](https://playnite.link/) support. This option allows automatic launch of Playnite upon GameStream session start, and automatic close upon session exit.

Set `enabled` to `true` to enable and select one of the following modes: `desktop` or `fullscreen`. Set path to directory of your Playnite installation. Please note: Do not add `\` at the end of the path, Profile Engine will automatically pick correct executable according to mode configuration.

### Profiles

```yml
profiles:
  default: default
```

After first time setup, you will have only one file in your profiles folder, called `default`. It was automatically generated during first time setup process and it is a profile with your current desktop monitor layout. If you want automatic profile switching upon stream session detection working, you will need to create a `streaming` profile.

`default` and `streaming` profiles are mandatory and their names should not be changed. These two profiles are utilized in session monitoring mechanics and are hardcoded.

If you want to create a `streaming` profile, best way is to disable all secondary displays, leave only one display enabled. Update resolution to the one you want to have on client (for Surface Pro and alike devices with custom aspect ratio you can create custom resolutions via NVIDIA Control Panel). Then just call `--save streaming`.

```text
INFO: If you are using active monitoring, default profile primary display
should be the same as streaming profile primary display.

The Profile Engine works just fine switching primary monitors around, but the issue
is that nvstreamer.exe starts before Profile Engine switches primary monitor and detaches
extra displays, so it can cause a black/white screen and getting kicked/disconnected from
the session. I've tested this extensively, and it works in around 14 out of 20 cases.

If your use case requires having different primary monitor for streaming profile, you can
disable active monitoring and use CLI command to switch profile before initiating streaming
session either on host or through Remote CLI client to avoid described above issues.
```

You can add custom profiles by using `-save` command via CLI, which will append all required information to `config.yml`.

The format should be in similar fashion:

```cmd
C:\Software\ProfileEngine\peng.exe -save config_file_name
C:\Software\ProfileEngine\peng.exe -save couch
C:\Software\ProfileEngine\peng.exe -save streaming
C:\Software\ProfileEngine\peng.exe -save tv_primary
```

The name you give your profile will be the one you will be calling through CLI:

```cmd
C:\Software\ProfileEngine\peng.exe -mode config_file_name
C:\Software\ProfileEngine\peng.exe -mode couch
C:\Software\ProfileEngine\peng.exe -mode streaming
C:\Software\ProfileEngine\peng.exe -mode tv_primary
```

Your `profile` section in `config.yml` will look like the following:

```yml
profiles:
  default: default
  streaming: streaming
  config_file_name: config_file_name
  couch: couch
  tv_primary: tv_primary
```

Configuration file has `.yml` file extension, which represents [YAML](https://yaml.org/), a human friendly data serialization standard.

`NOTE:` Changing these options requires application restart in order for them to take effect (if you're running active monitoring). CLI Arguments are available instantly.

`NOTE:` Best way to use this software, is by streaming your desktop, instead of a specific game.

---

## Downloads

| OS      | Platform | Link            |
| ------- | -------- | --------------- |
| Windows | 64-bit   | [Download](https://github.com/ZeroHumanInteractions/profile_engine/releases/download/v1.0.0.1/peng-x64.zip) |
| Windows | 32-bit   | [Download](https://github.com/ZeroHumanInteractions/profile_engine/releases/download/v1.0.0.1/peng-x86.zip) |

---

## Installation

1. Download a file for your platform and extract it into dedicated folder.
2. Run executable once, if everything went good, you should see new files in a directory.
3. Open config.yml with your preferred editor and configure it.
4. Launch executable again and enjoy.

If you want to easily call `peng.exe` from anywhere by opening terminal, add path to [environment](https://helpdeskgeek.com/windows-10/add-windows-path-environment-variable/).

---

## Limitations

Important things to know:

When you boot to Windows, NVIDIA remembers the currently default primary device, and will use it for streaming, even if you disable/change it. The only solution is to change your primary display to the one you use for streaming, configure it, restart your device and then change to configuration you will be using when you're using host device physically. This way, upon streaming start, GameStream will use display, which was primary at the time of windows startup.

---

## Feedback

Looking for all kind of feedback on the following topics:

- Your thoughts on current problem with official support for multi-monitor host devices.
- Your experience with streaming from host with multiple-display configuration and using touchscreen devices as client.
- Information about whether profiles remember your display refresh rate or cause issues.
- Features you would like to see implemented.
