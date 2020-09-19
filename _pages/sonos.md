---
key: 10
title: Let Sonos wake you up!
product: Fibaro Home Center 2 and Sonos
permalink: /sonos/
excerpt: Wake-up with your favorite music and soft home lights
image: sonos.jpg
background-image: sonos.jpg
---

# Smart wake-up with Sonos controlling your Fibaro enabled lights<!-- omit in toc -->

September 20, 2020   
_**Applies to:** Fibaro Home Center 2, Sonos_

## Table of Contents<!-- omit in toc -->
- [Goal](#goal)
- [TL;DR](#tldr)
- [Why write new LUA code? There are plug-ins available!](#why-write-new-lua-code-there-are-plug-ins-available)
- [How I implemented it](#how-i-implemented-it)
  - [SOAP request with LUA](#soap-request-with-lua)
  - [Parsing the SOAP response](#parsing-the-soap-response)
  - [Run your wake-up scene at alarm time](#run-your-wake-up-scene-at-alarm-time)
- [Download my LUA template](#download-my-lua-template)

## Goal

Almost two years ago I wrote an article about my [advanced wake-up routine](https://docs.joepverhaeg.nl/wakeup/). In this Fibaro Home Center 2 LUA scene I use the Philips Hue API to retrieve the wake-up routine from the Hue bridge to turn on a couple of lights and the coffee machine in the morning.

In this article I do the same, but I read the alarms set in my Sonos System with the Sonos SOAP API. Yes, Sonos still uses SOAP!

## TL;DR

* Set recurring alarm in the Sonos app.
* A Fibaro Home Center 2 LUA scene reads all alarms.
* If an alarm is set for today run a wake-up scene.

## Why write new LUA code? There are plug-ins available!

Home Center 2 has an official Sonos plug-in, but this only enables a virtual device to press start, stop and go to the next song. On the official Fibaro forums and marketplace there are thirdparty Sonos plug-ins but they don't have the ability to read the alarm settings.

## How I implemented it

I found out that the Sonos API is using SOAP messages. It was a real struggle as the Fibaro Home Center 2 doesn't support XML and SOAP, but I got it to work by parsing the XML with regular expressions.

### SOAP request with LUA

At first I contruct a SOAP message to use with by Fibaro System. The SOAP protocol needs an envelope element that identifies the XML document as a SOAP message. The following message body is send to the Sonos System to retrieve all alarms:

```xml
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/" s:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
  <s:Body>
    <u:ListAlarms xmlns:u="urn:schemas-upnp-org:service:AlarmClock:1">
    <InstanceID>0</InstanceID>
    </u:ListAlarms>
  </s:Body>
</s:Envelope>
```

Also a `SOAPAction` HTTP header field is needed within the HTTP `POST` request: 

```xml
urn:schemas-upnp-org:service:AlarmClock:1#ListAlarms
```

Now you can make a SOAP request to the path: `AlarmClock/Control` with HTTP. In Fibaro LUA code the code looks like:

```lua
local body   = '<s:Envelope xmlns:s=\"http://schemas.xmlsoap.org/soap/envelope/\" s:encodingStyle=\"http://schemas.xmlsoap.org/soap/encoding/\"><s:Body><u:ListAlarms xmlns:u=\"urn:schemas-upnp-org:service:AlarmClock:1\"><InstanceID>0</InstanceID></u:ListAlarms></s:Body></s:Envelope>'
local action = 'urn:schemas-upnp-org:service:AlarmClock:1#ListAlarms'
local path   = 'AlarmClock/Control'
local host   = '192.168.1.10:1400'

HC2 = net.HTTPClient()
HC2:request(
  'http://' .. host .. '/' .. path, {
    success = function(resp) getAlarms(resp.data) end,
    error = function(err)
              fibaro:debug(err.data)
            end,
    options = {
      headers = {
        ['Content-Type'] = 'text/xml',
        ['charset'] = 'UTF-8',
        ['Host'] = host,
        ['SOAPAction'] = action
      },
      data = body,
      method = 'POST'
    }
  }
)
```

> 💡 **Note:** You have to replace the IP address `192.168.1.10` on line 4 with the address your Sonos device. If you have multiple devices choose one. If your Sonos devices get the IP address from DHCP you have to reserve this address in your router.

### Parsing the SOAP response

When the Sonos API answers the Fibaro request the `getAlarms()` function is called. In this function I parse the XML SOAP resonse with regex. There are XML parse examples for the Fibaro System on the forums, but they all didn't work, because of the SOAP headers.

I know this is not the best way to do this (I you know a better way, please teach me), but the following code parses an alarm set on a Sonos device to a LUA variable:

```lua
-- find the first <Alarm ID... /> tag char number...
b = string.find(xml, "&lt;Alarm ID", e)
if b ~= nill then
  -- find the closing tag tag char number...
  e = string.find(xml, "/&gt;", b)
  -- substring the whole <Alarm .. /> tag...
  x = string.sub(xml, b, e+3)
  -- replace the html quote chars with real quotes for string.match
  x = x:gsub("&quot;", "\"")
  alarmId         = string.match(x, [[Alarm%sID="([^"]+)]])
  alarmTime       = string.match(x, [[StartTime="([^"]+)]])
  alarmRecurrence = string.match(x, [[Recurrence="([^"]+)]])
  alarmEnabled    = string.match(x, [[Enabled="([^"]+)]])
end
```

When I put this in a loop until all `<Alarm>` tags are read I have all alarms to work with in my morning automation.

Sonos uses the words `ONCE` and `WEEKDAYS` when an alarm is scheduled once and schedulles all weekdays. Else a day number is returned. To make checking if an alarm is for today I remap them to numbers:

```lua
local dayofweek = os.date("*t").wday-1
-- remap ONCE/WEEKDAYS to day numbers for check later on
if alarmRecurrence == 'ONCE' then
  alarmRecurrence = 'ONCE_' .. dayofweek
elseif alarmRecurrence == 'WEEKDAYS' then
  alarmRecurrence = 'WEEKDAYS_12345'
end
```

### Run your wake-up scene at alarm time

With the previous code all set you can check if there are any alarms set for today:

```lua
if tonumber(alarmEnabled) == 1 and alarmRecurrence:match(dayofweek) then
  fibaro:debug('alarm enabled at: ' .. alarmTime)
end
```

With this example code you can create your own LUA scene to run a wake-up routine at the alarm time if set for today. If you want inspiration how to do that, please read my [advanced wake-up article](https://docs.joepverhaeg.nl/wakeup/) I wrote in 2018.

## Download my LUA template

You can download the full LUA code from this article here:

* [LUA scene code](https://github.com/joepv/fibaro/blob/master/SonosAlarm.lua)