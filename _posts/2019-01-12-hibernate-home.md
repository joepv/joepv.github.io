---
layout: post
title:  "Hibernate your home with an advanced Home Center 2 scene to automatically turn off all lights (and save state)"
description: "Hibernate your home with an advanced Home Center 2 scene to automatically turn off all lights (and save state)"
author: joep
categories: [ Home Center 2, Philips Hue ]
tags: [ Lua, Scene ]
image: assets/images/hibernate-home.jpg
rating: 4.5
toc: true
---

After I build [reliable presence detection](/presence/) I wanted to automatically turn off the lights when nobody is at home. I though it would be nice if the controller _remembers_ _the state_ of the house and returns to that state when the first person arrives back at home. When there is a lot of time passed a standard arrival routine will run, because the previous light state is not important anymore right then.

The scene checking for lights needs to be universal, so I can use it for a _go to bed_ scene also. For this purpose the scene must handle _exclusions_. (you don't want to turn of the light that you just turned on for your bedtime scene).

## Goals

* Check all _z-wave_ lights that are on.
* Check all _Philips Hue_ lights that are on.
* Turn these lights off when everybody is away from home.
* Save the lights that where turned off to turn them back on when someone arrives at home.
* Exclude lights to turn off when using a _go to bed_ scene trigger (make scene for global use).

## TL;DR

* Create an _universal scene_ for checking the lights.
* Create a scene for _leaving_ home.
* Create a scene for _arriving_ at home.
* Optionally create a _go to bed_ scene._
* Save the light state to a _global variable_

## How I implemented it

### In words

When Node-RED [detects](/presence/) that somebody left the house it sets a personal presence global variable to `No` in Home Center 2. In my case, if both `JoepPresent` and `MoniquePresent` are set to `No` the _leave home_ scene is triggered. This scene sets another global variable `HomeState` to `away`. This is done to tackle the issue when you arrive at home and on the driveway your phone get's connected to your home Wi-Fi the system responds immediately. You don't want that to happen. You want to trigger the _arrive home_ scene with a door- or motion sensor. Then it's triggered as you physically step into your house.

The _leaving home_ scene triggers the _turn off all lights_ scene to save the light state. If the first person arrive's back at home the _arriving home_ scene is triggered by the earlier mentioned door- or motion sensor.

### Devices used

I can enter my house from three doors. At the front door I use a _Fibaro Door/Window Sensor_ and the other rooms are equipped with a _Fibaro Motion Sensor_. You have to change the sensors in my LUA scenes to your own use case.

### Global variables

These are the global variables you need for the routine described on this page only. Presence detection variables are not described as you can have another presence detection system than what I use.

| Global Variable   | Type       | Value        |
| ----------------- | ---------- | ------------ |
| HomeState         | Predefined | athome, away |
| TurnOffExclusions | Normal     | 0            |
| SleepingLights    | Normal     | 0            |

### Scene 1: check and turn off all lights explained

You can download the full LUA scenes at the bottom of this page. I only describe snippets of my code to make you understand what it does and show the challenges I ran into.

#### Function to check if value exists in array

I want to keep a table with all rooms that have lights on. This information is for future use, when I want to send a push message with rooms with lights still on message for example. To only add *new* rooms to the table I wrote the following function:

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

Before starting this scene from within the _leave home_ scene I _set_ a global variable with the _id's_ of the lights that not have to turn off automatically (see later on this page), I read this global variable with the following code:

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

With this check I don't have to keep a table with all light source id's. When you add a light to your house it's automatically incorporated in the scenes. Then I check if the device is *on*:

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

If you have Philips Hue devices in your home and use the Fibaro Hue plug-in the Hue light sources are not detected with the code above. You can detect Hue devices with the following line of code:

```lua
if fibaro:getType(j) == "com.fibaro.philipsHueLight" then
...
```

To check if a Hue light is on you can use the following line of code:

```lua
if (fibaro:getValue(j, "on") == "1") then
...
```

For Hue devices I created another code block that also save the id's from the lights that are on:

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

The above scripts checked the lights that are still on in our house and turned them off (if they where not excluded). I saved a table with these lights and now I write this table to a global variable to use with the _arrive home_ scene. With this info I can turn all lights back on in the same state when we left the house.

```lua
fibaro:setGlobal("SleepingLights", devicesOn:sub(1, -2)) -- remove last |
```

### Scene 2: leave home explained

This scene is pretty simple. If both `JoepPresent` and `MoniquePresent` are set to `No` the `HomeState` variable is set to `away` and the _TurnOffAllLights_ scene is started. These variables are set with my [presence detection scene](/presence/).

```lua
if fibaro:getGlobalValue("JoepPresent") == "No" and fibaro:getGlobalValue("MoniquePresent") == "No" then
  fibaro:setGlobal("HomeState", "away")
  fibaro:debug(os.date("%a, %b %d") .. " Set HomeState to away.")
  fibaro:setGlobal("TurnOffExclusions", "0") -- no exclusions
  if fibaro:countScenes(33) < 1 then
    fibaro:startScene(33) -- run Turn All Lights Off Scene
  else
    fibaro:debug(os.date("%a, %b %d") .. " Turn Of All Lights scene already running!")
  end
end
```

Note: the _TurnOffAllLights_ scene has id `33` in my system, you have to change that to the id on your own Home Center 2.

### Scene 3: arrive home explained

The LUA scene to run when the first person arrive's at home is a little bit more complex. At home I have tree entrances:

| Entrance     | Sensor                    |
| ------------ | ------------------------- |
| Front door   | Fibaro Door/Window Sensor |
| Garage door  | Fibaro Motion Sensor      |
| Terrace door | Fibaro Motion Sensor      |

As a house member can arrive at any of these doors I have to check all sensors for _activity_ and if one sensor sends a _breached_ state the Home Center 2 needs to act and start the _arrive home_ scene.

#### Reading the sensors

With the following line I check all three sensors for activity:

```lua
if tonumber(fibaro:getValue(176, "value") > 0 or tonumber(fibaro:getValue(164, "value") > 0 or tonumber(fibaro:getValue(130, "value") > 0 then
...
```

#### Tackling the motion sensor alarm cancellation delay

The _door sensor_ sets it's value to `1` when I open the door, and sets it's value back to `0` when I close the front door. The _motion_ sensors keep their _value_ at `1` until the _alarm cancellation delay_ is reached (set with _parameter 6_).

This is a problem. When I enter the garage to get some stuff before leaving the house the _value_ of the motion sensor stays `1` during the _alarm cancellation delay_, even if there is no motion anymore. If I grab something the garage and leave through the front door within this period the *value* of the motion sensor is still `1` and the lights go immediately back on when opening the front door. It took a while to figure this out!

I found out that the `lastBreached` parameter keeps a timestamp value from the last time motion was detected. In the LUA scene I check if the _active breach_ is within the _alarm cancellation delay_ parameter value (6). In my case this is _5 minutes_ (or 300 seconds). If this is the case I report no movement and it doesn't trigger the arrival scenario.

Another problem is that if the sensor is not breached the _lastBreached_ value is `0`. The sensor is so fast the scene reads 0 as soon there is motion detected and it thinks there is no movement. I fix this behaviour with the following code:

```lua
local lastBreachedGarage = currentime - tonumber(fibaro:getValue(164, "lastBreached"))
local currentime = os.time(os.date("!*t"))
local noMovement = 1
if lastBreachedGarage > 2 and lastBreachedGarage < 310 then
  noMovement = 0
end
```

#### Retrieving the light state and turn the lights back on when arriving home

If all these parameters are correct and the `HomeState` global variable is `away` I set the `HomeState` back to `athome` and check with a Fibaro motion sensor if the _illuminance_ is below _10_ to check if it's dark and the light need to be turned back on:

```lua
if fibaro:getGlobalValue("HomeState") == "away" and noMovement == 1 then
  fibaro:setGlobal("HomeState", "athome")
  fibaro:debug(os.date("%a, %b %d") .. " Set HomeState to \"athome\".")

  local currentLux = fibaro:getValue(160, "value")
  if ( tonumber(currentLux) < 10 ) then
    fibaro:debug(os.date("%a, %b %d") .. " Welcome home! Illuminance measuring " .. currentLux .. " lx, turn lights back on!")
...
```

If the illuminance is below _10 lux_ the Home Center 2 needs to know which lights to turn back on. I read the `SleepingLights` global variable and put the id's into a table:

```lua
local sleepinglights = fibaro:getGlobalValue("SleepingLights")
local lights = {}
for match in (sleepinglights..'|'):gmatch("(.-)"..'|') do
  table.insert(lights, match);
end
```

Then I check the elapsed time between leaving and arriving back home. If it is _less than 4 hours_ **and** there where _lights left on_, turn them back on:

```lua
local currentime =  os.time(os.date("!*t"))
local sleepingtime = lights[1]
local elapsedtime = currentime - sleepingtime

if elapsedtime < 14400 and lights[2] ~= nill then -- if elapsed time is less than 4 hours.
fibaro:debug(os.date("%a, %b %d") .. " Last member of the family left less than 4 hours ago, return lights to previous state!")
for k, v in pairs(lights) do
  if k ~= 1 then -- skip first, is unixtimestamp
    fibaro:call(v , "turnOn")
    fibaro:debug("turn on: " .. v)
  end
else
...
```

If the elapsed time between leaving and arriving back home is more then _4 hours_ a _standard arrival routine_ will run, because the previous light state is not important anymore. This routine you write in the `else` statement of the previous `if` condition:

```lua
...
else
  fibaro:debug(os.date("%a, %b %d") .. " Last member of the family left more than 4 hours ago, start arrive home scene!")

  fibaro:call(49, "setValue", "8") -- Spots garderobe
  fibaro:call(172, "turnOn") -- Gurken garden

  fibaro:call(140, "changeHue", "5021") -- Ledstip overloop
  fibaro:call(140, "changeSaturation", "199") -- Ledstip overloop
  fibaro:call(140, "changeBrightness", "114") -- Ledstip overloop
  fibaro:call(140, "turnOn") -- Ledstip overloop

  fibaro:call(44, "setValue", "8") -- Spots keuken
  fibaro:call(39, "setValue", "35") -- Kookeiland
end
```

## More awesome usecases

Because I made the _TurnOffAllLights_ universal and supports exclusions you can make cool other scenes using it like a _Go to Bed_ scene trigged by a _scene activition_. Also the _arrive home_ scene can be edited to automatically create a nice cup of coffee when arriving home from a long day.

I'll describe these cool features soon as I'm implementing these at the moment.

## Download my scenes complete LUA code

You can download the full LUA scene code from here:

* Scene 1: [TurnAllLightsOff.lua](https://github.com/joepv/fibaro/blob/master/TurnAllLightsOff_Public.lua)
* Scene 2: [LeaveHome.lua](https://github.com/joepv/fibaro/blob/master/LeaveHome_Public.lua)
* Scene 3: [ArriveHome.lua](https://github.com/joepv/fibaro/blob/master/ArriveHome_Public.lua)

**You have to change the _device id's_ from my motion sensors in this scene to your own id's!**