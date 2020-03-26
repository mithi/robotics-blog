---
title: "Bossy Inputs: Coding 3 -  All together now"
date: 2020-03-27T01:01:40+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---
>The full draft version of the code is here:
># [Bossy Inputs Code Draft](https://github.com/mithi/algorithm-playground/tree/master/bossy)
>IMPORTANT: The code is for processing inputs only, no output on the led matrix or sending data wirelessly yet

When compiled in the Arduino Micro
```
Sketch uses 7726 bytes (26%) of program storage space. Maximum is 28672 bytes.
Global variables use 528 bytes (20%) of dynamic memory, leaving 2032 bytes for local variables. Maximum is 2560 bytes.
```

When compuled in the Arduino Uno
```
Sketch uses 5564 bytes (17%) of program storage space. Maximum is 32256 bytes.
Global variables use 563 bytes (27%) of dynamic memory, leaving 1485 bytes for local variables. Maximum is 2048 bytes.
```


# Sketch `bossy.ino`
```arduino
#include "src/Bossy.h"

Bossy bossy;

void setup() {
  Serial.begin(9600);
}

void loop() {
  //int value = bossy.readValue(LEFT_JOYSTICK_UPPER_X);
  //int value = bossy.readState(LEFT_BUTTON_UPPER);

  print_on_pot_reading_change("p0: ", POT_LEFT);
  print_on_pot_reading_change("p1: ", POT_LEFT_CENTER);
  print_on_pot_reading_change("p2: ", POT_CENTER);
  print_on_pot_reading_change("p3: ", POT_RIGHT_CENTER);
  print_on_pot_reading_change("p4: ", POT_RIGHT);

  print_on_button_state_change("b-UL: ", LEFT_BUTTON_UPPER);
  print_on_button_state_change("b-LL: ", LEFT_BUTTON_LOWER);
  print_on_button_state_change("b-UR: ", RIGHT_BUTTON_UPPER);
  print_on_button_state_change("b-LR: ", RIGHT_BUTTON_LOWER);

  print_on_button_state_change("jb-UL: ", LEFT_JOYSTICK_UPPER_BUTTON);
  print_on_button_state_change("jb-LL: ", LEFT_JOYSTICK_LOWER_BUTTON);
  print_on_button_state_change("jb-UR: ", RIGHT_JOYSTICK_UPPER_BUTTON);
  print_on_button_state_change("jb-LR: ", RIGHT_JOYSTICK_LOWER_BUTTON);

  print_on_scale_change("jx-UL: ", LEFT_JOYSTICK_UPPER_X);
  print_on_scale_change("jx-LL: ", LEFT_JOYSTICK_LOWER_X);
  print_on_scale_change("jx-UR: ", RIGHT_JOYSTICK_UPPER_X);
  print_on_scale_change("jx-LR: ", RIGHT_JOYSTICK_LOWER_X);

  print_on_scale_change("jy-UL: ", LEFT_JOYSTICK_UPPER_Y);
  print_on_scale_change("jy-LL: ", LEFT_JOYSTICK_LOWER_Y);
  print_on_scale_change("jy-UR: ", RIGHT_JOYSTICK_UPPER_Y);
  print_on_scale_change("jy-LR: ", RIGHT_JOYSTICK_LOWER_Y);

  print_on_scale_change("sw-UL: ", LEFT_SWITCH_UPPER);
  print_on_scale_change("sw-LL: ", LEFT_SWITCH_LOWER);
  print_on_scale_change("sw-UR: ", RIGHT_SWITCH_UPPER);
  print_on_scale_change("sw-RL: ", RIGHT_SWITCH_LOWER);
}

void print_on_button_state_change(String prefix, int input_id) {
  if (bossy.hasChangedState(input_id)) {
    Serial.print(prefix);
    bossy.savedState(input_id)==PUSHED ?
      Serial.println("pressed") : Serial.println("not_pressed");
  }
}

void print_on_pot_reading_change(String prefix, int input_id) {
  if (bossy.hasChangedReading(input_id)) {
    Serial.print(prefix);
    Serial.print(bossy.savedReading(input_id));
    Serial.print(" ");
    Serial.println(bossy.savedReadingLowRes(input_id));
  }
}

void print_on_scale_change(String prefix, int input_id) {
  if (bossy.hasChangedState(input_id)) {
    Serial.print(prefix);
    Serial.println(bossy.savedState(input_id));
  }
}

```

# Header `src/Bossy.h`

