---
sidebar-position: 4
---

# Introduction to Pure Pursuit

:::note Resources
* [Perdue SIGbots' Page on Pure Pursuit](https://wiki.purduesigbots.com/software/control-algorithms/basic-pure-pursuit) - Amazing explanation and Python implementation (Convertable to Java)
* [Gluten Free's Pure Pursuit Tutorial Series](https://youtu.be/3l7ZNJ21wMo) - Explanation + Code
* [FTC Team 9866's Pure Pursuit Explanation](https://chsftcrobotics.weebly.com/uploads/1/2/3/6/123696510/pure_pursuit.pdf)
* [Video Explanation](https://www.youtube.com/watch?v=qYR7mmcwT2w)
* [MPCLib's Implementation](https://github.com/jw5243/MPC-Lib/blob/master/MPCLib/src/main/java/com/horse/mpclib/examples/PurePursuitRobot.java) - Code for Mecanum
:::

Pure pursuit is a path-tracking algorithm commonly used in robotics and autonomous vehicle navigation. It is designed to guide a vehicle toward a desired path or target by continuously adjusting its steering angle.

The basic idea behind pure pursuit is to compute the steering angle of the vehicle based on the desired path and the vehicle's current position. The algorithm works by considering a look-ahead distance ahead of the vehicle along the desired path. A point on the desired path is selected based on this lookahead distance, and the vehicle's steering angle is set to navigate towards that point.

Here's a step-by-step explanation of how pure pursuit works:

1. Define the desired path: The desired path can be represented as a set of waypoints or a continuous mathematical function. These waypoints define the desired trajectory or path that the vehicle should follow.
2. Determine the lookahead distance: The lookahead distance is the distance ahead of the vehicle along the desired path that is considered for selecting the target point. It determines how far ahead the vehicle should look to find the target point.
3. Find the target point: Using the lookahead distance, the algorithm finds a point on the desired path that is closest to the lookahead distance ahead of the vehicle's current position. This point becomes the target point.
4. Compute the steering angle: Once the target point is determined, the algorithm calculates the steering angle required for the vehicle to navigate toward the target point. The steering angle is typically computed using geometric calculations based on the position of the target point and the vehicle's position.
5. Control the vehicle: The computed steering angle is then used to adjust the vehicle's steering mechanism accordingly. The process is repeated continuously, with the vehicle updating its position and recalculating the steering angle based on the new target point.

By continuously updating the steering angle based on the target point, pure pursuit allows the vehicle to follow a desired path closely and accurately. It can handle both curved and straight paths effectively, making it a widely used algorithm in autonomous navigation systems.

<iframe width="100%" height="422" src="https://www.youtube.com/embed/qYR7mmcwT2w" title="LTC21 Tutorial Pure Pursuit" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Implementation
Thanks to FTC team Gluten Free's pure pursuit tutorial, the vast majority of the code that goes into making pure pursuit is explained and copied from their youtube series linked below. Because of this, the following sections related to pure pursuit will only delve into drive train-specific modifications.

<iframe width="100%" height="422" src="https://www.youtube.com/embed/3l7ZNJ21wMo" title="FTC Programming: Pure Pursuit Tutorial 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

