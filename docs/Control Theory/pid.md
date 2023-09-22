---
sidebar-position: 2 
---
# PID Loops
:::note Resources
* [GM0's PID Page](https://gm0.org/en/latest/docs/software/concepts/control-loops.html)
* [CTRL-ALT-FTC's PID Page](https://www.ctrlaltftc.com/the-pid-controller)
:::

Once you have all the data needed to localize a robot and all subsystems within that robot, you need some way to actually move the robots mechanisms to the desired position. This can be the drive train, arm, etc. The way to do this within in FTC is the PID Control Loop, standing for Proportional, Derivative, and Integral. 

In simpler terms the PID converts the inputs you receive from your sensors into powers for the motors which control your mechanism. 

## Beginner Video

<iframe width="100%" height="422" src="https://www.youtube.com/embed/fv6dLTEvl74" title="PID Controller Explained" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Advanced Video

<iframe width="100%" height="422" src="https://www.youtube.com/embed/wkfEZmsQqiA" title="What Is PID Control? | Understanding PID Control, Part 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## How it Works

The PID Control Loop is a feedback loop that is based on error to a certain position. Since you know the position you are at, and the position you want to go to, the error can be continuously found. A certain power is then applied to your motor based on the error and the PID loop coefficients that will be explained on this page. The basic fact to note, the **PID takes error as an input and outputs a power to your motor based on that error**. For this page, assume $e(t)$ as the error. 

To find out where the mechanism is currently you will need some kind of sensor to return this information. In the case of a motor you will need a encoder. 

After this, calculating the error function is trivial. Simply query the sensor's current value and subtract it from what the sensor's value would be at the mechanisms desired value. 

$$
e(t) = input - setpoint
$$

:::info
If you get confused by the statement above try relating this logic to the AC example in the video above. 
:::

The PID Formula
The output $m_n$ of a PID controller obeys the equation 
$$
u(t) = K_p \cdot e(t) + K_i \cdot \int_{0}^{t} e(\tau) d\tau + K_d \cdot \frac{de(t)}{dt}
$$

This is a function of time(t), that outputs the output power to the motor system, f(t).

This formula can look pretty daunting, but it can be broken down pretty easily. Furthermore, a conceptual understanding of the formula itself is not necessarily vital to being able to implement the PID. 

First, here's a formal definition of the terms in this equation:

* $u(t)$ represents the control output at time tt.
* $Kp$ is the proportional gain.
* $Ki$ is the integral gain.
* $Kd$ is the derivative gain.
* $e(t)$ is the error signal at time tt, typically calculated as the difference between the desired setpoint and the actual process variable.

Here's a more intuitive breakdown: 

First, note the use of $e(t)$ in this formula, this is the calculated error of the motor that is previously described. Also, note the use of $K_p$, $K_i$, and $K_d$, these are all constant coefficients that are set through the process of tuning. This formula combines all three parts of the PID together, you can make out the math behind each individual part by looking at the constant coefficient that is made use of in that part.

Now, let's break the PID loop down part by part.

## Proportional Component
$$
P(t) = K_p \cdot e(t)
$$
The first part of the PID Loop is the proportional term, this is the simplest term and is the easiest to understand. It directly relates the error to the power set to the motor. The coefficient, Kp, is directly multiplied to the error of the motor to its target position. Thinking about how this works, as the error becomes smaller, so will the term as a whole, setting the power to be slower. The power will slow down more and more as the robot reaches the position, allowing for what should be an accurate stop. 

Let's use this in an example. Assume that Kp has been tuned to 0.02. Say that my motor's error from its target position is 50 ticks, it is 50 ticks away from where it needs to be. For the first loop of the PID, 0.02 will be multiplied by the current error, resulting in an output of 1, the motor will be set to 1 power, or its top speed. Now on the next run, we get a bit closer, say 30 ticks away. Now 30 is multiplied by 0.02, and the power becomes 0.6. As the motor gradually gets closer, the power gradually decreases until it stops at the target position, this is how the proportional component works.

## Integral Component
$$
I(t) = K_i \cdot \int_{0}^{t} e(\tau) d\tau
$$
The Integral and Derivative Components can start to get a bit more in-depth and complex in terms of the math behind them, but a conceptual understanding of what they do will work.

For starters, we do not recommend the use of the integral component within your PID for FTC use. However, feel free to try it out on your robot, there is nothing detrimental to its use. However, it will require more tuning for results that aren't really apparent in FTC. 

Essentially, an integral accounts for accumulating error over time and sums up error over time. However, with the small size of FTC fields as well as the short period of the match, the effect of the integral is not really observed in most cases. The integral component also multiples its summing by the time to complete each loop to prevent any possible time lag with the accumulation. It is also important to place a limit on the integral term in your code. In some cases, it can accumulate to be pretty large, it is recommended to set a range for your integral term between `[-maxIntegralValue, maxIntegralValue]` to keep it from summing up to high.

## Derivative Component
$$
D(t) = K_d \cdot \frac{de(t)}{dt}
$$
Even though the math behind this term is pretty easy to understand if you have a knowledge of basic calculus, it can still be understood with just the conceptual understanding. What this term does is first calculate the rate at which the error is changing at a given time t. By calculating the rate of change of e(t), it then dampens the rate of change and attempts to keep a constant rate of change of the error as the motor approaches its position. Using the proportional and derivative components to create a PD Loop is our recommended practice for custom PID Loops.

## PID Tuning
Arguably, the most intensive part of the PID implementation process is not the code implementation, but the tuning of the coefficients instead. Remember, all three coefficients need to be tuned to correct values. Here is the recommended tuning procedure. 

Starting with all coefficients at 0, increase the Kp coefficient until the mechanism is moving to the target position, and having oscillations at the target position. Then, increase the Kd coefficient until the mechanism is reaching a smooth stop at its target position with no oscillations.

## PID Simulator 
Try playing around with the PID simulator in the link below to gain a better understanding of how each component of a PID controller works. 

https://grauonline.de/alexwww/ardumower/pid/pid.html

## Potential Applications
* Autonomous Movement: Odometry acts as the input, setpoint is the desired robot position. 
* Motor Control: Encoder Position acts as the input, setpoint is the desired encoder position. 
* Vision Control: Distance from camera center pixel to center pixel of a detected object's bounding box is the setpoint. 
* Distance sensor based driving: Input is the distance sensor reading, setpoint is the desired distance sensor reading.

## Implementation
:::info
In this example, the k_p, k_i, and k_d variables are the coefficients that must be tuned for the PID.
:::
```java 
public void MoveToPosition(DcMotor motor, int targetPos, double k_p, double k_i, double k_d, OpMode opmode){
    ElapsedTime time = new ElapsedTime();
    int accuracy = 3;
    double previousTime = 0, previousError = 0;
    double p = 0, i = 0, d = 0;
    double max_i = 0.2, min_i = -0.2;
    double power;
    while (Math.abs(targetPos - motor.getCurrentPosition()) > accuracy && ((LinearOpMode)opmode).opModeIsActive() ) {
        double currentTime = time.milliseconds();//gets current time in ms
        double error = position - motor.getPosition();//gets current error
        
        p = k_p * error; //Proportional Component
        
        i += k_i * (error * (currentTime - previousTime)); //Integral Component
        
        Range.clip(i, min_i, max_i); //Accounting for Integral Accumulation
        
        d = k_d * (error - previousError) / (currentTime - previousTime); //Derivative Component
        
        //set power
        power = p + i + d;
        motor.setPower(power);

        //Save Values
        previousError = error;
        previousTime = currentTime;
    }
}
```