---
layout: post
title:  "Wake up relaxed and energized in your smarthome..."
subtitle: "with Sonos and Home Center 3"
description: "Wake up relaxed and energized in your smarthome with Sonos and Home Center 3."
author: joep
categories: [ Home Center 3 ]
tags: [ Lua, Scene, Quick App ]
image: assets/images/relaxedwakeup.jpg
beforetoc: "Now that spring has sprung and the sun's rays are poking through the curtains, it's great to snooze in bed at the weekend with your favorite music in the background. "
toc: true
---

Today I'll show you how you can conveniently ensure that your Sonos speaker automatically starts playing in your home office during the week and your favorite music starts playing from the speaker in your bedroom at the weekend by using the [Home Center 3](https://www.fibaro.com/nl/products/home-center-3/).

![relaxinbed](../assets/images/relaxedwakeup1.jpg)

## What do you need?

- A Fibaro Home Center 3.
- Two Sonos speakers.
- The [Sonos Player](https://marketplace.fibaro.com/items/sonos-player-for-hc3) for HC3 Quick App.
- A piece of LUA flavor that I explain to you in this article.

> I assume that you can figure out how to download and install a Quick App from the Fibaro Marketplace and how to create a LUA scene. This article only discusses a piece of code to let your smarthome choose which Sonos will play music.

## How it's done

You can determine the day of the week with the following code:

```lua
local day = os.date("%a")
```

The output this code generates is: **Mon**, **Tue**, **Wed**, **Thu**, **Fri**, **Sat**, **Sun**. Now you can determine what day it is and which Sonos should play music:

```lua
if (day == "Sat" or day == "Sun") then
    fibaro.call(id, "setVolume", "5")
    fibaro.call(id, "playFromUri","stream.laut.fm/elvisjunkie")
end
```

You can expand this even further. Suppose you leave to work every other week on Friday and only in the even weeks. First retrieve the week number with the following code:

```lua
local week = os.date("%W")
```

This allows you to determine whether it is a **Friday** of an **even** week:

```lua
if (week % 2 == 0 and day == "Fri") then
    fibaro.call(id, "setVolume", "5")
    fibaro.call(id, "playFromUri","stream.laut.fm/hiphop-forever")
end
```

With the above examples you can program your smart home to your heart's content to start your mornings as relaxed as possible.
