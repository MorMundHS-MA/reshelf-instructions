---
title:  "Building a shelf with magnetic locks"
date:   2019-06-18 20:20:23 +0200
layout: post
categories: [build instructions, shelf]
tags: [build instructions]
excerpt_separator: <!--more-->
---

Instructions for building the prototype version of the reshelf

<!--more-->

## Reshelf prototype

![Finished Shelf](finishedShelf.jpg)  

### Wood working 101
![coated chipboard](wood.jpg)  
The reshelf prototype was built using coated chipboard and was joined using [biscuit joiners](https://en.wikipedia.org/wiki/Biscuit_joiner), as well as some screws where necessary. The shelf has a total of 16 spaces, eight regular sized and eight half-sized, for storing items. On the top, behind the logo, the shelf contains room for the electronics for operating the locks. The doors can be made of any material provided it is sturdy enough, otherwise the locks might not open reliably. We chose colored acrylic glass because it looks neat and could easily be cut in a laser-cutter. The doors are attached using generic hinge strips from the local hardware store. To push the door open when the lock retracts, a small feather is mounted inside.

### Unlocking potential

The door lock use [Lock-style Solenoids](https://www.adafruit.com/product/1512) which basically is an electromagnet connected to a slanted bolt. These magnets require a voltage of 12V and draw 0.6 Ampere of current. This high amount of power, compared to the Raspberry Pi's 3.3V GPIO pins, requires some special circuitry. The 12V are provided by a power supply and the magnets are controlled by connecting ground to the drain of an [IRF7103 MOSFET](https://www.infineon.com/cms/de/product/power/mosfet/20v-300v-n-channel-power-mosfet/40v-75v-n-channel-power-mosfet/irf7103/). When the MOSFET's gate pin is connected to ground no current will flow to the magnets. However when a voltage of 5V (12V would be optimal) is applied to the gate, current is allowed to flow through the magnet causing it to retract. It should be noted that neither the magnets nor the controller board are designed to constantly open a magnet. However for opening a door a small pulse is sufficient.  
Due to the limited amount of GPIO pins available on the Raspberry Pi and their low voltage of 3.3V we additionally used a [5V shift register](http://www.ti.com/lit/ds/symlink/sn74hc595.pdf) to extend the number of possible doors to 24. Additionally to drive the shift register a [level shifter](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=16&ved=2ahUKEwiD1-TIq_biAhXKDuwKHXO1DdgQFjAPegQIBxAC&url=https%3A%2F%2Fwww.st.com%2Fresource%2Fen%2Fdatasheet%2Fm54hc27.pdf&usg=AOvVaw1h5-sy9XQZj8ys-TOlBKai) was used to upshifted the 3.3V to 5V.  
The original design also included [GPIO expanders](http://ww1.microchip.com/downloads/en/DeviceDoc/MCP23008-MCP23S08-Data-Sheet-20001919F.pdf) which would have been used to detect if the door was closed properly using buttons.  
The design was also extended by adding a relay to the 12V line connected to the magnets. This was necessary as the state of the shift registers is undefined on start up this would cause doors to open unintentionally when the shelf was powered on. With this revision the relay was off until explicitly enable by the Raspberry Pi.
