# Advanced wake-up with Philips Hue and Fibaro Home Center 2

## Goals

* Use my Philips Hue led strips as a wake-up light.
* Use one app to schedule the whole home wake-up scene.
* Start the morning routine when walking downstairs (check motion).
* Turn on the lights only when it's dark (read lux).

In our bedroom we integrated a Philips Hue lightstrip in the ceiling and use this with the Philips Hue app as a wake-up light. It beautifully fakes a sunrise in our whole room. As we use the Hue app to set our wake-up _alarm_ I use this app to trigger the Home Center to run a wake-up routine for the rest of the house.

## How I implemented it

### In words
Reading Hue schedules from the bridge cannot be done with the Fibaro Hue plug-in. Therefore I wrote a LUA scene to read the Hue schedules from the Hue bridge and run the wake-up routine at the schedules wake-up time. I achieved this with 2 LUA scenes:

1. *Scene 1* _runs every minute_ and polls the Hue bridge schedules at 04:00. If a wake-up is scheduled for today write the wake-up times to a _global variable_. Every minute it checks if there is a wake-up planned by reading the same _global variable_ and if so it sets the _WakeUpReady_ global variable to _1_.
2. *Scene 2* _runs when motion detected_ by a Fibaro Motion Sensor. If it detects motion it checks if the global variable _WakeUpReady_ is set to _1_ and runs the wake-up routine.

### Code snippets explained

You can download the full LUA scenes at the bottom of this page. I only describe snippets of my code to make you understand what it does and show the challenges I ran into.

#### Recurring times are saved as a bitmask in the Hue bridge

The Hue API states:

```
W[bbb]/T[hh]:[mm]:[ss]
Every day of the week given by bbb at given time
```

You have to convert the bitmask to weekday's. So you can check if the alarm is set for _today_. The first step is to convert decimal to a binary. I did this with the folowing LUA function:

```lua
function bin(dec)
    local result = ""
    repeat
        local divres = dec / 2
        local int, frac = math.modf(divres)
        dec = int
        result = math.ceil(frac) .. result
    until dec == 0
    local StrNumber
    StrNumber = string.format(result, "s")
    local nbZero
    nbZero = 8 - string.len(StrNumber)
    local Sresult
    Sresult = string.rep("0", nbZero)..StrNumber
    return Sresult
end
```

