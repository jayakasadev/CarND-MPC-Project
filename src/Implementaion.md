# The Model
For this project I made use of the kinematic model.
I was given the vehicle's position(x and y coordinates), orientation angle (psi), and velocity.
In addition, I recieved 6 coordinates from my desired path.
Using these values, I calculated the cross-track error(CTE) and psi error (EPSI). 

### Equations
The model combines the state and actuations from the previous timestep to calculate the state for the current timestep:	

x​t+1​​=x​t​​+v​t​​∗cos(ψ​t​​)∗dt

y​t+1​​=y​t​​+v​t​​∗sin(ψ​t​​)∗dt

ψ​t+1​​=ψ​t​​+​L​f​​​​v​t​​​​∗δ​t​​∗dt

v​t+1​​=v​t​​+a​t​​∗dt

cte​t+1​​=f(x​t​​)−y​t​​+(v​t​​∗sin(eψ​t​​)∗dt)

eψ​t+1​​=ψ​t​​−ψdes​t​​+(​L​f​​​​v​t​​​​∗δ​t​​∗dt)

Using the above model, I computed the Actuator outputs(acceleration and steering angle). 

# Timestep Length and Elapsed Duration (N & dt)
The values chosen for N and dt are 10 and 0.1, respectively.
dt was set to 0.1 because the latency was 100ms.  
Choosing N was much more difficult. 
I started with 25 from the quizzes and worked my way down.
Values over 12 caused erratic behavior such as spin outs.
With N = 10 and dt = 0.1, the optimizer is only determining a corrective trajectory for a 1 second window.`

# Polynomial Fitting and MPC Preprocessing
The incoming map coordinates are transformed to the vehicle's perspective. 
This simplifies the process of fitting a polynomial to the coordinates because the vehicle's x and y coordinates are now at the origin (0, 0) and the orientation angle is also zero.

# Model Predictive Control with Latency
The approach to dealing with latency has multiple parts:
* Limiting Speed
* Modifying the original Kinematic equations
  * The original kinematic equations depend upon the actuations from the previous timestep. 
  Due to a latency of 100ms, and because we calculate for 100ms intervals, I modified the equations to look back at t-2 instead of t-1.
* Temporal and Acceleration Smoothing
  * This kept sequential steering and acceleration values closer together. 