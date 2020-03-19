---
title: "Bossy: Task List"
categories: [bossy]
tags: [bossy, custom controller, wireless controller]
date: 2020-03-16T15:44:53+08:00
draft: false
---

## Objective: Build a a wireless custom general-purpose controller project
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
    - [x] .Test 8x8 Led matrix on Arduino Micro
    - Check if the two arduino micro  can communicate

3. Soldered Protoboard test
    - [x] .Solder led matrix, potentiometers, joysticks
    - Solder buttons and switches
    - Test led matrix
    - Test multiplexer #1 with joysticks
    - Test multiplexer #2 with potentiometers, buttons, switches
    - Test wireless connection

4. Coding and finishing up
    - Add UI functionality for provide feedback of controls in the 8x8 led matrix (screen)
    - Make an enclosure to house our controller
    - Use controller to control the neopixels wirelessly
