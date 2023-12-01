# Arduino field oriented control (FOC) reaction wheel inverted pendulum   

![Test Compile](https://github.com/simplefoc/Arduino-FOC-reaction-wheel-inverted-pendulum/workflows/Library%20Compile/badge.svg)

This is a project of designing and controlling the reaction wheel inverted pendulum based entirely on Arduino [SimpleFOC library and SimpleFOC shield](https://github.com/simplefoc/Arduino-FOC). 

<p align="center">
    <img src="images/swing-up.gif" height="230px">
    <img src="images/stabilization.gif" height="230px">
</p>

This is a very fun project in many ways, and it is intended:
- Students in search for a good testing platform for their advanced algorithms
- Everyone with a bit of free time and a motivation to create something cool :D


### YouTube demonstration video :D
<p align="center">
<a href="https://youtu.be/Ih-izQyXJCI">
<img src="https://docs.simplefoc.com/extras/Images/youtube_pendulum.png"  height="320px">
</a>
</p>

But for me, the most exciting part of this project was the ability to use the Field Oriented Control algorithm. 
**The main benefits of using the BLDC motor in this project are:**
-  High torque to weight ratio
   - The lighter the better
-  Lots of torque for low angular velocities
   - No need to spin the motor to very high RPM to achieve high torques
-  No gearboxes and backlash
   - Very smooth operation = very stable pendulum

So far, FOC has been restricted to high-end applications due to the complexity and the cost of the hardware mostly, but also due to the lack of user-friendly, well documented software. Therefore I am very happy to show you the projects like this one, which directly benefit the FOC algorithm and BLDC motors and encourage you to use these techniques in your projects as well. One of the very simple ways of starting (demonstrated in this video) is using the [SimpleFOC library and SimpleFOC shield](https://github.com/
/Arduino-FOC). 


## Readme structure 
- [What are the necessary components?](#what-are-the-necessary-components)
  - [3D printed parts](#3d-printed-parts)
  - [Hardware parts](#hardware-parts)
  - [Electrical components](#electrical-components)
    - [BLDC motor](#bldc-motor)
    - [Position sensors](#position-sensors)
    - [BLDC driver](#bldc-driver)
  - [Table mount](#table-mount)
- [Connecting all the components](#connecting-all-the-components)
  - [Encoder 1 (motor)](#encoder-1-motor)
  - [Encoder 2 (pendulum)](#encoder-2-pendulum)
  - [Motor](#motor)
- [Arduino code](#arduino-code)
  - [Encoder 1 (motor) code](#encoder-1-motor-code)
  - [Encoder 2 (pendulum) code](#encoder-2-pendulum-code)
  - [Motor code](#motor-code)
  - [Control algorithm code](#control-algorithm-code)
      - [Stabilization](#stabilization)
      - [Swing-up](#swing-up)
      - [The integration](#the-integration)
  - [Full Arduino code](#full-arduino-code)
- [Contact](#contact)


# What are the necessary components?
Due to the using of the brushless motor and the SimpleFOC shield, this might be one of the simplest hardware setups of the reaction wheel inverted pendulum there is.
<p align="center">
    <img src="images/components.gif" height="350px">
    <img src="images/img1.png" height="300px">
</p>

## 3D printed parts
This pendulum project has three 3d printed parts. You can find them in the `CAD files > STL` directory. They are:
- inertia wheel (`reaction_wheel.stl`)
    - infill: `70%`
    - layer hight: `<0.12mm`
    - it should be printed as precise as possible (to reduce the vibrations)
    - mass: `100g`
- pendulum arm (`pendulum_arm.stl`) 
    - infill: `50-70%`
    - layer hight: `0.12-0.2mm`
    - no need for too much precision, only the strength
    - mass: `20g`
- base (`bottpm_swingup.stl`)
    - infill: `70%`
    - layer hight: `<0.12`
    - due to the bearings needs to be pretty precise
    - mass: `60g`
- cable holder (`cable_holder.stl`) - *optional*

I have printed the pendulum in the PETG filament but there should be no real difference in using PLA or ABS filaments, this is just what I had in hand at the moment.

You will also find all the CAD files used in this project in the `CAD files` folder. I have included the Solidworks 2016 parts and the full assembly. Additionally all the parts are exported to STEP format for easier porting to the other CAD IDEs.

> Beware: 
I have designed the holes on the pendulum arm and the wheel to match perfectly the motor the encoders I was using, so please check the dimensions of your motors and encoder holes before printing :D

If you would prefer some other CAD format, let me know, maybe I can export it differently. 

## Hardware parts
All of the mechanical hardware is very simple and can be bought in any hardware store. If you prefer to buy it online, I have included links to the Ebay products you could use. 

Component | Description | Link | Price
---- | ---- | ---- | ----
<img src="images/ball.jpg" height="100px"> | Ball bearing <br> 8x16x5mm <br> 3x | [Ebay](https://www.ebay.com/itm/608-623-625-688zz-Double-sided-Metal-Sealed-Deep-Groove-Ball-Bearing-High-Speed/223959673454?hash=item3425096a6e:m:mP1kq1bn9gGwE3T_vvNPXaA) | 3$
<img src="images/tube.jpg" height="100px"> | Aluminum tube <br> diameter: 8mm  <br> length: 30mm | [Ebay](https://www.ebay.com/itm/Aluminum-Tube-in-various-diam-and-many-lengths/122904111733?hash=item1c9da7ea75:m:mv3bktoI7tBDQ8xDbciZK6A) | 3$
<img src="images/m3.jpg" height="100px"> |M3 screws<br> 7pcs <br> M3x10mm | [Ebay](https://www.ebay.com/itm/M2-M2-5-M3-M4-M5-M6-M8-Stainless-Steel-Metric-Hex-Socket-Head-Cap-Screws-Bolt/123439613021?hash=item1cbd93045d:m:mQsuAChkph7G_5vk5ctmQiA) | 2$
<img src="images/m3.jpg" height="100px"> | M4 screws & nuts<br> 2pcs <br> M4x50mm | [Ebay](https://www.ebay.com/itm/M2-M2-5-M3-M4-M5-M6-M8-Stainless-Steel-Metric-Hex-Socket-Head-Cap-Screws-Bolt/123439613021?hash=item1cbd93045d:m:mQsuAChkph7G_5vk5ctmQiA) | 4$

## Electrical components
### BLDC motor
Unfortunately the BLDC motor I was using in my video is an old version of the [iPower GM4108](https://shop.iflight-rc.com/index.php?route=product/product&product_id=217&search=GM4108H-120T) motor which is not available anymore to buy, in any case I was not able to find a website that would sell them. 
The good news is that you can use basically any other gimbal motor in that class with only minor modifications, mostly in the CAD parts - change the mounting holes positions. Some of the motors I was able to find that would be the most similar to my motor would be:
Component | Description | Link | Price
---- | ---- | ---- | ----
<img src="images/gm4108.jpg" height="100px"> | iPower Motor GM4108H-120T | [iFlight webshop](https://shop.iflight-rc.com/index.php?route=product/product&product_id=217&search=GM4108H-120T) | 35$
<img src="images/bgm4108.jpg" height="100px"> | BGM 4108 120T | [Ebay](https://www.ebay.com/itm/gimbal-brushless-motor-BGM-4108-120T-hollow-silver-version/261539739163?hash=item3ce4fbb61b:g:4iAAAOSwPe1Tzdhy) | 30$
<img src="images/bgm_ebay.jpg" height="100px"> | BGM4108-150HS | [Ebay](https://www.ebay.com/itm/New-Brushless-Gimbal-Motor-BGM4108-150HS-24N22P-for-Sony-NEX5-7-Camera-Mount-DIY/261903969970?epid=1539061157&hash=item3cfab16eb2:g:0EIAAOSwiLdWBLrX) | 32$

### Position sensors
In this project I am using two encoders to track the movement of the pendulum and the motor. Now, this is somewhat particular to my implementation, there are may ways to measure the angle of the pendulum. You could use the `IMU` and measure the inclination by combining the accelerometer and the gyro measurement, you could also use the magnetic sensors such as `AS5048` and similar absolute encoders. 

The encoders I have used in this project are:
<img src="images/AMT_103.jpg" height="100px"> | AMT103 CUI | [Mouser](https://eu.mouser.com/ProductDetail/CUI-Devices/AMT103-V?qs=WyjlAZoYn51X2GCrrvGQTg==T) | 20$
---- | ---- | ---- | ----

The main features these encoders are:
 - Configurable number of impulses per revolution (PPR)
    - `48 - 2048`
 - Vey easy mounting
    - click-on to any type of shaft ` < 8mm`
 - Integrated pull-ups 
 - Index pin
 - Low-cost `~20$`

### BLDC driver
In this project I used the [SimpleFOCShield board](https://docs.simplefoc.com/arduino_simplefoc_shield_showcase) which is basically an Arduino shield which enables the usage of the FOC algorithm with the BLDC motors. It is practically plug & play in combination with the Arduino [SimpleFOC library](https://docs.simplefoc.com/arduino_simplefoc_library_showcase).

There are quiet a few boards out there that are in short a triple H-bridge motor drivers and the SimpleFOC library will be compatible with most of them. But for most of them the hardware setup will be a bit more complex. Here are some examples of the possible BLDC drives you could use:
Examples | Description | Link | Price
---- | ---- | ---- | ---
[<img src="https://docs.simplefoc.com/extras/Images/shield_top_v13.png" height="100px">](https://simplefoc.github.io/simplefoc_shield_product)| Arduino SimpleFOCShield | [More info](https://simplefoc.github.io/simplefoc_shield_product) | 25$
[<img src="https://docs.simplefoc.com/extras/Images/l6234.jpg" height="100px">](https://www.ebay.com/itm/L6234-Breakout-Board/153204519965?hash=item23abb3741d:g:LE4AAOSwe35bctgg) | Dorotek L6234 breakout board| [Drotek](https://store-drotek.com/212-brushless-gimbal-controller-l6234.html), [Ebay](https://www.ebay.fr/itm/L6234-Breakout-Board-/153204519965) | 30$
[<img src="https://docs.simplefoc.com/extras/Images/pinout.jpg" height="100px">](https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ:rk:1:pf:1) | HMBGC V2.2 | [Ebay](https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ:rk:1:pf:1) | 20$

## Table mount
There are surely many ways to mount this pendulum to the stable surface, in my case I hade on my hands a very robust vacuum suction cup holder which works perfectly for this application. It is not the cheapest solution though.
<img src="images/vacuum.jpg" height="100px"> | Delkin Fat Gecko | [Ebay](https://www.ebay.com/itm/Delkin-Fat-Gecko-Stealth-Vacuum-Light-Mount-Holds-4-LB-Free-US-Shipping/141019370583?hash=item20d568d457:g:O6wAAMXQXMRR6EZ7) | 30$
---- | ---- | ---- | ----

The only important thing is that the pendulum base is fixed, everything else is the design choice :D 


# Connecting all the components
In this project all the electronics components are:

[Arduino UNO](https://store.arduino.cc/arduino-uno-rev3) | [Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>](arduino_simplefoc_shield_showcase) | 2x [AMT 103 encoder](https://www.mouser.fr/ProductDetail/CUI-Devices/AMT103-V?qs=%2Fha2pyFaduiAsBlScvLoAWHUnKz39jAIpNPVt58AQ0PVb84dpbt53g%3D%3D) | [iPower GM4108-120T](https://shop.iflight-rc.com/index.php?route=product/product&product_id=217&search=GM4108H-120T)
--- | --- | --- | --- 
<img src="images/arduino_uno.jpg" width="150px"> |  <img src="images/shield_to_v13.jpg" width="150px">  | <img src="images/AMT_103.jpg" width="150px">  | <img src="images/mot.jpg" width="150px"> 

## Encoder 1 (motor)
- Channels `A` and `B` are connected to the encoder connector `P_ENC`, terminals `A` and `B`. 

## Encoder 2 (pendulum)
<blockquote > Pinout restriction<br>
Arduino UNO doesn't have enough hardware interrupt pins for two encoders therefore we need to use the software interrupt library. </blockquote>

- Encoder channels `A` and `B` are connected to the pins `A0` and `A1`.


## Motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector `TB_M1`

<blockquote>Alignment<br> Motor phases <code>a</code>,<code>b</code>,<code>c</code> and encoder channels <code>A</code> and <code>B</code> have to have the same orientation for the algorithm to work. But don't worry about it too much. Connect it initially as you wish and then if the motor locks in place reverse phase <code>a</code> and <code>b</code> of the motor, that should be enough.
</blockquote>


# Arduino code 
Let's go through the full code for this project and write it together.
First thing you need to do is include the `SimpleFOC` library:

```cpp
#include <SimpleFOC.h>
```
Make sure you have the library installed. If you still don't have it please check the [get started page](getting_started).

Also in this case, we are using two encoders so we will need to have a software interrupt library.
I would suggest using `PciManager` library. If you have not installed it yet, you can do it using the Arduino library manager directly. Please check the *SimpeFOClibrary*'s `Encoder` class [docs](https://simplefoc.github.io/Arduino-FOC/encoder) for more info.
So once you have it please include it to the sketch:
```cpp
// software interrupt library
#include <PciManager.h>
#include <PciListenerImp.h>
```

## Encoder 1 (motor) code 

First we define the `Encoder` class with the A and B channel pins and number of impulses per revolution.
```cpp
// define Encoder
Encoder encoder = Encoder(2, 3, 500);
```
Then we define the buffering callback functions.
```cpp
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}
```
In the `setup()` function we initialize the encoder and enable interrupts:
```cpp
  // initialize encoder hardware
  encoder.init();
  // hardware interrupt enable
  encoder.enableInterrupts(doA, doB);
```
And that is it, let's setup the pendulum encoder.

<blockquote class="info">For more configuration parameters of the encoders please check the *SimpeFOClibrary*'s <code>Encoder</code> class <a href="https://docs.simplefoc.com/encoder">docs</a>.</blockquote>


## Encoder 2 (pendulum) code
We define the pendulum as the `Encoder` class with the A and B channel pins and number of impulses per revolution.
```cpp
// define Encoder
Encoder pendulum = Encoder(A0, A1, 1000);
```
Then we define the buffering callback functions.
```cpp
// channel A and B callbacks
void doPA(){pendulum.handleA();}
void doPB(){pendulum.handleB();}
```
Next we define the `PciManager` pin change listeners:
```cpp
// pin change listeners
PciListenerImp listenerPA(pendulum.pinA, doPA);
PciListenerImp listenerPB(pendulum.pinB, doPB);
``` 
In the `setup()` function first we initialize the pendulum encoder:
```cpp
  // initialize encoder hardware
  pendulum.init();
```
And then instead of calling `pendulum.enableInterrupt()` function we use the `PciManager` library interface to attach the interrupts.
```cpp
  // interrupt initialization
  PciManager.registerListener(&listenerPA);
  PciManager.registerListener(&listenerPB);
```
And that is it the pendulum is ready, let's setup the motor.

## Motor code
First we need to define the `BLDCMotor` class with the number of pole pairs(`11`) of the motor.

```cpp
// define BLDC motor
BLDCMotor motor = BLDCMotor(11);
```
<blockquote class="warning">If you are not sure what your pole pairs number is please check the  <code>find_pole_pairs.ino</code> example.</blockquote>

Next we need to define the `BLDCDriver3PWM` class with the PWM pin numbers and the driver enable pin.
```cpp
// define BLDC driver
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8);
```

Then in the `setup()` we configure first the voltage of the power supply if it is not `12` Volts and initialize and link the driver.
```cpp
  // power supply voltage
  // default 12V
  driver.voltage_power_supply = 12;
  driver.init();
  motor.linkDriver(&driver);
```
Then we tell the motor which control loop to run by specifying the `motor.controller` variable.
```cpp
  // set control loop type to be used
  // MotionControlType::torque
  // MotionControlType::velocity
  // MotionControlType::angle
  motor.controller = MotionControlType::torque;
```
<blockquote class="info">For more information about the voltage control loop please check the  <a href="voltage_loop">doc</a>.</blockquote>

Next we connect the encoder to the motor, do the hardware init and init of the Field Oriented Control.
```cpp  
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // initialize motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();
```
The last peace of code important for the motor is of course the FOC routine in the `loop` function.
```cpp
void loop() {
  // iterative FOC function
  motor.loopFOC();

  // iterative function setting and calculating the angle/position loop
  // this function can be run at much lower frequency than loopFOC function
  motor.move(target_voltage);
}
```
Now we are able to read the two encoders and set the voltage to the motor, now we need to write the stabilization algorithm.
<blockquote class="info">For more configuration parameters and control loops please check the *SimpleFOClibrary*'s <code>BLDCMotor</code> class <a href="https://docs.simplefoc.com/motor_initialization">doc</a>.</blockquote>

## Control algorithm code

The control algorithm is divided in two stages. Stabilization and swing-up.

#### Stabilization

In order to stabilize the pendulum we will be using a state space controller which means that it takes in consideration all three variables important for this pendulum system:
- pendulum angle - `p_angle`
- pendulum velocity - `p_vel`
- motor velocity - `m_vel`

The controller code is very simple at the end, it just calculates the linear control rule:
```cpp
target_voltage =  40*p_angle + 7*p_vel + 0.3*m_vel;
```
The gains `40`,`7` and `0.3` you can imagine as weights, which tell how much we care about these variables. The highest weight is obviously on the pendulum angle and the smallest is on motor velocity that makes sense. Basically if we set `0` to the motor velocity weight, your pendulum will still be stable but your motor will probably never stop spinning. It will always have some velocity. On the other hand if you put it much higher, you will probably prioritize your motor movements over the stability and your pendulum will no longer be stable. So there is a tradeoff here. 

This is a very simple explanation of a relatively complex topic and I would like to point you toward a nice [youtube video](https://www.youtube.com/watch?v=E_RDCFOlJx4) explanation of similar approaches.

Also maybe interesting to say is that for a system like this one there is really no need to run it with the sample times less then 20ms. In my case I have run it at ~25ms, but you can go even to 50ms.

<blockquote class="warning"><b>NOTE</b><br> The FOC algorithm <code>motor.loopFOC()</code> will run ~1ms but the control algorithm and the function <code>motor.move()</code> will be downsampled to ~25ms.</blockquote>

#### Swing-up

The swingup implemented in this example is the simples one possible, that is always good, it means that the hardware is well designed so you dont need to make some fancy algorithm to make it work :D

This is hte code of the swing-up:
```cpp
target_voltage = -_sign(pendulum.getVelocity())*motor.voltage_power_supply*0.4;
```
What it does really is it checks which direction the pendulum is moving `sign(pendulum.getVelocity())` and sets the very high voltage value `motor.voltage_power_supply*0.4` in the opposite direction (`-`). 
It means that the algorithm is going to try to accelerate the movement of the pendulum (because the pendulum acceleration is caused as the reaction of the motor acceleration, but inverse direction).
The voltage value you are setting is something you will tune. I have found that for my pendulum 40% of the maximum voltage was enough to make the pendulum swing up. More voltage would make it swing up too fast and the pendulum would not be able to stabilize when it reaches the top. Much less voltage would not be enough for the pendulum to swing up at all.

#### The integration

Now we jsut need to decide when do we do the swing up and when do we do the stabilization. Basically we need to decide the angle from which we decide that it is not possible to recover and we should proceed with the swing-up. 
I my case I have decided it is `0.5 radians`, `~30degrees`. 

So the full control algorithm code looks like this:
```cpp
// control loop each ~25ms
  if(loop_count++ > 25){
    
    // calculate the pendulum angle 
    float pendulum_angle = constrainAngle(pendulum.getAngle() + M_PI);

    float target_voltage;
    if( abs(pendulum_angle) < 0.5 ) // if angle small enough stabilize
      target_voltage =  40*pendulum_angle + 7*pendulum.getVelocity() + 0.3*motor.shaftVelocity();
    else // else do swing-up
      // sets 40% of the maximal voltage to the motor in order to swing up
      target_voltage = -_sign(pendulum.getVelocity())*motor.voltage_power_supply*0.4;

    // set the target voltage to the motor
    motor.move(target_voltage);

    // restart the counter
    loop_count=0;
  }
```
And that is it guys we can read our pendulum angle, we can control the motor, and we have our control algorithm. Lets write the full code! 

## Full Arduino code

For the full code I have just added a function `constrainAngle()` to constrain the pendulum angle in between -180 and 180 degrees and moved the stabilization part of the controller to the stand-alone function `controllerLQR()`.


```cpp
#include <SimpleFOC.h>
// software interrupt library
#include <PciManager.h>
#include <PciListenerImp.h>


// BLDC motor init
BLDCMotor motor = BLDCMotor(11);
// driver instance
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8);
//Motor encoder init
Encoder encoder = Encoder(2, 3, 500);
// interrupt routine 
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}


// pendulum encoder init
Encoder pendulum = Encoder(A1, A2, 1000);
// interrupt routine 
void doPA(){pendulum.handleA();}
void doPB(){pendulum.handleB();}
// PCI manager interrupt
PciListenerImp listenerPA(pendulum.pinA, doPA);
PciListenerImp listenerPB(pendulum.pinB, doPB);

void setup() {
  
  // initialise motor encoder hardware
  encoder.init();
  encoder.enableInterrupts(doA,doB);
  
  // init the pendulum encoder
  pendulum.init();
  PciManager.registerListener(&listenerPA);
  PciManager.registerListener(&listenerPB);
  
  // set control loop type to be used
  motor.controller = MotionControlType::torque;

  // link the motor to the encoder
  motor.linkSensor(&encoder);
  
  // driver
  driver.voltage_power_supply = 12; 
  driver.init();
  // link the driver and the motor
  motor.linkDriver(&driver);

  // initialize motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();
}

// loop downsampling counter
long loop_count = 0;

void loop() {
  // ~1ms 
  motor.loopFOC();

  // control loop each ~25ms
  if(loop_count++ > 25){
    // updating the pendulum angle sensor
    // NECESSARY for library versions > v2.2 
    pendulum.update();
    // calculate the pendulum angle 
    float pendulum_angle = constrainAngle(pendulum.getAngle() + M_PI);

    float target_voltage;
    if( abs(pendulum_angle) < 0.5 ) // if angle small enough stabilize
      target_voltage = controllerLQR(pendulum_angle, pendulum.getVelocity(), motor.shaftVelocity());
    else // else do swing-up
      // sets 40% of the maximal voltage to the motor in order to swing up
      target_voltage = -_sign(pendulum.getVelocity())*motor.voltage_limit*0.4;

    // set the target voltage to the motor
    motor.move(target_voltage);

    // restart the counter
    loop_count=0;
  }
   

}

// function constraining the angle in between -pi and pi, in degrees -180 and 180
float constrainAngle(float x){
    x = fmod(x + M_PI, _2PI);
    if (x < 0)
        x += _2PI;
    return x - M_PI;
}

// LQR stabilization controller functions
// calculating the voltage that needs to be set to the motor in order to stabilize the pendulum
float controllerLQR(float p_angle, float p_vel, float m_vel){
  // if angle controllable
  // calculate the control law 
  // LQR controller u = k*x
  //  - k = [40, 7, 0.3]
  //  - x = [pendulum angle, pendulum velocity, motor velocity]' 
  float u =  40*p_angle + 7*p_vel + 0.3*m_vel;
  
  // limit the voltage set to the motor
  if(abs(u) > motor.voltage_limit*0.7) u = sign(u)*motor.voltage_limit*0.7;
  
  return u;
}
```
This is the full code guys. You can also find it in the `Arduino` directory of the git repo. 

I hope you found this readme (even though very long) helpful and I hope you will be successful in realizing this project as well. It is very very cool! 

# Contact
Feel free to leave the issue if you experience any problems setting up this project!
