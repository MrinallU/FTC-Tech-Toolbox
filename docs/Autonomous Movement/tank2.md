---
sidebar-position: 3
---

# Tank Drive (Part 2)

:::notes Resources
* [CTRL-ALT-FTC](https://www.ctrlaltftc.com/practical-examples/drivetrain-control#differential-drivetrain-controller) - **Must Read**
* Mecanum Drive (Part 2)
* Localization
:::

We will now be creating a far more accurate movement system for our tank robot, which if done correctly, should look something like this:
<iframe width="100%" height="422" src="https://www.youtube.com/embed/F2axTpwH678" title="FTC #19376 Differential Drive Controller test" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For the various benefits of using this type of movement controller as opposed to the previous controller mentioned in part one, please refer to Mecanum Drive part 2. 

In general, this controller involves feeding in the coordinate point the robot must travel to and the desired heading of the robot. Unlike a mecanum robot, a tank drive system cannot move sideways, meaning we cannot simultaneously move to the desired point and turn to the desired angle. This means the robot must reach the desired (x,y) point before turning to the desired heading.

As mentioned in the mecanum section we will be making use of a positional feedback controller that relies on our odometry localization to check the controller's status. By continuously analyzing the error between the goal point and the robot's current position we can power the robot's motors in such a way that it is driven toward the destination point. Given that your localization system is accurate, this results in improved accuracy and consistency in the final position of the robot.

The math behind this controller is explained very well in [CTL ALT FTC](https://www.ctrlaltftc.com/practical-examples/drivetrain-control#differential-drivetrain-controller), which we have linked below: 

## Implementation
Although the implementation found in CTRL ALT FTC is of high quality it does not show how to terminate the loop so that the robot can complete other tasks. 

To do this we follow the same concept seen in the mecanum part 2 module. If both the position and angle threshold has been reached then we can exit from the loop. However, if there is another type of error we need another method of exiting the controller, this is where the timeout method comes into play. 

**We are going to assume that you know how to implement a PID controller by now, make sure to use different coefficients for the distance and angle controller.**

```java 
double timeout = 5000; // 5000 ms or 5 seconds
boolean loopIsActive = true;
double threshold = 2, angleThreshold = 2; // 2 inches and 2 degrees of error allowed 
distanceController = new PID();
angleController = new PID();  // make sure this follows "Dealing with Angles" 
ElapsedTime timer = new ElapsedTime();

public void setRobotRelative(f,t){
    frontLeftMotor.setPower(f + t);
    backLeftMotor.setPower(f + t);
    frontRightMotor.setPower(f - t);
    backRightMotor.setPower(f - t);
}

while (loopIsActive && timer.milliseconds() <= timeout) {
    double xError = targetX - robotX;
    double yError = targetY - robotY; 
    double theta = Math.atan2(yError,xError);
    // 0 is the reference because we want the distance to go to 0 
    double distance = Math.hypot(xError, yError);
    double f, t; // forward power, turn power
    if (distance < threshold) { // assuming the coordinate position is correct.... 
         f = 0;
         t = angleController.calculate(targetAngle, robotTheta);      
         double angleError = targetAngle - robotTheta; // check angle error. 
         if(Math.abs(angleError) < angleThreshold){
             loopIsActive = false;  // break when the condition is reached. 
         }
    } else {
        f = distanceController.calculate(0, distance); 
        t = angleController.calculate(theta, robotTheta);
    }
    // Range.clip is included in the SDK and will clip between two values
    // angleController.error is a demonstrative attribute that gets the error. 
    f *= Math.cos(Range.clip(angleController.error, -PI/2, PI/2));
    
    // set motor power here! 
    setRobotRelative(f,t);    
}
```

