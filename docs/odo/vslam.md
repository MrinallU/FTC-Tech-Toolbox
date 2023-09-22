---
sidebar-position: 6
---
# VSLAM 

In FTC, VSLAM is a way of localization that makes use of a specific camera. In FTC, the specific camera used is called the [Intel T265 Camera](https://www.intelrealsense.com/tracking-camera-t265/). It uses its fisheye lenses in accordance with its variety of sensors to return a pose regarding the robot's location.

![Example banner](../assets/img_3.png)

There has been an api created for the T265 camera, this github page gives all the information necessary from gradle implementation to how to use it to return the robot's position in the code.

https://github.com/pietroglyph/ftc265

:::danger
We do not reccomend usage of VSLAM and the T265 camera for robot localization. First off, Intel has discontinued production of this camera, so it can be hard(and expensive) to get your hands on it. However, the main problem is the api for this camera seems to pose many challenges that can be very hard to fix. To ensure your auto will work properly, this camera requires sufficient testing to ensure it is recording positions on every initialization, something that refs will not like in matches. Also, it often bugs out and does not work due to the nature of the FTC field having constantly moving objects. Furthermore, there are instances where the camera won't start up when run and causes your robot to keep going straight. Our team speaks from experience as we have utilized the Intel T265 in previous seasons, but now stick to a deadwheel odometry setup.
:::

> **Intel**: 
> "An ideal operating environment for the T265 has a reasonable number of fixed, distinct visual features in view. It will perform poorly if the entire field of view contains moving, near field objects such as people"

