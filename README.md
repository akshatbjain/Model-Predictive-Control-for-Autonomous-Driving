# Model Predictive Control for Autonomous Driving

This project implements Model Predictive Control for an autonomous car in a Udactiy provided simulator. It uses websocket to communicate telemetry, track waypoint data, steering and acceleration commands from and to the simulator. The simulator also has a 100ms latency as one may encounter in the real-world scenario.

Ipopt and cppad libraries are used to calculate an optimal trajectory and the correspoinding actuation commands. The aim is to minimize the error using a third-degree polynomial fit to the given waypoints. As seen in the video (link below), only a short duration worth of waypoints are considered at any given time (green line). A trajectory is generated for that duration based on the vehicle's kinematics, a cost function based on the vehicle's cross-track error and the orientation angle error.

## Implementation Details

* The kinematic model includes:
  * the vehicle's x coordinate,
  * the vehicle's y coordinate,
  * orientation angle (psi), 
  * velocity,
  * the cross-track error, and 
  * psi error (epsi)
  
  Actuator outputs are acceleration and steering angle (delta). The model combines the state and actuations from the previous timestep to calculate the state for the current timestep based on the equations below:

![equations](https://github.com/akshatbjain/Model-Predictive-Control-for-Autonomous-Driving/blob/master/equations.png)

* The values chosen for Timestep Length (N) and Elapsed Duration (dt) are 10 and 0.1, respectively. These values mean that the optimizer is considering a one-second duration in which to determine a corrective trajectory. Adjusting either N or dt (even by small amounts) often produced erratic behavior. These values were attained after a lot of experimentation.

* The waypoints are preprocessed by transforming them to the vehicle's perspective. This simplifies the process to fit a polynomial to the waypoints because the vehicle's x and y coordinates are now at the origin (0, 0) and the orientation angle is also 0.

* The following is done to deal with latency: the original kinematic equations depend upon the actuations from the previous timestep, but with a delay of 100ms. The actuations are applied another timestep later, so the equations have been altered to account for this. Also, in addition to the cost functions suggested in the lessons (punishing CTE, epsi, difference between velocity and a reference velocity, delta, acceleration, change in delta, and change in acceleration) an additional cost penalizing the combination of velocity and delta was included. This results in much more controlled cornering.

## Results

[Video](https://youtu.be/uOLImtM4NcQ)
