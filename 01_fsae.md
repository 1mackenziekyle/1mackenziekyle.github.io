---
layout: page
title: FSAE
permalink: /torque-vectoring/
---

# UBC Formula Electric - Quad-motor torque vectoring algorithm 
*Sept. 2024 - Present* 

Researching vehicle modelling and controls algorithms for slip control, yaw rate controllers, and power efficiency modules.

One of the main challenges of this project is getting accurate sensor data, which requires data fusion approaches from multiple sensors. We are currently looking into implementing a complimentary filter using an IMU and GPS, and moving onto an Extended Kalman Filter if the complementary filter does not give satisfactory results. 


# UBC Formula Electric: Torque Vectoring Drive Algorithm 
*2023 - 2024* 


![picture 1](media/c22f49f8fb6b99d68cafc8f31db64a142c95411c8fe8ccecd3ccf0c5e1ef9d88.png)  

The goal of this project was to completely revamp the team's driving algorithm and to improve the car's performance 
in driving events at the FSAE Formula Electric competition. We went into this project looking to utilize the most recent research being done on
electric motor vehicles, and apply our knowledge of control theory to create an algorithm that utilized all the available power the vehicle could deliver in the most efficient way possible for all the different driving events. 

The project was split into three main modules: 

1. Power limiting 
2. Software active differential 
3. Traction Control

####  1. Power Limiting Module

The goal of this module was to calculate the absolute most available power to the car at any given point, while taking in safety considerations including the temperature limits of our motors and inverters, the state of charge of our battery pack, and of course the driver's inputs, e.g. steering wheel angle and accelerator/brake pedal positions. The limiting factor for power limit is often the battery's state of charge (SOC). We cannot use regenerative braking too much at high SOC, and we can't pull too much power from the battery at low SOC, due to voltage sagging under high discharge currents.

#### 2. Software Active Differential 

This module takes in the steering wheel angle as input, and modulates the left-to-right motor torques to improve the cornering behavior of the car. By applying different torques to each side, we can apply a net moment on the car, causing an angular acceleration in the yaw axis to help us steer around the corner. Additionally, we can take advantage of the traction benefits of the car rolling and causing an increased downforce on the outside wheels of the car.

![picture 2](media/5c7c8663969ffe99bddc1840975ae27b93877c60bee5531a2cc3db1bc2c2a256.png)  

![picture 3](media/576b8e67f3ea7d8eb861f3be09261e5d90aed0cc396bea8e645b7927df15369c.png)  

From *Draou 2013*, we substitute wheel speeds $\omega_{L,R}$ for torque requests $T_{L,R}$ 

#### 3. Traction Control 

This module's job is to detect when we are slipping and reallocate torques to maximize traction with the ground to stabilize the car. You can think of it as a PID loop that is trying to set the slip ratio of the rear wheels with a value near zero. 

