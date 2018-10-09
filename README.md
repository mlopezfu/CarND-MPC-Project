# Udacity Self-Driving Car Engineer Nanodegree Program
# *Model Predictive Controller Project*

## Intro

This repository contains the solution to the Udacity MPC Project. The goal of this project is to navigate a track in a Udacity-provided [simulator](https://github.com/udacity/self-driving-car-sim/releases), which communicates telemetry and track waypoint data via websocket, by sending steering and acceleration commands back to the simulator. The solution must be robust to 100ms latency, as one may encounter in real-world application.

This solution, as the Nanodegree lessons suggest, makes use of the IPOPT and CPPAD libraries to calculate an optimal trajectory and its associated actuation commands in order to minimize error with a third-degree polynomial fit to the given waypoints. The optimization considers only a short duration's worth of waypoints, and produces a trajectory for that duration based upon a model of the vehicle's kinematics and a cost function based mostly on the vehicle's cross-track error (roughly the distance from the track waypoints) and orientation angle error, with other cost factors included to improve performance. 

## Result
As a final result, after a lot of testing, we can give a lot of laps with stable riding.

Finally, we can see the result of the first lap of the final configuration.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=5O-0dkt97yY
" target="_blank"><img src="http://img.youtube.com/vi/5O-0dkt97yY/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="600" border="10" /></a>

## Rubric Points

- **The Model**: *Student describes their model in detail. This includes the state, actuators and update equations.*

The kinematic model includes the vehicle's x and y coordinates, orientation angle (psi), and velocity, as well as the cross-track error and psi error (epsi). Actuator outputs are throttle(a) and steering angle(delta). The model combines the state and actuations from the previous timestep to calculate the state for the current timestep based on the following equations:

![equations](./images/equations.png)

In order to achieve the main goal of this project and to drive the vehicle safely and smootly into the track lanes, we needed to use costs. 

The following values were chosen as cost hyperparameters.

```C++
int cost_cte_factor = 10;//2000;
int cost_epsi_factor = 10;//2000;
int cost_v_factor = 10;//1;
int cost_current_delta_factor = 1000;//1100;
int cost_current_a_factor = 10;//10;
int cost_diff_delta_factor = 100;//100;
int cost_diff_a_factor = 10;//10;
double ref_cte = 0;
double ref_epsi = 0;
double ref_v = 20;
```


- **Timestep Length and Elapsed Duration (N & dt)**: *Student discusses the reasoning behind the chosen N (timestep length) and dt (elapsed duration between timesteps) values. Additionally the student details the previous values tried.*

The values chosen for N and dt are 20 and 0.1, respectively. These values mean that the optimizer is considering a two second duration in which to determine a corrective trajectory and making actuation each 100 ms. Adjusting either N or dt (even by small amounts) often produced erratic behavior. Other values tried include:

 N: 10  dt: 0.10 
 Where the car has some zigzag driving. It stays on the road, but its not very stable.
 
 <a href="http://www.youtube.com/watch?feature=player_embedded&v=Dr2biNf4b3Q
" target="_blank"><img src="http://img.youtube.com/vi/Dr2biNf4b3Q/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="600" border="10" /></a>
 
 N: 20 dt: 0.05
 Where the car has still more agressive driving, and can´t follow any portion of a straight road without overshooting.
 
 <a href="http://www.youtube.com/watch?feature=player_embedded&v=Mh3PYqfDgM0
" target="_blank"><img src="http://img.youtube.com/vi/Mh3PYqfDgM0/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="600" border="10" /></a>

and many others. But the best performance, and the only one who let the car give a lot of laps without problems was the selected one of N=20 and dt=0.1.

- **Polynomial Fitting and MPC Preprocessing**: *A polynomial is fitted to waypoints. If the student preprocesses waypoints, the vehicle state, and/or actuators prior to the MPC procedure it is described.*

The waypoints are preprocessed by transforming them to the vehicle's perspective (see main.cpp lines 112-125). This simplifies the process to fit a polynomial to the waypoints because the vehicle's x and y coordinates are now at the origin (0, 0). 

- **Model Predictive Control with Latency**: *The student implements Model Predictive Control that handles a 100 millisecond latency. Student provides details on how they deal with latency.*

The approach to dealing with latency was to use calculate the following data at the instant latency_dt in the future, so we can calculate the future error that will be when the actuator makes the movement. (see main.cpp lines 99-101 and 134-153)

We also use the cost functions suggested in the lessons (punishing CTE, epsi, difference between velocity and a reference velocity, delta, acceleration, change in delta, and change in acceleration).

In the main.cpp (line 212) file there is included a 100 ms delay to model the real delay that would be in the real car actuators, because the simulator can react in a different way.


## Technical difficulties

It has been difficult to make the project work on a mac. Thanks to [Dario Cazzani](https://discussions.udacity.com/u/dariocazzani) for their Docker instructions that save my life and let me compile with the IPOPT and CPPAD libraries.
