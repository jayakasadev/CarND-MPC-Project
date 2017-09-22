# Model Predictive Control

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

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Fortran Compiler
  * Mac: `brew install gcc` (might not be required)
  * Linux: `sudo apt-get install gfortran`. Additionall you have also have to install gcc and g++, `sudo apt-get install gcc g++`. Look in [this Dockerfile](https://github.com/udacity/CarND-MPC-Quizzes/blob/master/Dockerfile) for more info.
* [Ipopt](https://projects.coin-or.org/Ipopt)
  * If challenges to installation are encountered (install script fails).  Please review this thread for tips on installing Ipopt.
  * Mac: `brew install ipopt`
       +  Some Mac users have experienced the following error:
       ```
       Listening to port 4567
       Connected!!!
       mpc(4561,0x7ffff1eed3c0) malloc: *** error for object 0x7f911e007600: incorrect checksum for freed object
       - object was probably modified after being freed.
       *** set a breakpoint in malloc_error_break to debug
       ```
       This error has been resolved by updrading ipopt with
       ```brew upgrade ipopt --with-openblas```
       per this [forum post](https://discussions.udacity.com/t/incorrect-checksum-for-freed-object/313433/19).
  * Linux
    * You will need a version of Ipopt 3.12.1 or higher. The version available through `apt-get` is 3.11.x. If you can get that version to work great but if not there's a script `install_ipopt.sh` that will install Ipopt. You just need to download the source from the Ipopt [releases page](https://www.coin-or.org/download/source/Ipopt/).
    * Then call `install_ipopt.sh` with the source directory as the first argument, ex: `sudo bash install_ipopt.sh Ipopt-3.12.1`. 
  * Windows: TODO. If you can use the Linux subsystem and follow the Linux instructions.
* [CppAD](https://www.coin-or.org/CppAD/)
  * Mac: `brew install cppad`
  * Linux `sudo apt-get install cppad` or equivalent.
  * Windows: TODO. If you can use the Linux subsystem and follow the Linux instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions


1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.
