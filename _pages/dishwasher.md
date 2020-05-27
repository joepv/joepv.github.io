---
key: 9
title: Smart Energy Management
product: Bosch Home Connect, Node-RED and Fibaro Home Center 2
permalink: /dishwasher/
excerpt: Wash your dishes with power from your solar panels!
image: perfectdry.jpg
background-image: perfectdry.jpg
---

# Smart Energy with Solar Panels and a Bosch Home Connected dishwasher<!-- omit in toc -->

May 18, 2020   
_**Applies to:** Node-RED, DSMR Reader, Bosch Home Connect and Fibaro Home Center 2_

## Table of Contents<!-- omit in toc -->

- [Goal](#goal)
- [TL;DR](#tldr)
- [Why I chose Node-RED](#why-i-chose-node-red)
- [Prerequisites](#prerequisites)
- [Node-RED implementation](#node-red-implementation)
  - [Bosch Home Connect](#bosch-home-connect)
    - [Installation](#installation)
    - [Configuration](#configuration)
  - [Functions I wrote](#functions-i-wrote)
    - [Polling State](#polling-state)
    - [DSMR request](#dsmr-request)
    - [Energy return check](#energy-return-check)
  - [](#)

## Goal

In the Netherlands there is a law that if you generate more sustainable energy via solar panels than you consume from your energy supplier when the sun is not shining, the energy that you have delivered back to the public network is deducted from this. The energy is stated in kWh and this deduction is called netting.

The government has decided to gradually phase out the netting in 2023. This makes it very important to use the energy of your solar panels as smartly as possible. That is why we are already trying to adapt as much as possible by using devices that require a lot of energy during the day.

One of these devices is our dishwasher made by Bosch. The dishwasher has the Home Connect function available, so why no use it to automate the start of the washing when there is more energy delivered from the solar panels than the other devices in our house at that moment need.

## TL;DR

* Read energy return from our Smart Meter.
* Check if the dishwasher is ready to do the dishes.
* Start the dishwasher program when the solar panels deliver more energy than the house consumes.
* Program it with as little API and network traffic as possible.

## Why I chose Node-RED

If you read my previous articles you know I use the Fibaro System as base of my domotica system. Why do I make this smart energy system with Node-RED and not with LUA in the Home Center?

That is because with the Home Center 2 I can not trigger actions based on events. You have to poll an API for new data, and I want the dishwasher virtual device to be instant updated. Also I don't want to poll API's to much because most public API's like Bosch Home Connect has limits requesting the API.

## Prerequisites

Before you can start with this awesome stuff you must have to following in place:

* Your dishwasher must be connected to [Bosch Home Connect](https://apps.apple.com/nl/app/home-connect-app/id901397789).
* Node-Red must be installed and configured with the [`node-contrib-home-connect`](https://www.npmjs.com/package/node-red-contrib-homeconnect) node.
* DSMR Reader must be installed and the API enabled in the configuration settings.

> **Note:** This article is based on a 3-phase power connection, find the correct phase where the dishwasher is connected to and write this down to use later.

## Node-RED implementation

### Bosch Home Connect

#### Installation

Node install configure info here

#### Configuration

How to get the HAIM, special option I did not know

### Functions I wrote

#### Polling State

#### DSMR request

#### Energy return check

### 