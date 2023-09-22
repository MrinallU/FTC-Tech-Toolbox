# Autonomous Movement Libraries

Instead of starting from scratch, most teams use ready-made libraries for movement in their projects. These libraries have pre-built algorithms that save a lot of time compared to creating and fixing movement code by hand.

Why pick a library? Even though this guide explains how to use movement algorithms found in these libraries, we still recommend that all teams use these libraries. They're well-tested and need only a bit of tweaking to work perfectly. Some of the fancier algorithms in this guide take a lot of time to set up, which most teams don't have during the busy season.

Libraries also make sure things are super accurate, like using special math in their movement code. This means that using a library often results in a more precise autonomous program compared to one written from the ground up. That's why top FTC teams often rely on these movement libraries.

To build on that idea, the main reason top FTC teams use libraries in their code is so they can spend the time they save on writing and fixing code on other important parts of their project that can't be handled with libraries. Here are some examples:

1. Keeping track of a moving turret
2. Making the autonomous program as efficient as possible
3. Getting the software that controls the robot's different parts just right
4. Driver Practice

:::tip
Note that some teams may believe that using custom-made autonomous movement code will help your team win the control award. While this statement may have some truth to it, note that anybody can create custom movement code as the implementations for popular movement algorithms can be found online. Because of this, we believe that you should save time by using a path planning library and instead work on solving a unique problem in a creative way for which there is no library. Solving these problems is likely what will set you apart from other teams when running for the control award.

Some examples:
* [Wall Odometry](https://www.youtube.com/watch?v=Bo4R6sHHHWY)
* [Intelligent Intake Claw](https://www.youtube.com/watch?v=Bo4R6sHHHWY)
* [Automatic Bucket Height](https://www.youtube.com/watch?v=bfZT--sZf6U)
:::

So, you can see that the time spent working on these tasks often makes the difference between a good team and an outstanding one.

## Popular Libraries

### Roadrunner
:::info
For more information regarding what goes on under the hood of roadrunner take a look at the following page: https://acme-robotics.gitbook.io/road-runner/advanced/resources
:::

Among all the path planning libraries used in FTC robotics, this particular one stands out as the favorite choice, and there's a good reason for that. Roadrunner empowers robots to follow intricate paths in an easy-to-understand manner, allowing for the creation of autonomous programs with very little effort in terms of setup as all localization and path planning code is already implemented. We strongly suggest using this library and using the time you save to fine-tune your software or spend more time practicing how to control the robot effectively.

Keep in mind that dedicating a considerable time chunk to fine-tune the parameters requested by the library is necessary. This step ensures the library operates as anticipated. The guide provided below aims to make this tuning process more streamlined.
https://learnroadrunner.com/#frequently-asked-questions

### FTCLib
https://docs.ftclib.org/ftclib/v/v2.0.0/


