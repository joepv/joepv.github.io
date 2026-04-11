---
layout: post
title: "Homey Energy Management"
description: "Stop blowing fuses and start saving energy! Discover why the Homey Energy Dongle is nice dongle for EV charging, solar surplus, and monitoring appliance health."
author: joep
categories: [ Homey Pro, Homey Self-Hosted-Server ]
tags: [ Review ]
image: assets/images/homey-energy-dongle.webp
beforetoc: "Is your freezer dying or just hungry? I dive into the Homey Energy Dongle, P1 passthrough for your EV, and how to automate your home battery with smart flows."
toc: true
---

Let's cut straight to the point with the question everyone is asking: **Why choose the Homey Energy Dongle instead of the HomeWizard P1 Meter?**

## Native Integration with Homey

The Homey Energy Dongle has been specifically developed for the Homey ecosystem. Although the HomeWizard P1 meter works with Homey via the Homey API, the Homey dongle is a native extension of your Homey ecosystem.

I managed to get it configured and visible in the app in **less than a minute**. I connected it to my smart meter, opened the Homey app and done!

![homey-energy-iphone.webp](../assets/images/homey-energy-iphone.webp)

## P1 Passthrough for EV charging

The Homey Energy dongle has a built-in splitter for another P1 reader. Most smart meters only have one P1 port. If you have an EV charging station that uses a physical cable for load balancing, your P1 port is already occupied, so you'd need a cheap splitter that doesn't always work. The Homey Dongle solves this problem, because you can plug your car charger P1 interface into the dongle.

This ensures that you do not get an super high load on your electricity phases, which would cause your earth leakage circuit breaker to trip.

![homey-energy-dongle-pt.webp](../assets/images/homey-energy-dongle-pt.webp)

## Usage: Identify Power Hungry Devices

Most P1 dongles show you what energy enters (and leaves) your home, the real win happens when you find the individual big spenders.

Homey's Energy tab is a powerful insight, but it gives you the best insight when you measure at socket level.

Let me explain.

### Pro-Tip: A Wall Plug for Every Appliance

If you put a smart plug (like a Fibaro Wall Plug or HomeWizard Energy Socket) behind your fridge and freezer in your house you get insight in their power usage due:

1. **Visibility:** In the Homey Energy tab, you'll see exactly what percentage of your monthly bill goes to that old freezer in the shed.
2. **Savings Potential:** You'd be surprised how much power a 10-year-old fridge pulls from the grid compared to modern ones.
3. **Health Monitoring:** By monitoring the power draw, you can set up a Homey Flow to see if your appliance is failing. If your freezer suddenly pulls a constant 100W instead of cycling on and off, your thermostat might be broken or the seals are gone. Your Homey can notify you before your food defrosts!

## Smart Flows: Orchestrating the Data

To help a little bit with the energy transition here in the Netherlands, I've put together an example flow that turns your energy data into an automated action.

### Intelligently Charging Your Home Battery with Homey

Since I haven't added a physical home battery to my setup yet, I've built a demo flow to illustrate the logic behind smart charging of your home battery.

When your solar production exceeds your home's demand, Homey can initiate to charge your battery. This allows you to store that energy and feed it back to the grid only when it makes the most (financial) sense.

This flow is designed to give you a head start in making a flow for this:

![homey-energy-battery.webp](../assets/images/homey-energy-battery.webp)

#### Flow 1: The "Free Energy" Indicator

- **WHEN:** The power becomes less than 2000W.
- **THEN:** Set the LED ring of your Homey to green (or send a notification: *"The sun is providing! Time to start the heavy machinery."*).

### The Appliance Health Check (Freezer Malfunction Alert)

Another example is that most people only realize their freezer is dying when they see a puddle of water on the floor. By then, it's too late.

By tracking the power usage of your appliances, you can spot an issue early on. A healthy fridge or freezer should "pulse”, it draws power to cool down and then idles. If your Homey data shows a constant, flat line of high power consumption, it's a red flag that your appliance is struggling to keep its cool.

Let me show you how you can set up a health check flow to create an early warning system:

![homey-energy-fridge.webp](../assets/images/homey-energy-fridge.webp)

#### Flow 2: The Appliance Health Check (Maintenance Alert)

- **WHEN:** The power of the "Freezer Plug" has been above 50W for more than 4 hours.
- **AND:** (Optional) Door sensor is closed.
- **THEN:** Send a push notification: *"Warning: The freezer is running constantly. Check the seals or the thermostat!"*

## Conclusion

The Homey Energy Dongle removes the friction of third-party APIs and provides a nice tool for your energy management. By combining it with your wall plugs, you're totally in control.