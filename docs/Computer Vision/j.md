# Object Tracking / Driving to an Object
:::note Resources
* Our Page on Object Distance Estimation
* Our Page on Autonomous Movement
:::
### Object Tracking
[Our turret module provides a tutorial on object tracking as well as a codebase where object tracking is implemented (be sure to also look at the case study section in the linked module): 
](../Commonly%20Programmed%20Modules/Turrets.md)

### Driving to an Object

#### Our Way
Implement the following algorithm: 

* Detect the object and store its bounding box (refer to the Machine Learning Toolchain module)
* Determine the distance from the object to the camera (refer to the Object Distance Estimation module). You will only need the distance on the z and x axis for this. 
* You now have the hypotenuse **(distance on the z-axis)** and one side length **(absolute value of the distance on the x-axis)** of a right triangle! Solve for the other side length through the Pythagorean theorem. This second side is your y distance!
* Add the x (horizontal) and newly calculated y distance to your robot's current position, this is the object's real-world position. Note that you have to account for the robot's orientation!
* Plug the position into your autonomous movement functions (refer to the autonomous movement sections)

### Alternative Methods

<iframe width="100%" height="422" src="https://www.youtube.com/embed/qDoLmZyH69o" title="Vuforia in FTC 6: Navigating to the Beacon" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

[The FTC samples also give an excellent tutorial on driving to a detected object!](https://github.com/FIRST-Tech-Challenge/FtcRobotController/blob/e0282fcbd3de5116132a69a1d0ed229f3ca97e0f/FtcRobotController/src/main/java/org/firstinspires/ftc/robotcontroller/external/samples/ConceptVuforiaDriveToTargetWebcam.java)

### Real World Example
FTC Team Hazen Robotics has implemented code that can drive to a game element:
<iframe width="100%" height="422" src="https://www.youtube.com/embed/bfZT--sZf6U" title="Control Award Submission - Washington State FTC Championships" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

https://github.com/HazenRobotics/freight-frenzy/blob/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode/utils/TargetPositionCalculator.java
https://github.com/HazenRobotics/freight-frenzy/blob/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode/vision/HomographyTargetDistance.java
