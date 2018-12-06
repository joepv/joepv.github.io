---
key: 2
title: Presence detection with Unifi AP
permalink: /presence/
excerpt: Presence detection with Unifi AP and Fibaro Home Center 2 (interfaced with Node-RED)
image: out-of-home-off.jpg
background-image: out-of-home-off.jpg
---

# Presence detection with Unifi AP and Fibaro Home Center 2 (interfaced with Node-RED)

## Goals

* Reliable presence detection.
* Get smartphone presence from Unifi controller.
* Use smartphone presence in Fibaro Home Center 2 LUA scenes.

To my experience is presence detection with use of geofencing unreliable. Therefore I searched for an other rock solid solution to check the presence of our smartphone's. I started with a ping to the IPv4 address of the phone, but power saving disabled Wi-Fi after 15 minutes and all lights in our home turned off while sitting in the living room.

I found some projects reading the Unifi controller online devices list via the Unifi API for reliable presence detection in other domotica controllers, but the Fibaro Home Center 2 can't connect to a _https_ site with a self signed certificate (_update, this is fixed in version 4.520_). So I installed the Node-RED Unifi node and with some JavaScript I update a global variable via de Fibaro REST-API with our presence state. With this global variable set I can use our presence in other graphic blocks and LUA scenes.

## TL;DR

* Get smartphone online status with Unifi node in Node-RED.
* Update on/off-line status in Home Center 2 global variable with Fibaro REST-API.
* Use global variable in graphic blocks and LUA scenes.

## How I implemented it

_Still writing... come back soon for the complete article..._