```arduino
#ifndef Bossy_h
#define Bossy_h

#include "Arduino.h"


/**********************
  MULTIPLEXER PINS
*************************/
#define PIN_ENABLE_LEFT_MUX 11
#define PIN_ENABLE_RIGHT_MUX 12

#define s0 5
#define s1 6
#define s2 7
#define s3 8
#define PIN_SIGNAL A1

/**********************
 CHANNELS
*************************/
#define NUMBER_OF_CHANNELS 25

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

/**********************
 INPUT TYPE (normal or inverted)
*************************/
// The input type is normal when:
// button: pressed is HIGH
// joystick y axis: UP is HIGH
// joystick x axis: RIGHT is HIGH
// switch: RIGHT is HIGH
// it is inverted if otherwise
#define NORMAL true
#define INVERTED false

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


/**********************
 INPUT IDs
*************************/

// left inputs

#define LEFT_SWITCH_UPPER 0
#define LEFT_SWITCH_LOWER 1

#define LEFT_JOYSTICK_UPPER_Y 2
#define LEFT_JOYSTICK_UPPER_X 3
#define LEFT_JOYSTICK_UPPER_BUTTON 4

#define LEFT_JOYSTICK_LOWER_Y 5
#define LEFT_JOYSTICK_LOWER_X 6
#define LEFT_JOYSTICK_LOWER_BUTTON 7

#define LEFT_BUTTON_UPPER 8
#define LEFT_BUTTON_LOWER 9

#define POT_LEFT 10
#define POT_LEFT_CENTER 11
#define POT_CENTER 12

// right inputs

#define RIGHT_SWITCH_UPPER 13
#define RIGHT_SWITCH_LOWER 14

#define RIGHT_JOYSTICK_UPPER_Y 15
#define RIGHT_JOYSTICK_UPPER_X 16
#define RIGHT_JOYSTICK_UPPER_BUTTON 17

#define RIGHT_JOYSTICK_LOWER_Y 18
#define RIGHT_JOYSTICK_LOWER_X 19
#define RIGHT_JOYSTICK_LOWER_BUTTON 20

#define RIGHT_BUTTON_UPPER 21
#define RIGHT_BUTTON_LOWER 22

#define POT_RIGHT_CENTER 23
#define POT_RIGHT 24

/**********************
 MAP input id to multiplexer id (left or right)
*************************/

// the ids for the left multiplexer is 0 to 12
// the ids for the right multiplexer is 13 to 24
#define MUX_LEFT_MIN 0
#define MUX_LEFT_MAX 12

#define MULTIPLEXER_RIGHT_MIN 13
#define MULTIPLEXER_RIGHT_MAX 24

/**********************
  AGGREGATED BUTTONS
*************************/

#define NUMBER_OF_BOOLEAN_INPUTS 8

// which channels are boolean inputs/ie buttons
const uint8_t BOOLEAN_INPUTS[NUMBER_OF_BOOLEAN_INPUTS] = {
  LEFT_BUTTON_LOWER,
  LEFT_BUTTON_UPPER,
  LEFT_JOYSTICK_LOWER_BUTTON,
  LEFT_JOYSTICK_UPPER_BUTTON,
  RIGHT_BUTTON_LOWER,
  RIGHT_BUTTON_UPPER,
  RIGHT_JOYSTICK_LOWER_BUTTON,
  RIGHT_JOYSTICK_UPPER_BUTTON
};

// which buttons are not pulled up to 5v
// temporary software hack
const uint8_t BROKEN_BUTTONS[NUMBER_OF_BOOLEAN_INPUTS] = {
  LEFT_JOYSTICK_LOWER_BUTTON,
  LEFT_JOYSTICK_UPPER_BUTTON,
  RIGHT_JOYSTICK_LOWER_BUTTON,
  RIGHT_JOYSTICK_UPPER_BUTTON
};

/**********************
  POSSIBLE STATES
*************************/

// these are the possible states
#define PUSHED 4
#define NOT_PUSHED 0

#define NEUTRAL 2

#define DOWN 0
#define DOWN_CENTER 1
#define UP_CENTER 3
#define UP 4

#define LEFT 0
#define LEFT_CENTER 1
#define RIGHT_CENTER 3
#define RIGHT 4

#define SCALE_0 0
#define SCALE_1 1
#define SCALE_2 2
#define SCALE_3 3
#define SCALE_4 4

/*

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

/**********************
  BOSSY CONTROLLER CLASS
*************************/
class Bossy {
public:
  Bossy(void);
  // these three functions are READ-ONLY
  // reads the last saved readings & states
  // does NOT update or read new signals
  uint16_t savedReading(const uint8_t input_id);
  uint16_t savedState(const uint8_t input_id);
  uint8_t savedReadingLowRes(const uint8_t input_id);

  // these four functions has WRITE access
  // updates readings & states
  uint16_t readValue(const uint8_t input_id);
  uint8_t readState(const uint8_t input_id);
  bool hasChangedState(const uint8_t input_id);
  bool hasChangedReading(const uint8_t input_id);

private:
  uint16_t _readings[NUMBER_OF_CHANNELS];
  uint8_t _states[NUMBER_OF_CHANNELS];
  void _update(const uint8_t input_id);
  void _updateReading(const uint8_t input_id);
  void _updateState(const uint8_t input_id);
  void _enableRightMux(void);
  void _enableLeftMux(void);
  uint16_t _readMux(uint8_t channel);
  uint8_t _constrain(const uint8_t input_id);
  void _saveDebouncedReading(const uint8_t input_id);
  bool _isButton(const uint8_t input_id);
  bool _isBrokenButton(const uint8_t input_id);
  void _updateBrokenButton(const uint8_t input_id);
};

#endif

```

