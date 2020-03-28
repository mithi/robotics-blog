---
title: "🎮 Bossy Wireless! "
date: 2020-03-28T22:46:30+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---

> View bossy's working code: 🎮 [here](https://github.com/mithi/bossy)


Finally I've managed to test detected changes form Bossy to another
microcontroller with a NRF24L01 module.

Everytime Bossy detects a change made by the user handling the controller
it updates the LED Matrix appropriately and sends a two-byte message.
One byte contains the input idea (a number between 0 and 24 since there's 25
input channel total) and the other byte contains either the state (a number
on a scale of 0 to 4) or a low resolution version of the raw reading (IE the
reading scaled to a number between 0 and 255).


I've added three additional sketches.

## Sketch 1: Bare Minimum Receiver Test Code

> # 🎮 [Simple Receiver Code](https://github.com/mithi/bossy/tree/master/examples/bossy_simple_receiver/bossy_simple_receiver.ino)

One of them is the test code to  be used by the receiving Arduino that prints
the two-byte message it picks up. This is the sample code to test if the receiver is successfully
  able to get transmitted message from the Bossy Controller

## Sketch 2: Bare minimum Transmitter Test Code

> # 🎮 [Simple Transmitter Code](https://github.com/mithi/bossy/blob/master/examples/bossy_simple_transmitter/bossy_simple_transmitter.ino)

This sketch communicates data wireless everytime Bossy detects a change. It also prints this data in the Serial monitor.
This sketch does NOT use the LED Matrix.

Arduino Micro:
```
Sketch uses 11230 bytes (39%) of program storage space. Maximum is 28672 bytes.
Global variables use 599 bytes (23%) of dynamic memory, leaving 1961 bytes for local variables. Maximum is 2560 bytes.

```
Arduino Uno:
```
Sketch uses 9142 bytes (28%) of program storage space. Maximum is 32256 bytes.
Global variables use 636 bytes (31%) of dynamic memory, leaving 1412 bytes for local variables. Maximum is 2048 bytes.

```

## Sketch 3. Full Bossy Wireless Controller Code Version 1.0.0

> # 🎮 [Full Code](https://github.com/mithi/bossy/blob/master/examples/bossy_matrix_transmitter/bossy_matrix_transmitter.ino)

Here's the full working code.
It sends two bytes at a time, the first byte contains the id of the input
while the second byte contains the current state or reading of that said input.
It does this everytime Bossy detects a change in any of the 25 sources of signals.
It also provides feedback to the user via the LED matrix at the center of the controller.

Upload the first sketch discussed in this post to the receiving microcontroller
to verify that Bossy is successfully transmitting data.


Arduino Micro:
```
Sketch uses 17624 bytes (61%) of program storage space. Maximum is 28672 bytes.
Global variables use 829 bytes (32%) of dynamic memory, leaving 1731 bytes for local variables. Maximum is 2560 bytes.
```

Arduino Uno
```
Sketch uses 15390 bytes (47%) of program storage space. Maximum is 32256 bytes.
Global variables use 864 bytes (42%) of dynamic memory, leaving 1184 bytes for local variables. Maximum is 2048 bytes.
```
