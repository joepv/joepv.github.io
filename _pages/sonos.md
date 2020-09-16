---
key: 10
title: Let Sonos wake you up!
product: Fibaro Home Center 2 and Sonos
permalink: /sonos/
excerpt: Wake-up with your favorite music and soft home lights
image: perfectdry.jpg
background-image: perfectdry.jpg  Afbeelding: Unsplash @iskubor
---

# Smart wake-up with Sonos controlling your Fibaro enabled lights<!-- omit in toc -->

September 16, 2020   
_**Applies to:** Fibaro Home Center 2, Sonos_

## Table of Contents<!-- omit in toc -->
- [Goal](#goal)
- [TL;DR](#tldr)
- [How I implemented it](#how-i-implemented-it)
  - [Prerequisites](#prerequisites)
  - [Getting the LUA scene code](#getting-the-lua-scene-code)
  - [Create a new LUA scene and import the code](#create-a-new-lua-scene-and-import-the-code)

## Goal

Almost two years ago I wrote an article about my advanced wake-up routine. In this Fibaro Home Center 2 LUA scene I wrote back in the days I use the Philips Hue API to retrieve the wake-up routine from the Hue bridge to turn on a couple of lights and the coffee machine in the morning.

In this article I do the same, but I read the alarms set in my Sonos System with the Sonos SOAP API. Yes, Sonos still uses SOAP. It was a real struggle as the Fibaro Home Center 2 doesn't support XML and SOAP, but I got it to work!

## TL;DR

* Set recurring alarm in the Sonos app.
* A Fibaro Home Center 2 LUA scene reads all alarms.
* If an alarm is set for today run a wake-up scene.

## How I implemented it

I found out that the Sonos API is using SOAP messages. The Home Center 2 has an official Sonos plug-in, but this only enables a virtual device to press start, stop and go to the next song. A Fibaro forum member 

### Prerequisites

### Getting the LUA scene code

### Create a new LUA scene and import the code