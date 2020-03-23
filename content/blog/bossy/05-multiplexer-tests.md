---
title: "Bossy: Multiplexer Tests"
date: 2020-03-22T14:28:34+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---

![](/robotics-blog/multiplexers.png)

I tested the multiplexers if they're working.
To save time, I only tested two channels for each multiplexer,
channel 0, and 15.

I used two potentiometers which I connect randomly at
channel 0 or channel 15 of multiplexer 1 or multiplexer 2
(four possibilities). I also randomly pull the remaining
two channels either down to ground or up to 5 volts.

Then I check the Serial monitor if it behaves the way I expect.


```arduino

int s0 = 5;
int s1 = 6;
int s2 = 7;
int s3 = 8;

int mux1_enable_pin = 11;
int mux2_enable_pin = 12;

int value1 = 0;
int value2 = 0;

int sig_pin = A0;

void setup(){
  pinMode(s0, OUTPUT);
  pinMode(s1, OUTPUT);
  pinMode(s2, OUTPUT);
  pinMode(s3, OUTPUT);

  pinMode(mux1_enable_pin, OUTPUT);
  pinMode(mux2_enable_pin, OUTPUT);

  digitalWrite(s0, LOW);
  digitalWrite(s1, LOW);
  digitalWrite(s2, LOW);
  digitalWrite(s3, LOW);

  // Pulling down enable pin to ground enables the multiplexer
  digitalWrite(mux1_enable_pin, HIGH);
  digitalWrite(mux2_enable_pin, HIGH);

  Serial.begin(9600);
}

void loop(){

  // Read and distplay values at channel 0 and channel 15
  //for mux 1 and mux 2 every 1 second

  enable_mux1();
  value1 = readMux(0);
  value2 = readMux(15);
  print_mux_values(1, value1, value2);

  enable_mux2();
  value1 = readMux(0);
  value2 = readMux(15);
  print_mux_values(1, value1, value2);

  Serial.println();
  delay(1000);
}

void enable_mux1() {
  digitalWrite(mux1_enable_pin, LOW);
  digitalWrite(mux2_enable_pin, HIGH);
}

void enable_mux2() {
  digitalWrite(mux1_enable_pin, HIGH);
  digitalWrite(mux2_enable_pin, LOW);
}


int readMux(int channel){

  int controlPin[] = {s0, s1, s2, s3};

  int muxChannel[16][4]= {
    {0,0,0,0}, //channel 0
    {1,0,0,0}, //channel 1
    {0,1,0,0}, //channel 2
    {1,1,0,0}, //channel 3
    {0,0,1,0}, //channel 4
    {1,0,1,0}, //channel 5
    {0,1,1,0}, //channel 6
    {1,1,1,0}, //channel 7
    {0,0,0,1}, //channel 8
    {1,0,0,1}, //channel 9
    {0,1,0,1}, //channel 10
    {1,1,0,1}, //channel 11
    {0,0,1,1}, //channel 12
    {1,0,1,1}, //channel 13
    {0,1,1,1}, //channel 14
    {1,1,1,1}  //channel 15
  };

  //loop through the 4 control pins
  for(int i = 0; i < 4; i ++){
    digitalWrite(controlPin[i], muxChannel[channel][i]);
  }

  //read the value at the SIG pin
  int val = analogRead(sig_pin);
  return val;
}

void print_mux_values(int mux_id, int value1, int value2) {

  Serial.print("mux");
  Serial.print(mux_id);
  Serial.print(": ");
  Serial.print(value1);
  Serial.print(" | ");
  Serial.print(value2);
  Serial.print(" | ");
}

```

# References
- 2011-feb-1 [Bildr: Muxing Around With The CD74HC4067 + Arduino ](https://bildr.org/2011/02/cd74hc4067-arduino/)

