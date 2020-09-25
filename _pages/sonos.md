---
key: 10
title: Let Sonos wake you up!
product: Fibaro Home Center 2 and Sonos
permalink: /sonos/
excerpt: Wake-up with your favorite music and soft home lights
image: sonos.jpg
background-image: sonos2.jpg
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

I found out that the Sonos API is using SOAP messages. It was a real struggle as the Fibaro Home Center 2 doesn't support XML and SOAP, but I got it to work by parsing the XML with regular expressions. After posting my work on the [official Fibaro forums](https://forum.fibaro.com/) user [@10der](https://forum.fibaro.com/profile/11270-10der/) wrote XML parsing code in LUA! 

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

When the Sonos API answers the Fibaro request the `getAlarms()` function is called. In this function the XML SOAP resonse is parsed to extract the values and alarm times programmed in the Sonos System.

```lua
function getAlarms(xml)
    local dom = parseXml(xml)
    local results = getXmlPath(dom, "s:Envelope", "s:Body", "u:ListAlarmsResponse", "CurrentAlarmList")

    for _, result in ipairs(results) do
        -- Sonos store alarm list as XML in XML
        local alarms = getXmlPath(parseXml(result[1].text), "Alarms", "Alarm")
        for _, alarm in ipairs(alarms) do
            for k, v in pairs(alarm.attribute) do
                print(k, v)
            end
            print("-------------------------------------")
        end
    end
end
```

### Run your wake-up scene at alarm time

With the previous code all set you can put the alarm times in a variable and check if there are any alarms set for today:

```lua
if tonumber(alarmEnabled) == 1 and alarmRecurrence:match(dayofweek) then
  fibaro:debug('alarm enabled at: ' .. alarmTime)
end
```

With this example code you can create your own LUA scene to run a wake-up routine at the alarm time if set for today. If you want inspiration how to do that, please read my [advanced wake-up article](https://docs.joepverhaeg.nl/wakeup/) I wrote in 2018.

## Download my LUA template

You can download the full LUA code from this article here:

* [LUA scene code](https://github.com/joepv/fibaro/blob/master/SonosAlarm.lua)