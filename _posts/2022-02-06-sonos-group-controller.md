---
layout: post
title:  "Sonos Group Controller Quick App"
description: "Automatically control, group and ungroup your Sonos players with Home Center 3"
author: joep
categories: [ Home Center 3, Sonos ]
tags: [ Lua, Scene, Quick App ]
image: assets/images/hc3-sonosgroup.png
beforetoc: "In this blog I'll show you how to use the Sonos Group Controller Quick App I created to fully integrate your Sonos System into your home automation."
toc: true
---

There are some Sonos Quick Apps already available for the Home Center 3, but none of them can group and ungroup Sonos speakers. I wanted to create a Quick App that has little to no impact on my network. With the Home Center 3 it's not possible to subscribe to Sonos API events and you have to use polling. I don't like this to retrieve status updates so I used the minimal [Sonos Player](https://marketplace.fibaro.com/items/sonos-player-for-hc3) from [tinman](https://marketplace.fibaro.com/profiles/fibaro-user-unnamed-0d8b1f6e-6a22-4ed5-92be-7927e3617067)/[Intuitech](https://intuitech.de/) as a base to start.

## Possibilities

From a Lua scene (or Quick App) you can:

- Start a Sonos Favorite at a specified volume.
- Set the volume of a Sonos speaker.
- Save the player state (like `PLAY`/`STOP`) and pause.
- Get the previous player state and resume this state.
- Create or add a Sonos speaker to a group.
- Remove a Sonos speaker from agroup.

You can do more with the Quick App, like play an InTune / webradio station or play a local `.mp3` file from your NAS. Also you can send standard commands like `play`, `pause`, `stop`, `next` and `previous` but in this blog I focus on the above list.

## Installation

The installation is pretty straigtforward with minimal configuration.

### Prerequisites

- My [Sonos Group Controller Quick App](https://docs.joepverhaeg.nl).
- To spice it up, my [Sonos S2 icon set](https://forum.fibaro.com/files/file/476-sonos-s2-icons-by-joep/).

### Adding the device

1. **Start** your favorite browser and open your Home Center 3 dashboard by typing the correct URL for your HC3,
2. Go to **Settings** and **Devices**,
3. **Click** the blue **+** icon to add a new device,
4. In the **Add Device** dialog click on **Other Device**,
5. Choose **Upload File** and upload the `.fqa` file downloaded earlier,
6. Go to the device and **edit** the Quick App variables to **set the IPv4 address** of your Sonos speaker:

![hc3-sonos-zone-controller](../assets/images/hc3-sonos-gc-01.png) 

If you have configured the Quick App correctly the **first 4 favorites** from your **My Sonos** list will be added as buttons:

![hc3-sonos-zone-controller](../assets/images/hc3-sonos-gc-02.png)

### My Sonos list notice

Sonos stores playlists, InTunes stations and albums in *My Sonos* in alphabetical order. You cannot adjust this. The first 4 items are shown, but you can play all favorites easily which I show you later on.

