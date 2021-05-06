---
key: 11
title: Windows 10 Fibaro Home Center 2 and 3 app
product: Fibaro Home Center 2 and 3 and Windows 10
permalink: /win10app/
excerpt: Control your Fibaro Home Center 2 or 3 with Windows 10
image: win10app.png
background-image: win10app.png
---

# Control your Fibaro Home Center 2 or 3 with Windows 10<!-- omit in toc -->

Initial: November 5, 2020  
Update: November 14, 2020  
Update: May 6, 2021  
_**Applies to:** Fibaro Home Center 2, Fibaro Home Center 3, Windows 10_

## Download links for the impatient<!-- omit in toc -->

* [Version 1.2.0 direct file download](https://github.com/joepv/fibaro-control/releases/download/v1.2.0.0/Fibaro.Control.exe) 
* [Latest release page](https://github.com/joepv/fibaro-control/releases)

## Table of Contents<!-- omit in toc -->
- [Goal](#goal)
- [TL;DR](#tldr)
- [What my application does](#what-my-application-does)
- [Quick download](#quick-download)
- [Manual](#manual)
- [Run a scene](#run-a-scene)
- [Toggle a light on/off](#toggle-a-light-onoff)
- [Reload scenes and lights](#reload-scenes-and-lights)
- [Future roadmap](#future-roadmap)
- [Disclaimer](#disclaimer)

## Goal

While working from home for eight months now I wanted to toggle a light or run a scene from my Windows 10 laptop. After some Googling I didn't find an (free) application so I decided to dust off my some C# knowledge from around 2000 and create a Windows tray application myself.

## TL;DR

Run a scene or toggle a light on/off from the Windows 10 system tray.

## What my application does

The program loads your configured rooms, devices and scenes from your HC2/HC3/HC3 and puts them in a menu in the system tray. Now you can run a scene or toggle a light switch without reaching for a wall switch or your phone. Neat huh!

## Quick download

You can download the [latest release](https://github.com/joepv/fibaro-control/releases/latest) from the [releases](https://github.com/joepv/fibaro-control/releases) page.

Just select the `Fibaro.Control.exe` file from the assets if you just want to use the program.

![Assets](https://raw.githubusercontent.com/joepv/fibaro-control/master/Documentation/Image004.png "Fibaro Control: Assets")
## Manual

💡 Note: There is no setup. Just [download](https://github.com/joepv/fibaro-control/releases/latest) and copy the `Fibaro Control.exe` file to `C:\Program Files\`.

Start the `Fibaro Control.exe` program and log in to your Fibaro HC2/HC3:

![Login Screen](https://raw.githubusercontent.com/joepv/fibaro-control/master/Documentation/Image001.png "Fibaro Control: Login Screen")

The login data is automatically encrypted and saved to the Windows registry hive:

```
HKEY_CURRENT_USER\SOFTWARE\Joep\FibaroControl
```

## Run a scene

* **Right click** the **HC2/HC3 icon** in the system tray and go to the **scenes** menu.
* **Click** on a scene to **activate** it.

![Run a Scene](https://raw.githubusercontent.com/joepv/fibaro-control/master/Documentation/Image002.png "Fibaro Control: Run a Scene")

## Toggle a light on/off

* **Right click** the **HC2/HC3 icon** in the system tray and go to the **room** menu where the light is located.
* **Click** on a **light source** to turn it **on** or **off**.

![Toggle a Light](https://raw.githubusercontent.com/joepv/fibaro-control/master/Documentation/Image003.png "Fibaro Control: Toggle a Light")

## Reload scenes and lights

* **Right click** the **Home Center icon** in the system tray and click **settings**.
* **Click** the **reload** button.

## Future roadmap

The following functions I may add in the future:

* Toggle (Fibaro) Wall Plug that is configured as a switch.
* Add an option to read the Unifi Controller to determine your Windows 10 device location in the office/house and show only the rooms in that location.

## Disclaimer

This Home Center control program for Windows 10 is my personal project written in C#. I am not affiliated, associated, authorized, endorsed by, or in any way officially connected with Fibar Group S.A.