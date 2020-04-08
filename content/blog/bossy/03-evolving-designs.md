---
title: "🎮 Bossy: Evolving Designs"
date: 2020-03-19T03:39:01+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: false
---


## Version X.X Ideas (Update: March 30, 2020)
I'm experimenting with several possible designs for prototype 2.
In this design, I added 2 additional buttons, and 4 trimmers at the bottom.
Bring the total number of inputs from 25 to 31.

I realized that I wanted only a total of 8 potentiometers so that the visualization
of all 8 potentiometers would fit when they occupy one bar each in the led matrix.
So instead of 4 additional trimmers, I'll instead add only three
bringing the total number of inputs from 25 to 30.

I'm also thinking about using an Adafruit Metro Mini instead of an Arduino Micro.
They're both very expensive especially compared to Arduino Nano clones, but Adafruit Metro Mini
is half the price of the Arduino Micro, it's like an Arduino Uno in a cuter package, and well,
I really like it so and I have it here, so, haha.

I'm also entertaining adding two additional buttons (total of 32 unique inputs) and
maybe also an orientation sensor, but maybe that's adding too much complication with very little reward.

We'll see my final decision once I get around buying the necessary components for the next prototype.

![](/robotics-blog/draft-v3.png)

|![](/robotics-blog/draft-prelim-1-50x35.png)|![](/robotics-blog/draft-prelim-2-50x35.png)|
| ---------- | ---------- |


I checked out readily available protoboards: the brown 19x15 and both the 10x15 and 8x12 ones.
The first one 30 x 48 holes, the second is 37 x 55 and the third is 30 x 42.

|![](/robotics-blog/draft-proto-brown.png)|![](/robotics-blog/draft-proto8x12.png)|![](/robotics-blog/draft-proto10x15.png)|
| ---------- | ---------- | ---------- |

## Version 1.0
- I had to remove one button, one potentiometer, and the neopixel stick because of the protoboard's constraints.

![](/robotics-blog/v2-layout.png)

|![](/robotics-blog/v2-wip-1.JPG)|![](/robotics-blog/soldered-components-5.jpg)|
| ---------- | ---------- |

A possible improvement.
![](/robotics-blog/cleaner-wiring.png)

## Version 0.1

- Here's my testing setup
![](/robotics-blog/v1-breadboard.png)

- Here's my pathetic attempt to design the schematics. After several hours of pain, i decided to give up. lol

|![](/robotics-blog/v1-messy-pcb.png)|![](/robotics-blog/v1-messy-schematic.png)|![](/robotics-blog/breadboard.png)|
| ---------- | ---------- | ---------- |


## Arduino Micro Pinouts
- For future reference
![](/robotics-blog/arduino-micro.png)

## Adafruit Metro Mini Pinouts
- For future reference
