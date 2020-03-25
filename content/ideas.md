# Ideas
- Ideas for robotics projects I want to do

## Part 1

```
1.  Bossy: A custom general-purpose wireless controller
    -  Build controller Build with I already have

2. Control Three custom robots with Bossy
    - Robot #1 Arm Tank - MeArm 1.0 (Open-source 4-axis manipulator) on top of a metal tank chassis
    - Robot #2 Hacked Toy RC car frame, with pan-tilt servos and brushless motor
    - Robot #3 Xylophone Playing Machine with Servos

3. 7Bot
    - Explore the the open-source 6-axis manipulator from the ground up
    - Use it for various tasks like typing on a keyboard, placing go stones and more

4. Organize my Lego!

5. Hacking the Lego EV3 Motors
    - User the Motors from the Lego EV3 kit with Arduino-like microcontrol
    - Make one controller mimick the other.
    - Showcase various lego mechanisms with these motors from Yoshihito Isogawa's book
    - Make a Drawing Turtle Bot

6. Build various Mechanical Walkers (Klann's Linkage)
    - With Lego based on Ben Vagle, Wade Vagle and DIYwalkers.com tutorials

7. Master Builders Academy
    - Checkout and Review Lego Master Builders Academy Books
    - Make notes and apply what I learned to create simple designs

8. Sariel's Two Books
    - Review and make notes using Sariel's Unofficial Lego Technic Builder's Guide,
    - Review his other book "Incredible Lego Technic: Cars Trucks, Robots and more"

9. Assemble one or two of my Lego Technic Sets.
    - Make a timelapse video

10. Three Popular Women Makers:
    - Former Queen of Shitty Robots: Simone Giertz
    - Real Sexy Cyborg: Naomi Wu
    - Lady Ada, Adafruit Founder: Limor Fried

11. Learn Tinkercad Basics

12. Build simple browser-based robot simulators
    - hexapod, quadruped, and manipulator configurations
```

## Part 2

```
1. Get a miniature (1x1 sq inch) single-needle watercolor tattoo
    - Neir Automata Stubby or Blackbox with Flowers
    - Maybe the Super Giant Game Transistor sword?

2. Buy, Assemble, and Test the Creality Ender Pro 3D printer

3. Build a Raspberry Pi Based Servo Controller
    - Raspberry Pi Zero W, Two Adafruit Servo Bonnets, peripherals

4. Build a drawing Turtle bot with stepper motors and servo motors (to put colored markers up and down)

5. Build a working Miniature Enchanted Kingdom Air Race Ride Model
    - Using 5 stepper motors, Lego parts and 3D printed Parts

6. Build various open-source 3d-printed robots and mechanisms
    - Download from thingiverse, mostly micro servo based

7. Build a Beginner-Friendly Framework for Robots using Raspberry Pi and Adafruit Servo Driver
```

## Part 3

```
1. Design my own robots / robot kit
    - 2 quadrupeds, one with 8 joints one with 12 joints
    - 2 hexapods, one with 12 joints one with 18 joints
    - Dual 4-axis manipulator for generic pick and place
    - Triple 3-axis manipulator in parallel for XY plotting
    - 7DOF Biped with Arms and rotating hat
    - 12DOF Humanoid robot
    - Pan and Tilt with gripper claw

2. Build a custom drone and share my learnings
    - Watch Joop Brokking's make arduino flight controller drone from scratch
    - Learn more about drones based on Drones Dojo of Caleb Bergquist

3. Build my own custom Open Dogs
  - (brushless ones and servo-based ones) based on James Bruton's youtube videos

4. Lesson learned from Russ Tedrake's Underactuated Robotics course
```

## 7 BOT PROJECT

```
1. Rewrite Arduino Due's code to bare minimum
2. Use serial to communicate the angles from Mac to Arduino
3. Figure out inverse kinematics
4. Make script from x, y, z to angles to pwm signals
5. Use inverse kinematics to:
    - put go stones on go board precisely
    - Write my name
    - Pick and place, stack papercups
    - Type on keyboard
6. Add record functionality and use this to
    - Write name
    - Put honey on bread
    - Stir soup
7. Can robot arm learn inverse kinematics using machine learning?
  - PWM -> Angles -> Pose (x, y, z)

```

## Bossy Custom Controller derivatives
- mini-bossy, tiny-bossy

|![](/robotics-blog/mini-bossy.png)|![](/robotics-blog/tiny-bossy.png)|
| ---------- | ---------- |


## Component Tester
- Inspired by [VolorsR Instructable](https://www.instructables.com/id/Arduino-Multimeter-and-Components-Tester/)
- It has a mini oled display. You can check analog sensor readings, continuity, resistance. You can also
set pwm values using potentiometers when you want to calibrate servos
- You can check 2 analog sensor readings, 2 resistances and set two pwm values
- A couple of preliminary designs I came up with

|![](/robotics-blog/component-tester-1.png)|![](/robotics-blog/component-tester-2.png)|
| ---------- | ---------- |
|![](/robotics-blog/component-tester-3.png)|![](/robotics-blog/component-tester-volosr.png)|
