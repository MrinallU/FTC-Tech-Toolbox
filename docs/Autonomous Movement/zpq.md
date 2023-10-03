---
sidebar-position: 3
---

# Pure Pursuit: Mecanum

:::caution
Note that this module assumes that you have watched Gluten-free's pure pursuit tutorial linked in the previous module.
:::

:::note Prerequisite 
* [Mecanum Drive Part 2](https://ftc-tech-toolbox.vercel.app/docs/Autonomous%20Movement/mec2)
::: 

Note that the video tutorials `moveToPosition` function only outputs the desired x and y powers for the robot to reach its final position. We need to convert these values into values we can input into the drive motors while accounting for the robot's angular orientation. Luckily field centric driving does precisely this. 

The tutorial also assumes that you have already implemented a localizer for your robot. If you have not done this yet, look at the localization section. 

Also, note that we make use of the 3 PID controller method to move the robot (shown in Mecanum Drive part 2), however, you can also replace this with the method shown in the video tutorial. Regardless you will still have to make use of the `driveFieldCentric` function to make the robot correctly follow the given velocities. 

Note the lack of a while-loop in this `movetoPosition` function. This your because the pure pursuit controller continuously updates the destination point so the robot follows the given path smoothly, essentially acting as a feedback loop that terminates when the robot is at the end of its path. Here, the `moveToPosition` function's purpose is to push the robot in the right direction. If we were to wait for the robot to reach its lookahead position by adding a while-loop in this function the path would not be smooth. 

:::info
For brevity, we have only implemented the proportional aspects of the motion controller. You should try implementing the integral and derivative components on your own! 
* Here's a quick hint: you should only be using the integral and derivative components if the robot is following the final point on the curve!
:::

```java 
double movementP = 0.05, 
angleP = 0.01;

public void moveToPosition(double targetXPos, double targetYPos, double targetAngle){
    resetCache();
    odometry.updatePosition();
    
    double xDiff = targetXPos - odometry.getX();
    double yDiff = targetYPos - odometry.getY();
    double angleDiff = normalizeAngle(odometry.getAngle() - targetAngle);
      
    driveFieldCentric(movementP*yDiff), angleP*angleDiff, movementP*xDiff);

    driveFieldCentric(0, 0, 0);
}


public void driveFieldCentric(double drive, double turn, double strafe) {
      // https://gm0.org/en/latest/docs/software/tutorials/mecanum-drive.html#field-centric
      double fRightPow, bRightPow, fLeftPow, bLeftPow;
      double botHeading = -Math.toRadians(normalizeAngle(odometry.getAngle()));
      System.out.println(drive + " " + turn + " " + strafe);

      double rotX = drive * Math.cos(botHeading) - strafe * Math.sin(botHeading);
      double rotY = drive * Math.sin(botHeading) + strafe * Math.cos(botHeading);

      double denominator = Math.max(Math.abs(strafe) + Math.abs(drive) + Math.abs(turn), 1);
      fLeftPow = (rotY + rotX + turn) / denominator;
      bLeftPow = (rotY - rotX + turn) / denominator;
      fRightPow = (rotY - rotX - turn) / denominator;
      bRightPow = (rotY + rotX - turn) / denominator;

      setDrivePowers(bLeftPow, fLeftPow, bRightPow, fRightPow);
}

  // Remaps the given angle into the range (-180, 180].
  public static double normalizeAngle(double degrees) {
    double normalized_angle = Angle.normalizePositive(degrees);
    if (normalized_angle > 180) {
      normalized_angle -= 360;
    }
    return normalized_angle;
  }
```

## Solving the Endpoint Issue

In gluten free's tutorial, there is an error in the pure pursuit code which prevents loop termination when the robot reaches the end of the path. We can easily fix this by using the distance threshold idea mentioned in previous modules. 

We know that the generated path must have a final point at which the robot must stop. Therefore we can figure out when we can end the pure pursuit loop by continuously checking if the robot's current position is at a close enough distance to the desired destination position. 

```java 
ArrayList<CurvePoint> allPoints = new ArrayList<>(); 
double desiredAngle = 90; //degrees
//Add the points

double distanceThreshold = 1; // robot must be at most one inch away from the final position. 
double timeoutThreshold = 5000; // stop the robot if it takes more than 5 seconds to complete the path
double finalPointX = allPoints.get(allPoints.size()-1).x, 
finalPointY = allPoints.get(allPoints.size()-1).y; 

double distance = Math.hypot(odometry.getX()-finalPointX), odometry.getY()-finalPointY); 
ElapsedTime timer = new ElapsedTime(); 
while(distance>distanceThreshold&&timer.milliseconds()<timeoutThreshold){
    followCurve(allPoints, Math.toRadians(desiredAngle)); 
    // Update the distance. 
    distance = Math.hypot(odometry.getX()-finalPointX), odometry.getY()-finalPointY); 
}
```

## Alternative Implementation

https://github.com/jw5243/MPC-Lib/blob/master/MPCLib/src/main/java/com/horse/mpclib/examples/PurePursuitRobot.java

The link above provides an excellent implementation of the pure pursuit algorithm due to its conciseness. This was possible due to the numerous built-in utility classes that simplify a lot of the math involved in programming autonomous movement algorithms. To use the code simply plug in the convertPowerToInput() function's output values as powers in your drive motors. 

