---
key: 3
title: Hibernate your home
permalink: /hibernate-home/
excerpt: Advanced LUA scene to automatically turn off all lights (and save state)
image: aurora-scene.jpg
background-image: aurora-scene.jpg
---

# Advanced LUA scene to automatically turn off all lights (and save state)

Januari 12, 2019 
_**Applies to:** Fibaro Home Center 2 and Philips HUE plug-in._

## Goals

* Check all _z-wave_ lights that are on.
* Check all _Philips Hue_ lights that are on.
* Turn these lights off when everybody is away from home.
* Save the lights that where turned off to turn them back on when someone arrives at home.
* Exclude lights to turn off when using a _go to bed_ scene trigger (make scene for global use).

After I build [reliable precense detection](/presence/) I want to automatically turn off the lights when nobody is at home. I tought it would be nice if the controller _remembers_ _the state_ of the house and returns to that state when the first person arrives back at home. When there is a lot of time passed a standard arrival routine will run, because the previous light state is not important anymore right then.

The scene checking for lights needs to be universal, so I can use it for a _go to bed_ scene also. For this purpose the scene must handle _exclusions_. (you don't want to turn of the light that you just turned on for your bedtime scene).

## TL;DR

* Create an _universal scene_ for checking the lights.
* Create a scene for _leaving_ home.
* Create a scene for _arriving_ at home.
* Optionally create a _go to bed_ scene._
* Save the light state to a _global variable_

## How I implemented it

### In words

When Node-RED [detects](/presence/) that somebody left the house it sets a personal presence global variable to `No` in Home Center 2. In my case, if both `JoepPresent` and `MoniquePresent` are set to `No` the _leave home_ scene is triggered. This scene sets another global variable `HomeState` to `away`. This is done to tackle the issue when you arrive at home and on the driveway your phone get's connected to your home Wi-Fi and the system responds immediatly. You don't want that to happen. You want to trigger the _arrive home_ scene with a door- or motion sensor. Then it's triggered as you physically step into your house.

The _leaving home_ scene triggers the _turn off all lights_ scene to save the light state. If the first person arrive's back at home the _arriving home_ scene is triggered by the earlier mentioned door- or motion sensor.

### Devices you need

I can enter my house from three doors. At the front door I use a _Fibaro Door/Window Sensor_ and the other rooms are equipped with a _Fibaro Motion Sensor_. You have to change the sensors in my LUA scenes to your own use case.

### Global variables you need

These are the global variables you need for the routine described on this page only. Presence detection variables are not described as you can have another presence detection system than what I use.

| Global Variable   | Type       | Value        |
| ----------------- | ---------- | ------------ |
| HomeState         | Predefined | athome, away |
| TurnOffExclusions | Normal     | 0            |

### Scene 1: check and turn off all lights explained

You can download the full LUA scenes at the bottom of this page. I only describe snippets of my code to make you understand what it does and show the challenges I ran into.

#### Function to check if value exists in array

I want to keep a table with all rooms with lights on. This information I save for future use, when I want to send a push message with rooms with lights still on message for example. To add only new rooms to the table I wrote the following function:

```lua
local function has_value (tab, val)
  for index, value in ipairs(tab) do
    if value == val then
      return true
    end
  end
  return false
end
```

#### Get exclusions from the global variable

Before starting this scene from within the _leave home_ scene I _set_ a global variable with the _id's_ of the lights that not have to turn off automatically:

```lua
local exclusionsVar = fibaro:getGlobalValue("TurnOffExclusions")
local exclusions = {}
for match in (exclusionsVar..'|'):gmatch("(.-)"..'|') do
  table.insert(exclusions, match);
end
```

#### Check Z-Wave lights

When the exclusions are read I can loop over all devices and read the `isLight` value to determine if the device is a light:

```lua
if fibaro:getValue(i, "isLight") == "1" then
...
```

With this check I don't have to keep a table with all light source id's. When you add a light to your house it's automatically incorporated in the scenes. Then I check if the device is on:

```lua
if (fibaro:getValue(i, "value") >= "1")
...
```

If both conditions match the device is turned off and the id is saved (if it is not excluded). The full code block for the check is:

```lua
local i = 0
local maxNodeID = 1000
for i = 0, maxNodeID do
  if fibaro:getValue(i, "isLight") == "1" then
    if (fibaro:getValue(i, "value") >= "1") then
      local DeviceName = fibaro:getName(i)
      local RoomName = fibaro:getRoomNameByDeviceID(i)
      if not has_value(roomsWithLightsOn, RoomName) and RoomName ~= "unassigned" then
        table.insert(roomsWithLightsOn, RoomName)
      end
      if RoomName ~= "unassigned" then
        devicesOn = devicesOn .. i .. '|'
        fibaro:debug(os.date("%a, %b %d") .. " Device is on: " .. i .. " " .. DeviceName .. " " .. RoomName)
        -- If device if not on the exclusion list, turn it off
        local excluded = has_value(exclusions, tostring(i))
        if not has_value(exclusions, tostring(i)) then
          fibaro:call(i , "turnOff")
        else
          fibaro:debug(os.date("%a, %b %d") .. " Excluded device: " .. i)
        end
      end
    end
  end
end
```

#### Check Philips Hue lights

If you have Philips Hue devices in your home and use the Fibaro Hue plug-in the Hue light bulbs are not detected with the code above. You can detect Hue devices with the following line of code:

```lua
if fibaro:getType(j) == "com.fibaro.philipsHueLight" then
...
```

To check if a Hue light is on you can use the following line of code:

```lua
if (fibaro:getValue(j, "on") == "1") then
...
```

For Hue devices I created another code block and add also save the id's from the lights that are on:

```lua
local j = 0
local maxHueID = 1000
for j = 0, maxHueID do
  if fibaro:getType(j) == "com.fibaro.philipsHueLight" then
    if (fibaro:getValue(j, "on") == "1") then
      local DeviceName = fibaro:getName(j)
      local RoomName = fibaro:getRoomNameByDeviceID(j)
      if not has_value(roomsWithLightsOn, RoomName) and RoomName ~= "unassigned" then
        table.insert(roomsWithLightsOn, RoomName)
      end
      devicesOn = devicesOn .. j .. '|'
      fibaro:debug(os.date("%a, %b %d") .. " Hue device is on: " .. j .. " " .. DeviceName .. " " .. RoomName)
      -- If Hue device if not on the exclusion list, turn it off
      if not has_value(exclusions, tostring(j)) then
        fibaro:call(j , "turnOff")
      end
    end
  end
end
```

#### Saving the light state

The above scripts checked the lights that are on in your house and turned them off (if they where not excluded). I saved a table with these lights and now I write this table to a global variable to use with the _arrive home_ scene. With this info I can turn all lights back on in the same state when we left the house.

```lua
fibaro:setGlobal("SleepingLights", devicesOn:sub(1, -2)) -- remove last |
```

### Scene 2: leave home explained

### Scene 3: arrive home explained

**_Still writing, check back soon!_**