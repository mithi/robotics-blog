---
title: "🕷️ Hexapod Simulator: Boring useless ramblings"
date: 2020-04-08T07:03:15+08:00
categories: [hexapod-simulator, hexapod-robot-simulator]
tags: [hexapod-robot-simulator, hexapod-simulator, hexapod]
draft: false
---

### The obsession

Back in 2013, I came across a [kickstarter project by Arcbotics](https://www.kickstarter.com/projects/arcbotics/hexy-the-hexapod-low-cost-six-legged-open-robot). It was an open-source hexapod kit.
You can download the files and have it lasercut on a 5mm thick flat acrylic sheet or have them 3d printed.
It was the cheapest hexapod robot that I knew off back then, about 1/10th the usual price of the popular hexapod robots on the market. But I didn't have money. On 2016, I could finally own and build this hexapod, and I got obsessed with learning everything about hexapod robots.

I wrote a series of articles about it, on a now [abandoned wordpress site](https://hexyrobot.wordpress.com/), about stability, kinematics, inverse kinematics, common gaits etcetera, etcetera. I've made modifications and upgrades like using metal gear servos, using a beefy plugged powers upply, and  I wrote [code from scratch](https://github.com/mithi/hexy/) that you can run on the Raspberry Pi that to make a hexapod robot dance. I asked my friend to make a [video of my hexapod robot dancing](https://www.youtube.com/watch?v=tG-Y_kE-LaE) and eventually spoke about hexapod robots on a [Python conference](https://medium.com/@mithi/a-raspberry-pi-hexy-transcript-62533c69a566). There's something about hexapod robots that I find really fascinating. The many possibilities of complex movements given 18 degrees of freedom - one of highest if not the highest of all standard robot configuration.

### What I was looking for

One of the things I was looking at that time was an open-source robot simulator, one which I could study, customize and play with. My requirement was very simple

1. I am able to install and run it on my laptop, or view it on the web.
2. I could see the hexapod in a 3D plot.
3. I could specify the hexapod dimensions
4. Given the roll, pitch, yaw, translation of the body, foot positions, the corresponding 18 joint angles would be displayed.

I got stuck in requirement one. Then I moved on with my life. Three years later, I found out I haven't really moved on.

### What I found

I found a few on Youtube most of them using a matlab environment and the code wasn't really available.

If you search hexapod robot simulator on Github at this very moment in writing [April 8, 2020, 3:64 PM Philippine time], there are a total of only six repository results, one of them is mine.

1. Mine
2. A fork of my own project
3. One which only a README containing two lines, the title and the subtitle.
4. One a hexapod leg model of one leg
5. A code last updated 5 years ago specific to a hexapod built with JavaOpenGL with very little documentation on how to get it to run. The author even said that new bug may have snuck in and s/he it seems like has also moved on.
6. A unity hexapod simulator with two commits, one is the first commit, and the other was Create ReadMe.

### The python simulator that I thought I could hack

I saw one unmaintained Python code from 2013 from an unmaintained [forum](https://discourse.arcbotics.com/t/inverse-kinematics/116/33), the  embedded photos weren't showing anymore. The genius author wrote the inverse kinematics from scratch using Numpy and made displayed it only with PyGame, which I thought was brillliant (aside from the fact that he wrote it in only 16 days), but it a few things I didn't like.

 The code wasn't source-controlled, it was just a link to a Dropbox folder pasted on a forum. The creator seemed to have moved on and had no interest in mantaining it.

The code was was very specific, specific for a specific hexapod that he owned of specicfic measurements, the coxia limb length (body contact of leg to first joint), was even assumed to be zero. The graphic user interface doesn't display the actual computed angles. The angles also wasn't displaying correctly and it quite a long time to understand why it wasn't what was expected. It had a code something like

```python
if (footY < 75) and footY > > -75:
  kneeAngle = math.degrees(asin(float(footY)/75.0))
  ankleOffset - kneeAngle + footRx
```

When it shoud have been `ankleAngle = 90 - kneeAngle` to be honest I'm not quite sure exactly why. He actually tried to explain the code, but it was very difficult given that the explanations were very visual and his code was very specific to his hexapod, some of the named variabled required the accompanying drawings to understand, and some of the estimates were specific to the build.

I could go on and on, but my point was, the author had no interest maintaining it, it was highly specific, and most importantly I found it very hard - with my not-that-smart brain - to hack.

### The closest thing

The best I could find was a [Excel spreadsheet calculator with 2d visuals by Oscar Liang](https://oscarliang.com/inverse-kinematics-implementation-hexapod-robots/), but requirements 2, 3, 4 wasn't satisfied.

- No 3d plot
- Can only specify only side of the body (it assumes it's a perfect hexagon), I wanted to specify three numbers (front, middle, side of the triangle made by 3 adjacent vertices for example)
- No roll, pitch, yaw

### This rambling has to stop

I just had to get this off my chest. The world needs a hexapod robot simulator. Last February, I told my self that I'm going to make one. How hard could it be? Well, I found the answer was Yes.