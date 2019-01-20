---
key: 4
title: Wake-up with a fresh morning coffee
product: Google Calendar, Node-RED and Fibaro Home Center 2
permalink: /coffee/
excerpt: Brewed by Google Calendar, Node-RED and Fibaro Home Center 2
image: coffee.jpg
background-image: coffee.jpg
---

# Wake-up with a fresh morning coffee

January 20, 2018 (_**draft**_)  
_**Applies to:** Fibaro Home Center 2, Node-RED and Google Calendar._

## Goals

* Check in Google Calendar if coffee is desirable _next_ morning.
* Change [wake-up routine](/wakeup/) to activate the coffee machine if needed.

When I have to get up (very) early for a long ride to a client I make myself a cup of coffee in the morning. I thought why not automate this process by reading my calendar and telling the [wake-up routine](/wakeup/) to check if coffee is desirable in the morning!

## How I implemented it

### In words

With the Google Calendar node for Node-RED I _poll_ every everning at `21:15` my work calendar in Google. If there is a appointment with a coffee cup emoticon in it Node-RED checks if this appointment is _tomorrow_. If this is true the _global variable_ `Make Coffee` is set to `yes` in the Fibaro Home Center 2 via de Fibaro REST-API.

### Devices used
To automate my [Technivorm Moccamaster](https://www.moccamaster.com) coffee machine I use a [Fibaro Wall Plug](https://www.fibaro.com/en/products/wall-plug/).

### Node-RED part

#### Install the node for Node-RED to connect to Google Calendar:

`$ npm install node-red-node-google`

#### Import the following flow in Node-RED

```
<empty>
```

#### Edit the flow in Node-RED

Change all names and variables to your own in Node-RED and Home Center 2 variables!

### Fibaro part

#### Global variables

This is the global variable you need for the routine to work:

| Global Variable   | Type       | Value        |
| ----------------- | ---------- | ------------ |
| MakeCoffee        | Predefined | yes, no      |

_**This is a draft, come back soon!**_