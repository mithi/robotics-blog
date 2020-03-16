---
title: "Bossy: Todo List"
categories: [bossy]
tags: [bossy, custom controller, wireless controller]
date: 2020-03-16T15:44:53+08:00
draft: false
---

## Objective: A wireless custom controller project
- The custom controller's name is Bossy
- Build a custom controller with parts I already have at home


1. Preliminaries
    - [x] .List bill of materials
    - [x] .Install arduino
    - [x] .Install fritzing
    - [x] .Test Arduino micro is working
    - [x] .Make continuity tester with adafruit metro mini
    - [x] .Find out uniqe values of up, down and neutral for each joystick
    - [x] .Verify connectivity of switches, potentiometer, and momentary buttons
    - [x] .Finalize breadboard schematic, pcb design

2. Breadboard tests
    - Test neopixel stick with Arduino Micro
    - Test 8x8 Led matrix on Arduino Micro
    - Test Adafruit Feather and Neopixel Wing if working
    - Check if arduino micro and adafruit feather can communicate
    - Test multiplexer #1 with joysticks
    - Test multiplexer #2 with potentiometers, buttons, switches

3. Soldered Protoboard test
    - Test neopixel stick
    - Test led matrix
    - Test multiplexer #1 with joysticks
    - Test multiplexer #2 with potentiometers, buttons, switches
    - Test wireless connection

4. Coding and finishing up
    - Add UI functionality for provide feedback of controls in the 8x8 led matrix and neopixel stick (screen)
    - Make an enclosure to house our controller
    - Use controller to control the neopixels of the feather wirelessly

## Breadboard, Schematic, pcb design

Here's my testing setup
![](/breadboard.png)

After several iterations, I finally settled to one design (the last one)

|![](/1-design.jpg)|![](/2-design.jpg)|![](/3-design.jpg)|![](/4-design.jpg)|
| ---------- | ---------- | ---------- | ---------- |
|![](/5-design.jpg)|![](/6-design.jpg)|![](/7-design.jpg)|![](/final-design.jpg)|

Here's my pathetic attempt to design the schematics. After several hours of pain, i decided to give up. lol


![](/messy-schematic.png)


|![](/messy-pcb.png)|![](/final-design.jpg)|
| ---------- | ---------- | ---------- | ---------- |


