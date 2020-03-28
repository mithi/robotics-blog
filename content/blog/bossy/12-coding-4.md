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

``` arduino

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

```arduino
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
}
```

I've created a header file with structs to be able to do this smoothly

```arduino

#ifndef MatrixHelpers_h
#define MatrixHelpers_h

#include "BossyConstants.h"

/**********************
  LED MATRIX DISPLAY HELPERS
*************************/
// these structs are used for displaying state changes in the led matrix
// the pixels are the locations in the matrix that would change
// depending on the state of the joystick
#define Xi 0
#define Yi 1

#define NUMBER_OF_STICKS 8

struct Stick {
  uint8_t id;
  uint8_t first_pixel[2];
  uint8_t second_pixel[2];
};

const struct Stick jxupleft = {LEFT_JOYSTICK_UPPER_X, {1, 2}, {3, 2}};
const struct Stick jyupleft = {LEFT_JOYSTICK_UPPER_Y, {2, 3}, {2, 1}};

const struct Stick jxdownleft = {LEFT_JOYSTICK_LOWER_X, {1, 5}, {3, 5}};
const struct Stick jydownleft = {LEFT_JOYSTICK_LOWER_Y, {2, 6}, {2, 4}};

const struct Stick jxupright = {RIGHT_JOYSTICK_UPPER_X, {4, 2}, {6, 2}};
const struct Stick jyupright = {RIGHT_JOYSTICK_UPPER_Y, {5, 3}, {5, 1}};

const struct Stick jxdownright = {RIGHT_JOYSTICK_LOWER_X, {4, 5}, {6, 5}};
const struct Stick jydownright = {RIGHT_JOYSTICK_LOWER_Y, {5, 6}, {5, 4}};

const struct Stick sticks[NUMBER_OF_STICKS] = {
  jxupleft,
  jyupleft,
  jxdownleft,
  jydownleft,

  jxupright,
  jyupright,
  jxdownright,
  jydownright
};


#define NUMBER_OF_BUTTONS 8

struct Button {
  uint8_t id;
  uint8_t pixel[2];
};

const struct Button bupleft = {LEFT_BUTTON_UPPER, {0, 0}};
const struct Button bdownleft = {LEFT_BUTTON_LOWER, {0, 7}};
const struct Button bupright = {RIGHT_BUTTON_UPPER, {7 , 0}};
const struct Button bdownright = {RIGHT_BUTTON_LOWER, {7 , 7}};

// joystick buttons
const struct Button jupleft = {LEFT_JOYSTICK_UPPER_BUTTON, {2 , 2}};
const struct Button jdownleft = {LEFT_JOYSTICK_LOWER_BUTTON, {2 , 5}};
const struct Button jupright = {RIGHT_JOYSTICK_UPPER_BUTTON, {5 , 2}};
const struct Button jdownright = {RIGHT_JOYSTICK_LOWER_BUTTON, {5 , 5}};

const struct Button buttons[NUMBER_OF_BUTTONS] = {
  bupleft,
  bdownleft,
  bupright,
  bdownright,

  jupleft,
  jdownleft,
  jupright,
  jdownright,
};

#define NUMBER_OF_SWITCHES 4

struct SwitchSPDT {
  uint8_t id;
  uint8_t first_pixel[2];
  uint8_t second_pixel[2];
  uint8_t third_pixel[2];
};

const struct SwitchSPDT swupleft {LEFT_SWITCH_UPPER, {1, 0}, {2, 0}, {3, 0}};
const struct SwitchSPDT swdownleft {LEFT_SWITCH_LOWER, {1, 7}, {2, 7}, {3, 7}};
const struct SwitchSPDT swupright {RIGHT_SWITCH_UPPER, {4, 0}, {5, 0}, {6, 0}};
const struct SwitchSPDT swdownright {RIGHT_SWITCH_LOWER, {4, 7}, {5, 7}, {6, 7}};

const struct SwitchSPDT switches[NUMBER_OF_SWITCHES] = {swupleft, swdownleft, swupright, swdownright};

#define NUMBER_OF_POTENTIOMETERS 5
const uint8_t potentiometers[NUMBER_OF_POTENTIOMETERS] = {POT_LEFT, POT_LEFT_CENTER, POT_CENTER, POT_RIGHT_CENTER, POT_RIGHT};

#endif
```
