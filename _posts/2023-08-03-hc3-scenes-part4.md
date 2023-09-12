---
layout: post
title:  "HC3 Lua scenes part 4: setTimeOut vs Sleep explained"
description: "A blog series about learning to write Lua scenes for FIBARO Home Center 3"
author: joep
categories: [ Home Center 3 ]
tags: [ Scene ]
image: assets/images/hc3-scenes4.jpg
beforetoc: "In this blog I explain the difference of the setTimeOut and Sleep functions that you can use in Lua scenes."
toc: true
---

The `hub.setTimeOut()` function has many uses. It can be used to allow a task to wait a fixed time before performing an action. Like polling every minute for new data. The `hub.sleep()` means that the current execution is paused (blocked) for a fixed time and literally blocks the current scene from running.

## How the hub.sleep() function works

The Home Center 3 has a function named `hub.sleep()` to pause execution for a fixed amount of time. This function is runs synchronous and stops the whole Lua scene for the amount of time specified. This includes all events in the scene thread and new events will not be processed until after the `hub.sleep()`function is finished. In short, your scene stalls.

For example you want to **turn on** a light and **turn it off after 15 minutes**. You can program this with the following Lua scene:

```lua
hub.call(123, "turnOn")
hub.sleep(900000) -- 15 minutes in milliseconds
hub.call(123, "turnOff")
hub.debug("Scene 25", "This will be shown after the light is turned off!"
```

![hc3-scenes4-01.jpg](../assets/images/hc3-scenes4-01.jpg)
<sub>Photo by <a href="https://unsplash.com/@zanilic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Zan</a> on <a href="https://unsplash.com/photos/0WzeC6JtbHU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a></sub>

## How the hub.setTimeOut() function works

To solve the problem with the scene stalling you can use the `hub.setTimeOut()` function, that runs asynchronous. When you execute a Lua function asynchronously, the scene continues to run before the asynchronous function finishes. Asynchronous functions can be complicated because of the semantics of how things tie together when you run them at the same time.

To rewrite your scene you can use the following Lua code:

```lua
hub.call(123, "turnOn")

hub.setTimeout(900000, function() -- 15 minutes in milliseconds
	hub.call(123, "turnOff")
end)

hub.debug("Scene 25", "This will be shown before the light is turned off!"
```

This scene continues to run before the asynchronous function finishes. You can see that is runs asynchronous with the `hub.debug()` output. In my first example the line appears after the light has turned off and in the second example it appears before the light turns off.

## When does the sleep function becomes an issue?

With a function loop in a scene that runs every minute, you can run into problems when using the time functions in Lua. Then it can happen that by pausing the Lua scene (with the `hub.sleep()` function), in the next loop, the time has jumped just too far in the future and you get timing issues due time drift.

## Counter time drift with hub.setTimeOut() in a Quick App

If you create a Quick App for your HC3 that polls an API every minute, then you have to watch out for time drift and I recommend always using the `hub.setTimeOut()` function.

You can correct the system clock drift with the following Lua code:

```lua
function QuickApp:refresh()
	-- do your refresh API stuff here!

	local timeout = 60000 - (os.date("%S") * 1000)
	hub.setTimeout(timeout, function() -- wait 1 minute
		self:refresh()
	end)
end
```

The `60000 - (os.date("%S") * 1000)` line calculates that the timeout is always exactly to the full minute. If you use this code in a loop you will not suffer from time drift.


## Previous part: sourceTrigger explained

[In the previous module](https://docs.joepverhaeg.nl/hc3-scenes-part3/), I'll learn you about the special variable `sourceTrigger` that is available in the HC3 and how you use it Lua scenes.

## Next part: How to do a simple HTTP request

[In the next module](https://docs.joepverhaeg.nl/hc3-scenes-part5/), I'll learn you how you write a simple HTTP request in a Lua scene.