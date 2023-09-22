---
sidebar_position: 3
---
# Tank (No Deadwheels)
:::note Resources
* [Georgia Tech's Video on Differential Drive Odometry](https://www.youtube.com/watch?v=XbXhA4k7Ur8) - Excellent video explaing the logic behind odometry. Must Watch!
:::

This particular odometry technique utilizes encoders attached to the drive motors of our robot and employs several trigonometric formulas to convert the encoder values into a representation of the robot's pose. The step-by-step mathematical process is outlined below:

:::caution
Here are some notes/warnings about the following code and math: 
* The following formulas make use of the encoder values present in the front left and front right drive motors. 
* In the equations where there are variables of the same name for instance (a = a' + 1). The variable with the ' symbol represents the computed result from the previous iteration of the odometry function. 
* The calculation of the robot's angle (theta) can be replaced with the reading from an IMU. In the case of this odometry variation, **using the IMU reading is highly recommended**, especially considering the increased susceptibility to slipping compared to deadwheel odometry.
* All angle calculations are done in **radians**!
* You will need some manually calculated values before proceeding: 
  * The radius of your wheels
  * Amount of ticks your encoder reads after one revolution of the wheel
  * The track width of the robot (distance between the front left and right wheels)
:::

Firstly, we determine the distance traveled by the robot by converting the encoder readings for the left and right motors into actual distances. 

Encoders provide measurements in ticks, so we need to convert these readings into meaningful units. To do this, we divide the raw encoder measurement by the number of ticks recorded for each complete revolution of the wheel. Then, we multiply this value by the circumference of the wheel to obtain the distance in inches that the wheel has traveled since the last reading of the odometry function.

$$
\Delta tick = tick' - tick
$$
$$
D = 2\pi * wheelRadius *  \frac{\Delta tick}{ticksPerRevolution}
$$

Note that this formula must be applied to both the left and right wheels. 

We then average the calculated inch displacement from the left and right wheels giving the overall displacement of the robot represented as $D_c$
$$ 
D_c = \frac{D_l-D_r}{2}
$$
After determining the total displacement, we proceed to separate it into its x, y, and theta components using the trigonometric formulas provided below. By adding these components to the previously calculated values, we can obtain the current position of the robot.:::info
:::info
Note that the variable "L" is the track width of the robot (the distance between the front left and right wheels of the robot)
:::

$$
x' = x + D_c * cos(\theta)
$$
$$
y' = y + D_c * sin(\theta)
$$
$$
\theta ' = \theta + \frac{D_r-D_l}{L}
$$

## Implementation

Note that the code contains two update position functions: 
* `updatePositionWithIMU()` - Computes the position with the IMU (recommended)
* `updatePosition()` - Computes the position without the IMU as theta. 
```java
public class WheelOdometry {
    private final double WHEEL_RADIUS = 2; // WHEEL_RADIUS
    private final double TICKS_PER_REV = 538; // Manually Calibrated
    private final double TRACK_WIDTH = 10; 
    private double x, y, theta;
    private double prevL = 0, prevR = 0; // previously calculated vals
    private String outStr;
    // Construtor
    public WheelOdometry(int x, int y, int theta){
            this.x = x;
            this.y  = y;
            this.theta = theta;
    }
    
     // Apply the iterative process
    public void updatePositionWithIMU(double currLTick, double currRTick, double currTheta){
        double dL =  (2*Math.PI*WHEEL_RADIUS) * (
                        (currLTick - prevL) / TICKS_PER_REV
                );
        double dR =  (2*Math.PI*WHEEL_RADIUS) * (
                (currRTick - prevR) / TICKS_PER_REV
        );
        double dC = (dL + dR) / 2;

        double dX = dC * Math.cos(theta);
        double dY = dC * Math.sin(theta);
        x += dX; y += dY; theta = Math.toRadians(currTheta);
        outStr = "xPos: " + format(x) + "\nyPos: " + format(y) + 
        "\nAngle: " + format(theta);

        prevL = currLTick;
        prevR = currRTick;
    }
    
    
    // Apply the iterative process
    public void updatePosition(double currLTick, double currRTick){
        double dL =  (2*Math.PI*WHEEL_RADIUS) * (
                        (currLTick - prevL) / TICKS_PER_REV
                );
        double dR =  (2*Math.PI*WHEEL_RADIUS) * (
                (currRTick - prevR) / TICKS_PER_REV
        );
        double dC = (dL + dR) / 2;

        double dX = dC * Math.cos(theta);
        double dY = dC * Math.sin(theta);
        x += dX; 
        y += dY; 
        theta = theta + ((dR - dL)/TRACK_WIDTH);
        
        outStr = "xPos: " + format(x) + "\nyPos: " + format(y) + 
        "\nAngle: " + format(theta);

        prevL = currLTick;
        prevR = currRTick;
    }
    

    // reset pose
    public void setPose(int x, int y, int theta){
        this.x = x;
        this.y = y ;
        this.theta = theta;
    }
    
    // Output positions to telemetry
    public String displayPositions(){
        return outStr;
    }

    private String format(double num){
        return String.format("%.3f", num);
    }
}

```


