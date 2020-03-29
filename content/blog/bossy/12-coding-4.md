---
title: "🎮 Bossy Matrix: Feedback Test with LEDs"
date: 2020-03-28T01:43:49+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---

> View bossy's working code: 🎮 [here](https://github.com/mithi/bossy)

![](/robotics-blog/bossy-matrix-1.gif)

The next I needed to do is to provide feedback to the user that her input is indeed registered.
We have the LED matrix for this.

The code for this sketch when compiled in the Arduino Micro:

```
Sketch uses 12844 bytes (44%) of program storage space. Maximum is 28672 bytes.
Global variables use 780 bytes (30%) of dynamic memory, leaving 1780 bytes for local variables. Maximum is 2560 bytes.

```

With the Arduino Uno:

```
Sketch uses 9780 bytes (30%) of program storage space. Maximum is 32256 bytes.
Global variables use 640 bytes (31%) of dynamic memory, leaving 1408 bytes for local variables. Maximum is 2048 bytes.
```

The sketch is pretty simple, the Microcontroller listens for any change of state given the 25
possible sources of inputs (5 potentiometer, 4 3-DOF joysticks, 4 momentary switches, 4 spdt switches),
and provides feedback that such signals are indeed received by turning specific pixels of the 8x8 LED Matrix into a specific color.

Since 64 pixels is not that many, we can toggle between two modes (using the upper left switch)

One of the modes show the joysticks signals while the other shows the potentiometer signals.
Both modes show current state of the 4 momentary switches (buttons) and 4 spdt switches.


|![](/robotics-blog/bossy-matrix-2.gif)|![](/robotics-blog/bossy-matrix-3.gif)|
| ---------- | ---------- |

The loop contains code like this, which pretty much iterates through each input
check if the state of that input has changed, and then proceeds to update the LED Matrix appropriately.

```c++

  for (int i = 0; i < NUMBER_OF_BUTTONS; i++) {
    if (bossy.hasChangedState(buttons[i].id)) {
      update_button_display(buttons[i]);
    }
  }

  for (int i = 0; i < NUMBER_OF_SWITCHES; i++) {
    if (bossy.hasChangedState(switches[i].id)) {
      update_switch_display(switches[i]);
    }
  }

  for (int i = 0; i < NUMBER_OF_STICKS; i++) {
    if (bossy.hasChangedState(sticks[i].id)) {
      update_stick_display(sticks[i]);
    }
  }

  for (int i = 0; i < NUMBER_OF_POTENTIOMETERS; i++) {
    if (bossy.hasChangedState(potentiometers[i])) {
      update_pot_display(i, potentiometers[i]);
    }
  }

```

Updating the display given current state of the input could look something like this

```c++
const uint8_t button_colors[NUMBER_OF_STATES] =  {LED_GREEN, LED_GREEN, LED_GREEN, LED_GREEN, LED_RED};

void update_button_display(const struct Button &button) {
  uint8_t state = bossy.savedState(button.id);
  uint8_t color = button_colors[state];
  uint8_t x = button.pixel[Xi];
  uint8_t y = button.pixel[Yi];
  matrix.drawPixel(x, y, color);

  matrix.writeDisplay();
}
matrix.writeDisplay();

```

I've created a header file with structs to be able to do this smoothly

```c++
#ifndef BossyInputSets_h
#define BossyInputSets_h
#include "BossyConstants.h"

namespace BossyInputSets {

  const uint8_t stick_ids[BossyConstants::NUMBER_OF_STICKS] = {
    BossyConstants::LEFT_JOYSTICK_UPPER_X,
    BossyConstants::LEFT_JOYSTICK_UPPER_Y,
    BossyConstants::LEFT_JOYSTICK_LOWER_X,
    BossyConstants::LEFT_JOYSTICK_LOWER_Y,

    BossyConstants::RIGHT_JOYSTICK_UPPER_X,
    BossyConstants::RIGHT_JOYSTICK_UPPER_Y,
    BossyConstants::RIGHT_JOYSTICK_LOWER_X,
    BossyConstants::RIGHT_JOYSTICK_LOWER_Y
  };

  /*****************************
  * Buttons
  ******************************/
  const uint8_t button_ids[BossyConstants::NUMBER_OF_BUTTONS] = {
    BossyConstants::LEFT_BUTTON_UPPER,
    BossyConstants::LEFT_BUTTON_LOWER,
    BossyConstants::RIGHT_BUTTON_UPPER,
    BossyConstants::RIGHT_BUTTON_LOWER,

    BossyConstants::LEFT_JOYSTICK_UPPER_BUTTON,
    BossyConstants::LEFT_JOYSTICK_LOWER_BUTTON,
    BossyConstants::RIGHT_JOYSTICK_UPPER_BUTTON,
    BossyConstants::RIGHT_JOYSTICK_LOWER_BUTTON
  };

  /*****************************
  * Switches
  ******************************/
  const uint8_t switch_ids[BossyConstants::NUMBER_OF_SWITCHES] = {
    BossyConstants::LEFT_SWITCH_UPPER,
    BossyConstants::LEFT_SWITCH_LOWER,
    BossyConstants::RIGHT_SWITCH_UPPER,
    BossyConstants::RIGHT_SWITCH_LOWER
  };

  /*****************************
  * Potentiometers
  ******************************/
  const uint8_t potentiometer_ids[BossyConstants::NUMBER_OF_POTENTIOMETERS] = {
    BossyConstants::POT_LEFT,
    BossyConstants::POT_LEFT_CENTER,
    BossyConstants::POT_CENTER,
    BossyConstants::POT_RIGHT_CENTER,
    BossyConstants::POT_RIGHT
  };
}

#endif
```
