---
title: "🎮 Bossy Inputs: Modifiable Constants"
date: 2020-03-27T00:36:52+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---


> View bossy's working code: 🎮 [here](https://github.com/mithi/bossy)


## IMPORTANT QUICK SIDETRACK: Debouncing Buttons
Before continuing with the code discussion, I just want to put this here:
As I've mentioned in previous blog posts, I realized that there is no built-in pullup or pulldown resistor for
the joystick select buttons. So when it is not pressed it's just dangling in the air.

I hacked away this problem through software through temporary methods
`isBrokenButton()` and `_updateBrokenButton()` methods. This is just a quick fix for now.


I read about different ways to debouncing buttons and right now I just implemented the easiest way (delays)

- December 9, 2015 | Hackaday: Elliot Williams [Embed With Elliot: Debounce Your Noisy Buttons](https://hackaday.com/2015/12/09/embed-with-elliot-debounce-your-noisy-buttons-part-i/)

# Back to regular programming
Now that's out of the way. Let's go examine the [`BossyConstant.h`](https://github.com/mithi/bossy/blob/master/src/BossyConstants.h)
If you want to make modifications based on your specific setup, you can fiddle around the values here.


The first few lines is where you'd define the PIN signals

```c++
  const uint8_t PIN_ENABLE_LEFT_MUX = 11;
  const uint8_t PIN_ENABLE_RIGHT_MUX = 12;

  const uint8_t s0 = 5;
  const uint8_t s1 = 6;
  const uint8_t s2 = 7;
  const uint8_t s3 = 8;
  const uint8_t PIN_SIGNAL = A1;
```

Skipping a few lines, you see how the `input_ids` are named,
which are just numbers from  0 to 24.

```c++
  const uint8_t LEFT_SWITCH_UPPER = 0;
  const uint8_t LEFT_SWITCH_LOWER = 1;

  const uint8_t LEFT_JOYSTICK_UPPER_Y = 2;
  const uint8_t LEFT_JOYSTICK_UPPER_X = 3;
  const uint8_t LEFT_JOYSTICK_UPPER_BUTTON = 4;

  const uint8_t LEFT_JOYSTICK_LOWER_Y = 5;
  const uint8_t LEFT_JOYSTICK_LOWER_X = 6;
  const uint8_t LEFT_JOYSTICK_LOWER_BUTTON = 7;

  const uint8_t LEFT_BUTTON_UPPER = 8;
  const uint8_t LEFT_BUTTON_LOWER = 9;

  const uint8_t POT_LEFT = 10;
  const uint8_t POT_LEFT_CENTER = 11;
  const uint8_t POT_CENTER = 12;

  const uint8_t RIGHT_SWITCH_UPPER = 13;
  const uint8_t RIGHT_SWITCH_LOWER = 14;

  const uint8_t RIGHT_JOYSTICK_UPPER_Y = 15;
  const uint8_t RIGHT_JOYSTICK_UPPER_X = 16;
  const uint8_t RIGHT_JOYSTICK_UPPER_BUTTON = 17;

  const uint8_t RIGHT_JOYSTICK_LOWER_Y = 18;
  const uint8_t RIGHT_JOYSTICK_LOWER_X = 19;
  const uint8_t RIGHT_JOYSTICK_LOWER_BUTTON = 20;

  const uint8_t RIGHT_BUTTON_UPPER = 21;
  const uint8_t RIGHT_BUTTON_LOWER = 22;

  const uint8_t POT_RIGHT_CENTER = 23;
  const uint8_t POT_RIGHT = 24;
```

Going back up, we see an array
that maps the `input_id` to the corresponding
channel of respective multiplexer

```c++

  const uint8_t NUMBER_OF_CHANNELS = 25;
  const uint8_t NUMBER_OF_SWITCHES = 4;
  const uint8_t NUMBER_OF_BUTTONS = 8;
  const uint8_t NUMBER_OF_POTENTIOMETERS = 5;
  const uint8_t NUMBER_OF_STICKS = 8;
  const uint8_t NUMBER_OF_BROKEN_BUTTONS = 4;

  // map of input id to channel in the multiplexer
  const uint8_t INPUT_CHANNEL[NUMBER_OF_CHANNELS] = {

    11, // LEFT_SWITCH_UPPER
    10, // LEFT_SWITCH_LOWER

     8, // LEFT_JOYSTICK_UPPER_Y
     7, // LEFT_JOYSTICK_UPPER_X
     9, // LEFT_JOYSTICK_UPPER_BUTTON

     5, // LEFT_JOYSTICK_LOWER_Y
     6, // LEFT_JOYSTICK_LOWER_X
     4, // LEFT_JOYSTICK_LOWER_BUTTON

     0, // LEFT_BUTTON_UPPER
     3, // LEFT_BUTTON_LOWER

    14, // POT_LEFT
    12, // POT_LEFT_CENTER
    13, // POT_CENTER

    4, // RIGHT_SWITCH_UPPER
    5, // RIGHT_SWITCH_LOWER

    7, // RIGHT_JOYSTICK_UPPER_Y
    6, // RIGHT_JOYSTICK_UPPER_X
    8, // RIGHT_JOYSTICK_UPPER_BUTTON

    11, // RIGHT_JOYSTICK_LOWER_Y
    9, // RIGHT_JOYSTICK_LOWER_X
    10, // RIGHT_JOYSTICK_LOWER_BUTTON

    13, // RIGHT_BUTTON_UPPER
    12, // RIGHT_BUTTON_LOWER

     3, // POT_RIGHT_CENTER
     2  // POT_RIGHT
  };

```

Skipping a few lines again, we see which
inputs are connected to which multiplexer

```c++
  // the ids for the left multiplexer is 0 to 12
  // the ids for the right multiplexer is 13 to 24
  const uint8_t MUX_LEFT_MIN = 0;
  const uint8_t MUX_LEFT_MAX = 12;

  const uint8_t MULTIPLEXER_RIGHT_MIN = 13;
  const uint8_t MULTIPLEXER_RIGHT_MAX = 24;
```

We also specified the direction of states,
which is dependent on how the circuit is wired.


```c++
  /**********************
   INPUT TYPE (normal or inverted)
  *************************/
  // The input type is normal when:
  // button: pressed is HIGH
  // joystick y axis: UP is HIGH
  // joystick x axis: RIGHT is HIGH
  // switch: RIGHT is HIGH
  // it is inverted if otherwise
  const bool NORMAL = true;
  const bool INVERTED = false;

  // map of input id to its input type (
  const bool INPUT_TYPE[NUMBER_OF_CHANNELS] = {
    NORMAL, // LEFT_SWITCH_UPPER
    NORMAL, // LEFT_SWITCH_LOWER

    INVERTED, // LEFT_JOYSTICK_UPPER_Y
    INVERTED, // LEFT_JOYSTICK_UPPER_X
    INVERTED, // LEFT_JOYSTICK_UPPER_BUTTON

    INVERTED, // LEFT_JOYSTICK_LOWER_Y
    INVERTED, // LEFT_JOYSTICK_LOWER_X
    INVERTED, // LEFT_JOYSTICK_LOWER_BUTTON

    INVERTED, // LEFT_BUTTON_UPPER
    INVERTED, // LEFT_BUTTON_LOWER

    NORMAL, // POT_LEFT
    NORMAL, // POT_LEFT_CENTER
    NORMAL, // POT_CENTER

    INVERTED, // RIGHT_SWITCH_UPPER
    INVERTED, // RIGHT_SWITCH_LOWER

    NORMAL, // RIGHT_JOYSTICK_UPPER_Y
    NORMAL, // RIGHT_JOYSTICK_UPPER_X
    INVERTED, // RIGHT_JOYSTICK_UPPER_BUTTON

    NORMAL, // RIGHT_JOYSTICK_LOWER_Y
    NORMAL, // RIGHT_JOYSTICK_LOWER_X
    INVERTED, // RIGHT_JOYSTICK_LOWER_BUTTON

    NORMAL, // RIGHT_BUTTON_UPPER
    NORMAL, // RIGHT_BUTTON_LOWER

    NORMAL, // POT_RIGHT_CENTER
    NORMAL // POT_RIGHT
  };
```

I think those are the most important things to be aware of, in case
you want to do some modifications.
