---
sidebar-position: 1
---
:::note Prerequisites
* [Localization](../odo/What%20is%20Localization.md)
* [Control Theory](../category/control-theory)
:::

# Introduction

Autonomous movement, undoubtedly one of the most intricate aspects of FTC programming, brings together all the concepts and techniques covered in the preceding modules, making it a particularly challenging concept to grasp.

An exceptional autonomous movement algorithm should possess the capability to accomplish the following tasks with precision:

1. Navigate the robot to any desired point on the field: The algorithm should enable the robot to accurately calculate and execute the necessary movements to reach a specified location on the playing field.
2. Facilitate smooth path following: The algorithm should ensure that the robot moves along a designated path seamlessly and without abrupt changes in direction or speed.

A typical autonomous movement algorithm in FTC follows a series of steps to achieve accurate and smooth robot navigation. While specific implementations may vary, here is a general outline of the steps involved:

1. Perception and Localization: The algorithm begins by gathering information about the robot's surroundings using sensors such as cameras or encoders. This data is then processed to determine the robot's current position and orientation on the field.
2. Trajectory Generation: Given a series of manually inputted control points, the algorithm generates a smooth trajectory that defines the robot's desired position and velocity over time.
    a.   Rather than building a trajectory for the robot to follow some algorithms will follow a set of weigh points directly. In this case, to create a smooth path, we will need to create an algorithm to generate smooth curves that pass through our defined control points. 
3. Motion Control: The algorithm converts the generated trajectory into low-level motor commands or control signals that power the robot's drive motors. This can involve techniques such as PID control, feedforward control, or model predictive control to accurately track the desired trajectory.
4. Feedback and Adjustment: During execution, the algorithm continuously receives feedback from sensors, such as encoders or the IMU, to monitor the robot's actual position and make necessary adjustments to maintain accuracy and smooth movement. This feedback loop helps correct any errors or deviations from the intended trajectory.
5. Termination Criteria: The algorithm determines the conditions for the completion of the autonomous movement task. This could be reaching the final destination, accomplishing a specific action, or based on a time limit.

We will start off by going over simple autonomous movement algorithms to establish a solid foundation before moving on to more sophisticated algorithms such as pure pursuit or guided vector fields, which allow the robot to follow a smooth path. 


