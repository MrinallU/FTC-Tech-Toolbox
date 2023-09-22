---
sidebar_position: 4
---
# Mecanum (No Deadwheels)
:::info Resources
* This method is derived from the following paper: [Kinematic Model of a Four Mecanum Wheeled Mobile Robot](https://research.ijcaonline.org/volume113/number3/pxc3901586.pdf)
* All angle calculations are done in radians.
* Note that the only manually calibrated value you will need is the number of ticks your encoder reads after the robot travels an inch.
  * An easy way of calculating this value is by slowly driving your robot forward by 1 foot and diving your motor's encoder reading by 12.
* After conducting testing, we found that replacing the angle calculated through odometry with the angle calculated through the IMU significantly improves accuracy. Consequently, the implementation will solely rely on the IMU angle.
  * Nevertheless, you can refer to equation 24 in the aforementioned paper to determine the methodology for deriving the angle based on drive encoder velocities.
:::

This specific odometry technique involves using encoders attached to the drive motors of our robot to capture the individual velocities of the drive motors. We then input these values into the kinematic model of a mecanum robot, which consists of a set of equations that provide the overall velocities of the robot. By applying basic physics principles, we continuously accumulate these velocities over time to determine the position of the robot.

The step-by-step mathematical process is outlined below:

Firstly we will attain the individual velocities of our drive motors: 

```java 
fLeftMotor.retMotorEx().getVelocity() // repeat for the other four motors
```

We will represent these velocities mathematically as follows: 
* $w1$: Front left motor velocity
* $w2$: Front right motor velocity 
* $w3$: Back left motor velocity
* $w4$: Back right motor velocity
* $r$: wheel radius

We will then plug these velocities into the equations provided by the paper. Note that the derivation for these equations is rather extensive. As a result, we will be skipping over them. However, we encourage you to read through the paper to learn where these equations come from. 

$$
v(t) = (w_1+w_2+w_3+w_4) * \frac{r}{4}
$$

```java 
double xV = (fLeftMotor.retMotorEx().getVelocity() + fRightMotor.retMotorEx().getVelocity()
            + bLeftMotor.retMotorEx().getVelocity() + bRightMotor.retMotorEx().getVelocity()) * (r/((double)4)));

double yV =  (-fLeftMotor.retMotorEx().getVelocity() + fRightMotor.retMotorEx().getVelocity()
            + bLeftMotor.retMotorEx().getVelocity() - bRightMotor.retMotorEx().getVelocity()) * (r/((double)4)));
```

These formulas assume that the robot doesn't change in angle. To account for this we must rotate the velocity vectors by the robot's current angle, which is attained from the IMU. 

$$
v_x' = v_xcos(\theta) - v_ysin(\theta)
$$
$$
v_y'= v_xsin(\theta) + v_ycos(\theta)
$$

```java
double nx = (xV*Math.cos(Math.toRadians(getAngle())))-(yV*Math.sin(Math.toRadians(getAngle())));

double nY = (xV*Math.sin(Math.toRadians(getAngle())))+(yV*Math.cos(Math.toRadians(getAngle())));
```

It is a commonly known concept that integrating velocity over time yields the distance traveled during that timeframe. By keeping track of the elapsed time between function calls and multiplying it by the calculated velocity, we can obtain the distance covered. Accumulating this value into a running sum allows us to determine the current position of the robot.

:::info
For those not familiar with integration:

Integration is like adding up small pieces to find the total. Let's say you have a line with a bunch of dots on it. Each dot represents a number. When you integrate, you're adding up all those numbers to find the sum.

For example, imagine you have a line with dots representing the speed of a moving car at different times. By adding up all the speeds at each moment, you can find out how far the car has traveled in total.

Integration helps us find the total when we have lots of small pieces that we want to combine together. It's like counting all the dots on the line to know the whole story.
:::

$$
  x = \int_{0}^{t} v_x(t) \,dx 
$$
$$
y = \int_{0}^{t} v_y(t) \,dy
$$

Note that this value only returns the position in encoder ticks, to convert to inches we must divide the value by our manually calibrated tick-to-inch conversion factor. 

```java 
double conversionFactor = 162.15; 
curPose.yP+=(yV*(driveTime.seconds()-prevTime))/conversionFactor; // <-- Tick to inch conversion factor
curPose.xP+=(xV*(driveTime.seconds()-prevTime))/conversionFactor;
prevTime = driveTime.seconds();
```

## Implementation

```java 
double conversionFactor = 162.15, prevTime = 0;  
ElapsedTime driveTime = new ElapsedTime(); 

    // https://research.ijcaonline.org/volume113/number3/pxc3901586.pdf
public void updatePositionEncoderOdoBackup(){
    // apply mecnaum kinematic model (with wheel velocities [ticks per sec])
   double xV = (fLeftMotor.retMotorEx().getVelocity() + fRightMotor.retMotorEx().getVelocity()
           + bLeftMotor.retMotorEx().getVelocity() + bRightMotor.retMotorEx().getVelocity()) * 0.5;
   
   double yV =  (-fLeftMotor.retMotorEx().getVelocity() + fRightMotor.retMotorEx().getVelocity()
           + bLeftMotor.retMotorEx().getVelocity() - bRightMotor.retMotorEx().getVelocity()) * 0.5;

   // rotate the vector
   double nx = (xV*Math.cos(Math.toRadians(getAngle())))-(yV*Math.sin(Math.toRadians(getAngle())));
   double nY = (xV*Math.sin(Math.toRadians(getAngle())))+(yV*Math.cos(Math.toRadians(getAngle())));
   xV = nx; yV = nY;

   // integrate velocity over time
   curPoseY +=(yV*(driveTime.seconds()-prevTime))/conversionFactor; // <-- Tick to inch conversion factor
   curPoseX +=(xV*(driveTime.seconds()-prevTime))/conversionFactor;
   prevTime = driveTime.seconds();
}
```

