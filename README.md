# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   
### Simulator.
You can download the Term3 Simulator which contains the Path Planning Project from the [releases tab (https://github.com/udacity/self-driving-car-sim/releases).

### Goals
In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. You will be provided the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 50 m/s^3.

## Implementation

The main parts of the Path Planner are the following:

1. Find lines status (free, busy).
2. Decide line change (change left, right, keep lane)
3. Speed control (accelerate, keep or deaccelerate)
4. Define X,Y path points

### Find Lines Status (free, busy)

We iterate through the sensor fusion data to get others vehicles position and find out if vehicle's is in which line and within a minimum distance.  This information will flag to "not cleared" each of the 3 available lines (left, front and right).  Depending on actual car's line it will flag it to "not cleared" also, i.e. if car is already in right lane, we flag as "not cleared" the right line.
- left_lane_clear --> true, flase
- right_lane_clear --> true, false
- my_lane_clear --> true, false

We check for a minimum of safe distance to 24m. 

Improvements: 
- left, right clear should also check for those cars which may be out of the safe distance but with much higher speed than our vehicle.

### Decide Line change (change left, right, keep lane)

Once we know the status of the different lines (right, left and my lane) we can decide what to do.  We use the following logic:

* If cars in front and within minimum distance:
  * If I'm in center lane
    *  If left line clear --> Go to left  (car prefers to pass a car from its left)
    *  If only right line clear --> Go to right 
    *  If no right, left lines are clear then --> keep lane (follow car)
  * If I'm in left
    * If right line clear --> Go to right
    * Otherwise keep line (follow car)
  * If I'm in right
    * If left line clear --> Go to left
    * Otherwise keep line (follow car)

* No cars in front:
  * If car is in middle or left line and right line is clear --> go to right (**)

As can be seen in the logic, we try to be at right lines whenever possible.

### Speed Control (accelerate, keep or deaccelerate)

As default target velocity is close to speed limit, we set it at 49 mpg.  If we are following a car then we deaccelerate (by decreasing the target speed) in case our speed is bigger than the front car's speed.  

### Define X,Y path points.

This is as shown in the Project Walkthough Video.  Change to Frenet coordinates (s,d) and using spline function to generate evenly spaced points.  This will result in a smooth trajectory which will minimize "jerk".


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.


## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
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