# Source `src/Bossy.cpp`

```arduino
#include "Arduino.h"
#include "Bossy.h"

Bossy::Bossy(void) {
  pinMode(s0, OUTPUT);
  pinMode(s1, OUTPUT);
  pinMode(s2, OUTPUT);
  pinMode(s3, OUTPUT);

  pinMode(PIN_ENABLE_LEFT_MUX, OUTPUT);
  pinMode(PIN_ENABLE_RIGHT_MUX, OUTPUT);

  digitalWrite(s0, LOW);
  digitalWrite(s1, LOW);
  digitalWrite(s2, LOW);
  digitalWrite(s3, LOW);

  // pulling down enable pin to ground enables the multiplexer
  digitalWrite(PIN_ENABLE_LEFT_MUX, HIGH);
  digitalWrite(PIN_ENABLE_RIGHT_MUX, HIGH);

  // set all readings to a nonsense number
  for (uint8_t input_id = 0; input_id < NUMBER_OF_CHANNELS; input_id++) {
    _update(input_id);
  }
}

uint16_t Bossy::savedReading(const uint8_t input_id) {
  const uint8_t i = _constrain(input_id);
  return _readings[i];
}

uint16_t Bossy::savedState(const uint8_t input_id) {
  const uint8_t i = _constrain(input_id);
  return _states[i];
}

uint8_t Bossy::savedReadingLowRes(const uint8_t input_id){
  // convert reading from range 0-1023 to 0-255
  const uint8_t i = _constrain(input_id);
  return min(_readings[i] / 4, 255);
}

uint16_t Bossy::readValue(const uint8_t input_id) {
  const uint8_t i = _constrain(input_id);
  _update(i);
  return _readings[i];
}

uint8_t Bossy::readState(const uint8_t input_id) {
  const uint8_t i = _constrain(input_id);
  _update(i);
  return _states[i];
}

bool Bossy::hasChangedState(const uint8_t input_id) {
  const uint8_t i = _constrain(input_id);
  const uint16_t old_state = _states[i];
  _update(i);
  return old_state != _states[i] ? true: false;
}

bool Bossy::hasChangedReading(const uint8_t input_id) {
  const uint8_t i = _constrain(input_id);
  const uint16_t old_reading = _readings[i];
  _update(i);
  const uint16_t new_reading = _readings[i];

  // remove noise, especially of potentiometer
  const uint16_t TOL = 2;
  if (old_reading > new_reading) {
    return old_reading - new_reading > TOL;
  }
  return new_reading - old_reading > TOL;
}

void Bossy::_update(const uint8_t input_id) {
  //temporarily makeshift function for joystick buttons without pullup resistors
  if (_isBrokenButton(input_id)) {
    _updateBrokenButton(input_id);
    return;
  }

  _updateReading(input_id);
  _updateState(input_id);
}

void Bossy::_updateState(const uint8_t input_id) {
  const uint16_t reading = _readings[input_id];
  const bool input_type = INPUT_TYPE[input_id];

  if (input_type == NORMAL) {
    if (reading < 200) { _states[input_id] = SCALE_0; return; }
    if (reading < 400) { _states[input_id] = SCALE_1; return; }
    if (reading < 600) { _states[input_id] = SCALE_2; return; }
    if (reading < 800) { _states[input_id] = SCALE_3; return; }
    _states[input_id] = SCALE_4; return;
  } else {
    if (reading > 800) { _states[input_id] = SCALE_0; return; }
    if (reading > 600) { _states[input_id] = SCALE_1; return; }
    if (reading > 400) { _states[input_id] = SCALE_2; return; }
    if (reading > 200) { _states[input_id] = SCALE_3; return; }
    _states[input_id] = SCALE_4; return;
  }
}

void Bossy::_updateReading(const uint8_t input_id) {
  // check and enable multiplexer we are suppose to read from
  const bool is_left_mux = input_id >= MUX_LEFT_MIN && input_id <= MUX_LEFT_MAX;
  is_left_mux ? _enableLeftMux() : _enableRightMux();

  //debounce if it's a button else just save the reading
  if (_isButton(input_id)) {
    _saveDebouncedReading(input_id);
    return;
  }

  const uint8_t channel = INPUT_CHANNEL[input_id];
  _readings[input_id] = _readMux(channel);
}

void Bossy::_enableLeftMux(void) {
  digitalWrite(PIN_ENABLE_LEFT_MUX, LOW);
  digitalWrite(PIN_ENABLE_RIGHT_MUX, HIGH);
}

void Bossy::_enableRightMux(void) {
  digitalWrite(PIN_ENABLE_LEFT_MUX, HIGH);
  digitalWrite(PIN_ENABLE_RIGHT_MUX, LOW);
}

uint16_t Bossy::_readMux(const uint8_t channel) {
  const uint8_t controlPin[] = {s0, s1, s2, s3};
  const uint8_t muxChannel[16][4]= {
    {0, 0, 0, 0}, //channel 0
    {1, 0, 0, 0}, //channel 1
    {0, 1, 0, 0}, //channel 2
    {1, 1, 0, 0}, //channel 3
    {0, 0, 1, 0}, //channel 4
    {1, 0, 1, 0}, //channel 5
    {0, 1, 1, 0}, //channel 6
    {1, 1, 1, 0}, //channel 7
    {0, 0, 0, 1}, //channel 8
    {1, 0, 0, 1}, //channel 9
    {0, 1, 0, 1}, //channel 10
    {1, 1, 0, 1}, //channel 11
    {0, 0, 1, 1}, //channel 12
    {1, 0, 1, 1}, //channel 13
    {0, 1, 1, 1}, //channel 14
    {1, 1, 1, 1}  //channel 15
  };

  //Send the appropriate signals
  for(int i = 0; i < 4; i ++){
    digitalWrite(controlPin[i], muxChannel[channel][i]);
  }

  return analogRead(PIN_SIGNAL);
}

bool Bossy::_isButton(const uint8_t input_id) {
  for (uint8_t i = 0; i < NUMBER_OF_BOOLEAN_INPUTS; i++) {
    if (BOOLEAN_INPUTS[i] == input_id) { return true; }
  }
  return false;
}

void Bossy::_saveDebouncedReading(const uint8_t input_id) {
  const uint16_t saved_reading =  _readings[input_id];
  const uint8_t channel = INPUT_CHANNEL[input_id];
  const uint16_t reading = _readMux(channel);

  if (saved_reading != reading) {
    _delay_ms(5);
    const uint16_t second_reading = _readMux(channel);
    if (second_reading == reading) {
      _readings[input_id] = reading;
    }
  }
}

uint8_t Bossy::_constrain(const uint8_t input_id) {
  // input id can only be within 0-24
  return min(max(0, input_id), NUMBER_OF_CHANNELS - 1);
}

bool Bossy::_isBrokenButton(const uint8_t input_id) {
  for (uint8_t i = 0; i < 4; i++) {
    if (BROKEN_BUTTONS[i]==input_id) { return true; }
  }
  return false;
}

void Bossy::_updateBrokenButton(const uint8_t input_id) {
  const uint16_t saved_reading = _readings[input_id];

  const bool is_left_mux = input_id >= MUX_LEFT_MIN && input_id <= MUX_LEFT_MAX;
  is_left_mux ? _enableLeftMux() : _enableRightMux();
  const uint8_t channel = INPUT_CHANNEL[input_id];
  uint16_t reading = _readMux(channel);
  reading = reading == 0 ? 0 : 1023;

  if (saved_reading != reading) {
    _delay_ms(50);
    uint16_t second_reading = _readMux(channel);
    second_reading = second_reading == 0 ? 0 : 1023;

    if (second_reading == reading) {
      _readings[input_id] = reading;
    }
  }

  _states[input_id] = _readings[input_id] == 0 ? PUSHED : NOT_PUSHED;
}

```

>The full draft version of the code is here:
># [Bossy Inputs Code Draft](https://github.com/mithi/algorithm-playground/tree/master/bossy)
>IMPORTANT: The code is for processing inputs only, no output on the led matrix or sending data wirelessly yet
