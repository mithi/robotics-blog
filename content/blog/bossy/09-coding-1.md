---
title: "🎮 Bossy Inputs: How to Use"
date: 2020-03-26T23:38:51+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---

> View bossy's working code: 🎮 [here](https://github.com/mithi/bossy)

I have managed to read a couple of articles on object-oriented arduino to help me brainstorm on
how I'd structure my code. There are many ways to go about it, but ultimately, here's
how I ended up doing it. I prioritized readability over taxonomy, so it might not be
the most decoubled code with a bunch of small classes, but for my purpose I believe this will do.

Here's some of the articles I've read:

### Object Oriented Arduino and Arduino Project Directory Structure
- Nov 18, 2019 | Arduino Github: [Arduno IDE 1.5 Library Specification](https://github.com/arduino/Arduino/wiki/Arduino-IDE-1.5%3a-Library-specification)
- Jul 22, 2018 | Stack Exchange: [Arduino IDE and subfolders](https://arduino.stackexchange.com/questions/54651/arduino-ide-and-subfolders)
- Arduino.cc: [Writing a Library for Arduino](https://www.arduino.cc/en/Hacking/libraryTutorial)
- Arduino.cc: [Arduino Style Guide for Writing Libraries](https://www.arduino.cc/en/Reference/APIStyleGuide)
- [Arduino the Object Oriented Way](http://paulmurraycbr.github.io/ArduinoTheOOWay.html)
- [Arduino Object Oriented Programming (OOP)](https://roboticsbackend.com/arduino-object-oriented-programming-oop/)

# Quickstart

Using this tiny library should be easy enough:

```c++

#include <Bossy.h>

Bossy bossy;

void setup() {
  Serial.println(9600);
}

void loop() {
  int reading = bossy.readValue(LEFT_JOYSTICK_UPPER_X);
  int state = bossy.readState(RIGHT_SWITCH_LOWER);

  // access the same reading and state by calling
  int saved_reading = bossy.savedReading(LEFT_JOYSTICK_UPPER_X);
  int saved_state = bossy.savedState(RIGHT_SWITCH_LOWER);

  // both will print true because it doesn't read a new value
  Serial.println(reading == saved_reading);
  Serial.println(state == saved_state);

  // maps the raw value from the range 0-1023 to the range 0-255
  int low_res_reading = bossy.savedReadingLowRes(POT_LEFT_CENTER);
}

```

- There are 25 constants (aka `input_ids`) defined in total which range from 0 to 24, all of which are pretty self-explanatory.

- Everytime you call `bossy.readValue(input_id)` or `bossy.readState(input_id)`, the microcontroller gets the raw signal (`reading`) from
the appropriate channel of the appropriate multiplexer and saves it in memory.

- The `reading` is a value between `0` and `1023`.

- It also converts this raw `reading` into a predefined `state` which is also saved.

- You can access these saved reading and state with `bossy.savedReading(input_id)` and `bossy.savedState(input_id)`.

- It is important to note that the two latter function doesn't read a new signal,
it just gets the most recently read ones.

- `bossy.savedReadingLowRes(input_id)` maps the raw value from the range 0-1023 to the range 0-255. This is particularly useful
if you only want the reading to fit only one byte, and don't mind downgrading the resolution.

# States

For reference the predefined state is a number between 0 and 4 and the number's meaning is dependent on
the what type of input it is.

```c++
 const uint8_t NUMBER_OF_STATES = 5;

  // these are the possible states
  const uint8_t PUSHED = 4;
  const uint8_t NOT_PUSHED = 0;

  const uint8_t NEUTRAL = 2;

  const uint8_t DOWN = 0;
  const uint8_t DOWN_CENTER = 1;
  const uint8_t UP_CENTER = 3;
  const uint8_t UP = 4;

  const uint8_t LEFT = 0;
  const uint8_t LEFT_CENTER = 1;
  const uint8_t RIGHT_CENTER = 3;
  const uint8_t RIGHT= 4;

  const uint8_t SCALE_0 = 0;
  const uint8_t SCALE_1 = 1;
  const uint8_t SCALE_2 = 2;
  const uint8_t SCALE_3 = 3;
  const uint8_t SCALE_4 = 4;
```

It's more easily visualized like this:
```
  /*
  The possible states are just
  different ways to interpret a number from 0 to 4
   0------------1------------2------------3------------4            > SCALE
   |            |            |            |            |
   PUSH.........|............|............|............NOT_PUSHED   > button
   LEFT.........|............|............|............RIGHT        > spdt switch on-on
   LEFT.........|.........NEUTRAL.........|............RIGHT        > spdt switch on-off-on
   LEFT....LEFT_CENTER....NEUTRAL.....RIGHT_CENTER.....RIGHT        > joystick x axis
   UP......UP_CENTER......NEUTRAL.....DOWN_CENTER......DOWN         > joystick y axis
   UP...........|.........NEUTRAL.........|............DOWN
   UP...........|............|............|............DOWN
   |            |            |            |            |
   0------------1------------2------------3------------4            > SCALE
  */

```

# Only on state or reading change.

Most of the time, we only care about the input's value if and only if
the value has changed. We can do this by calling the `bossy.hasChangedState(input_id)` or `bossy.hasChangedReading(input_id)`
which outputs a boolean: either `true` or `false`. These two functions also reads and processes a new raw signal
and saves the results in memory that can be immediately accessed by calling `bossy.savedState(input_id)`,
`bossy.savedReading(input_id)`, `bossy.savedReadingLowRes(input_id)`

When button has changed state
```c++
if (bossy.hasChangedState(LEFT_BUTTON_UPPER)) {
  if (bossy.savedState(LEFT_JOYSTICK_UPPER_X)==PUSHED) {
    Serial.println("just pressed")
  } else {
    Serial.println("just released")
  }
}
```

When joystick is moved
```c++

if (bossy.hasChangedState(RIGHT_JOYSTICK_UPPER_Y)) {
  int state = bossy.savedState(RIGHT_JOYSTICK_UPPER_Y);
  switch (state) {
    case DOWN:
      Serial.println("LOWEST")
      break;
    case DOWN_CENTER:
      Serial.println("LOW")
      break;
    case NEUTRAL:
      Serial.println("NEUTRAL")
      break;
    case UP_CENTER:
      Serial.println("HIGH")
      break;
    case UP:
      Serial.println("HIGHEST")
      break;
    default:
      break;
  }
}
```

When potentiometer is turned
```c++
if (bossy.hasChangedReading(input_id)) {
  Serial.println(bossy.savedReadingLowRes(POT_RIGHT));
}
```




