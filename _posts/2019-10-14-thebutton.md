---
layout: post
title:  "The Button: Lua code samples to use with Home Center 2"
description: "The Button: Lua code samples to use with Home Center 2"
author: joep
categories: [ Home Center 2 ]
tags: [ Lua, Scene ]
image: assets/images/thebutton.jpg
toc: true
rating: 4.5
---

Sometimes I want to manually push a switch to turn on a device or simply run a light scene. Sometimes manual operation is faster than automation. The first [Fibaro Button](https://www.fibaro.com/us/products/the-button/) I bought was controlled with a *block scene* to switch the light on and off at my daughters bedroom. Nowadays I'm using LUA code and the options are endless.

## LUA template explained

I bought another button and created a LUA scene that detects the number of button presses. To catch the push event you have to add the *device id* followed with `CentralSceneEvent` to the LUA *script header*. My *device id* is `194`, so the header looks like:

```lua
--[[
%% properties
%% events
194 CentralSceneEvent
%% globals
--]]
```

When the button is pressed the LUA script runs and you have to catch the *event data* to detect how many times the button is pressed:

```lua
local buttonData = fibaro:getSourceTrigger()["event"]["data"]
local pressCount = tostring(buttonData["keyAttribute"])
```

LUA lacks a C style switch statement, so I use a `if/elseif` routine to detect how many times the button is pressed to run the code I want:

```lua
if (pressCount == "Pressed") then
  -- BEGIN 1x pressed
  fibaro:debug("Button 1x pressed.")
elseif (pressCount == "Pressed2") then
  -- BEGIN 2x pressed
  fibaro:debug("Button 2x pressed.")
elseif (pressCount == "Pressed3") then
  -- BEGIN 3x pressed
  fibaro:debug("Button 3x pressed.")
elseif (pressCount == "Pressed4") then
  -- BEGIN 4x pressed
  fibaro:debug("Button 4x pressed.")
elseif (pressCount == "Pressed5") then
  -- BEGIN 5x pressed
  fibaro:debug("Button 5x pressed.")
elseif (pressCount == "HeldDown") then
  -- Held down
  fibaro:debug("Button held down.")
elseif (pressCount == "Released") then
  -- Released
  fibaro:debug("Button released.")
else
  fibaro:debug("Error: unknown event data received!")
end
```

You can download the full LUA template from my GitHub repository with the link [at the bottom](#Download my LUA template) on this page.

## Extra's

With the examples below I describe some simple actions you can use in my LUA scene template for the Fibaro Button. You can easily change the *device id* in the examples and add the code within the *if block*.

### Switch a light on/off

A simple example to switch a light on/off. If the light is on, it switches off and vise versa:

```lua
local lightbulbId = 126
local lightbulb = tonumber(fibaro:getValue(lightbulbId, "value"))

if (lightbulb > 0) then
  fibaro:debug("Switch lightbulb off...")
  fibaro:call(lightbulbId, "turnOff")
else
  fibaro:debug("Switch lightbulb on...")
  fibaro:call(lightbulbId, "turnOn")
end
```

### Run a different light scene before and after 19:00

I want to run a light scene when I double press The Button, but I want it to run my *Cooking and Eating* scene when I double press it before `19:00` and run the *Relaxed Evening* scene when I double press it after seven in the evening:

```lua
local currentHour = tonumber(os.date("%H%M"))

if currentHour > 0400 and currentHour < 1900 then
  fibaro:debug("Run Cooking and Eating scene...")
  local cookEatSceneId = 23
  if fibaro:countScenes(cookEatSceneId) < 1 then
  	fibaro:startScene(cookEatSceneId)
  else
    fibaro:debug("Error: The Cooking and Eating scene is already running!")
  end
else
  fibaro:debug("Run Relaxed Evening scene...")
  local relaxSceneId = 19
  if fibaro:countScenes(relaxSceneId) < 1 then
  	fibaro:startScene(relaxSceneId)
  else
    fibaro:debug("Error: The Relaxed Evening scene is already running!")
  end
end
```

With the `fibaro:startScene(<id>)` function you can start a scene from *another* LUA scene. If you define your light scenes in separate blocks or LUA scenes and call them with this function you can change the scene easily without the need to edit a lot of different scenes with the same code piece.

For example my *Cooking and Eating* scene with id `23` looks like:

```lua
--[[
%% properties
%% events
%% globals
--]]

fibaro:call(20, "setValue", "10") -- Dim wardrobe spots to 10%
fibaro:call(21, "turnOn") -- Turn on garden lights

fibaro:call(30, "changeHue", "5021") -- Change hue Philips Hue ledstrip on stairwell
fibaro:call(30, "changeSaturation", "199") -- Change saturation Philips Hue ledstrip on stairwell
fibaro:call(30, "changeBrightness", "114") -- Change brightness Philips Hue ledstrip on stairwell
fibaro:call(30, "turnOn") -- Turn on Philips Hue ledstrip on stairwell

fibaro:call(22, "setValue", "25") -- Dim kitchen spots to 25%
fibaro:call(23, "setValue", "10") -- Dim table lights to 10%
fibaro:call(24, "turnOn") -- Turn on desk lamp
fibaro:call(25, "setValue", "30") -- Dim window spots to 15%
fibaro:call(26, "turnOn") -- Turn on monkey lamp
```

And you run it from another LUA scene like:

```lua
local cookEatSceneId = 23
if fibaro:countScenes(cookEatSceneId) < 1 then
	fibaro:startScene(cookEatSceneId)
else
  fibaro:debug("Error: The Cooking and Eating scene is already running!")
end
```

Pro tip: notice that I first check if the scene is already running with the `fibaro:countScenes(<id>)` function. 

## Download my LUA template

You can download The Button LUA template from:

* [LUA scene code](https://github.com/joepv/fibaro/blob/master/TheButton.lua)