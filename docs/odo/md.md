---
sidebar-position: 5
---

# Deadwheel Odometry (Mecanum and Tank)

:::note Resources
* [GM0's Odometry Page](https://gm0.org/en/latest/docs/software/concepts/odometry.html) - **Must Read!** (Explains deadwheel configurations and provides easy to implement pseudocode + a overview behind the for 3 wheel odometry)
* [RoadRunner's Odometry Paper](https://github.com/acmerobotics/road-runner/blob/master/doc/pdf/Mobile_Robot_Kinematics_for_FTC.pdf)
:::

Deadwheel odometry makes use of unpowered wheels connected to encoders to track the robot's current position. Essentially, a deadwheel odometry algorithm converts the returned encoder measurements into useful values.

For maximized accuracy, most teams make use of three deadwheel setups. However if a team needs an extra encoder slot, some will make do with a two-deadwheel setup and the IMU for angle calculation.

Why use 3-wheel odometry as opposed to 2-wheel: [Answered in RoadRunner docs](https://learnroadrunner.com/#what-is-the-difference-between-two-and-three-wheel-odometry)

## 2 Deadwheel Odometry

Intuitively speaking, we know that a mecanum drive train can move both forward and sideways. Therefore when making use of a two-wheel odometry setup we can see that you only need to use one of the parallel wheels.
:::caution
Note that because we are removing one of the parallel wheels, you cannot calculate the robot's current angle through odometry alone. Instead, you must make use of the IMU.
:::

![Example banner](../assets/img_2.png)

```java 
public class Odometry {
  // Constants
  public final double ENCODER_WHEEL_DIAMETER = 1.37795; // diameter of the deadwheel
  private final double ENCODER_TICKS_PER_REVOLUTION = 8154; // ticks measured after
  // one full revolution of the deadwheel
  private final double ENCODER_WHEEL_CIRCUMFERENCE = Math.PI * 2.0 * (ENCODER_WHEEL_DIAMETER * 0.5);

  // Variables
  private double xPos, yPos;
  public double angle;
  private double lastLeftEnc = 0, lastNormalEnc = 0;

  public Odometry(double xPos, double yPos) {
    this.xPos = xPos;
    this.yPos = yPos;
  }

  // Two Deadwheel Odo
  /*
  l = ticks from the parallel odometry wheel
  r = ticks from the perpendicular odometry wheel
  ang = robot's angle (in degrees)
  */
  public void updatePosition(double l, double n, double ang) {
    double dL = l - lastLeftEnc;
    double dN = n - lastNormalEnc;
    lastNormalEnc = n;
    lastLeftEnc = l;
    
    double leftDist = -dL * ENCODER_WHEEL_CIRCUMFERENCE / ENCODER_TICKS_PER_REVOLUTION;
    double dyR = leftDist;
    double dxR = -dN * ENCODER_WHEEL_CIRCUMFERENCE / ENCODER_TICKS_PER_REVOLUTION;
    
    double cos = Math.cos((Angle.degrees_to_radians(ang)));
    double sin = Math.sin((Angle.degrees_to_radians(ang)));
    double dx = (dxR * sin) + (dyR * cos);
    double dy = (-dxR * cos) + (dyR * sin);
    
    angle = ang;
    xPos += dx;
    yPos += dy;
  }
  
  public double getX() {
    return xPos;
  }

  public double getY() {
    return yPos;
  }
}
```


## 3 Deadwheel Odometry
Due to the extensive explanations written in the links provided at the top of the module, we feel that it would be redundant to provide our own. Instead, we will just give you an implementation.

[The following code is from the samples of Beta8397's virtual robot simulator:
](https://github.com/Beta8397/virtual_robot/blob/master/TeamCode/src/org/firstinspires/ftc/teamcode/EncBot.java)
```java 
/**
 * Utility class that represents a robot with mecanum drive wheels and three "dead-wheel" encoders.
 */
public class EncBot {
    public final double ENCODER_WHEEL_DIAMETER = 2; // diameter of the deadwheel
    private final double ENCODER_TICKS_PER_REVOLUTION = 1120; // ticks measured after
  // one full revolution of the deadwheel
    private final double ENCODER_WIDTH = 12.0; // distance between parallel deadwheels
    private final double ENCODER_WHEEL_CIRCUMFERENCE = Math.PI * 2.0;
    
    public final DcMotorEx[] motors = new DcMotorEx[4]; //back_left, front_left, front_right, back_right
    public final DcMotorEx[] encoders = new DcMotorEx[3]; //right, left, X
    public int[] prevTicks = new int[3];
    public double[] pose = new double[3];

    public void init(HardwareMap hwMap){
        String[] motorNames =  new String[]{"back_left_motor", "front_left_motor", "front_right_motor", "back_right_motor"};
        for (int i=0; i<4; i++) motors[i] = hwMap.get(DcMotorEx.class, motorNames[i]);
        motors[0].setDirection(DcMotorSimple.Direction.REVERSE);
        motors[1].setDirection(DcMotorSimple.Direction.REVERSE);
        // store deadwheels in an array. 
        String[] encoderNames = new String[]{"enc_right", "enc_left", "enc_x"};
        for (int i=0; i<3; i++) encoders[i] = hwMap.get(DcMotorEx.class, encoderNames[i]);
    }

    public void setDrivePower(double px, double py, double pa){
        double[] p = new double[4];
        p[0] = -px + py - pa;
        p[1] = px + py - pa;
        p[2] = -px + py + pa;
        p[3] = px + py + pa;
        double max = Math.max(1, Math.max(Math.abs(p[0]), Math.max(Math.abs(p[1]), Math.max(Math.abs(p[2]), Math.abs(p[3])))));
        if (max > 1) for (int i=0; i<4; i++) p[i] /= max;
        for (int i=0; i<4; i++) motors[i].setPower(p[i]);
    }

    public void resetOdometry(double x, double y, double headingRadians){
        pose[0] = x;
        pose[1] = y;
        pose[2] = headingRadians;
        for (int i=0; i<3; i++) prevTicks[i] = encoders[i].getCurrentPosition();
    }

    // call updateOdometry everytime you set powers to your drive motors. 
    public double[] updateOdometry(){
        int[] ticks = new int[3];
        for (int i=0; i<3; i++) ticks[i] = encoders[i].getCurrentPosition();
        int newRightTicks = ticks[0] - prevTicks[0];
        int newLeftTicks = ticks[1] - prevTicks[1];
        int newXTicks = ticks[2] - prevTicks[2];
        prevTicks = ticks;
        
        double rightDist = newRightTicks * ENCODER_WHEEL_CIRCUMFERENCE / ENCODER_TICKS_PER_REVOLUTION;
        double leftDist = -newLeftTicks * ENCODER_WHEEL_CIRCUMFERENCE / ENCODER_TICKS_PER_REVOLUTION;
        double dyR = 0.5 * (rightDist + leftDist);
        double headingChangeRadians = (rightDist - leftDist) / ENCODER_WIDTH;
        double dxR = -newXTicks * ENCODER_WHEEL_CIRCUMFERENCE / ENCODER_TICKS_PER_REVOLUTION;
        double avgHeadingRadians = pose[2] + headingChangeRadians / 2.0;
        
        double cos = Math.cos(avgHeadingRadians);
        double sin = Math.sin(avgHeadingRadians);
        pose[0] += dxR*sin + dyR*cos;
        pose[1] += -dxR*cos + dyR*sin;
        pose[2] = AngleUtils.normalizeRadians(pose[2] + headingChangeRadians);
        
        return pose;
    }

    public double[] getPose(){
        return pose;
    }

}
```
## Gobilda Pinpoint Odometry

Gobilda has recently came out with their own odometry pod setup, this allows teams to completely outsource their implementation of odometry pods and have a "plug and play" odometry implementation that is accurate to the inch. If possible, we now highly recommend teams to purchase and use the Pinpoint along with the Gobilda odometry wheels in their implementation. This may take the fun out of doing a custom implementation, but from our testing, we have found the pinpoint + Gobilda pods to be very accurate and reliable.

### What's Needed

The PinPoint has a built in IMU within it, so if you choose to use this with the two pods, you can base your localization solely off the pinpoint system. You use encoder cables to plug both your pods in to the pinpoint. While we recommend using the Gobilda Pods along with the pinpoint, due to their perfect and tested tensioning/accuracy, using custom pods will be perfectly fine as well. Just ensure your pods are tensioned. Our team used custom pods along with the pinpoint for the State and World Championships in Into the Deep and also had very positive results.

### Implementation

When first instantiating the Pinpoint object in your code, you want to create an object of the PinPoint Driver class

```java
public GobildaPinpointDriver odo;
```

Next, you have to instantiate it as part of your Hardware Map, just like a normal hardware device

```java
odo = hardwareMap.get(GoBildaPinpointDriver.class,"odo");
```
Next, you must provide the pod offsets. These offsets are the distances that both the X and Y pods are from where the Pinpoint is on your robot. For both pods, you should measure the axis that is perpendicular to the direction of the deadwheel to get this offset. That means, for the Y Pod Offset, you are really measuring the X-axis distance between the pinpoint and the Y deadwheel. 

For the X offset, left is positive and right is negative. For the Y pod offset, forward is positive and backwards is negative.

Here is an example of setting pod offsets(don't use these same ones on your robot; remeasure your own as described above)

```java
odo.setOffsets(184.15, -204.7875);
```

Finally, you have to provide the encoder resolution of the encoder you are using. If you are using the GoBilda Pods, this will be available as a preset variable for you to just input. However, if you are using custom pods with different encoders, you must divide the Counts Per Rotation(CPR) of your encoder by the odometry wheel diameter(in mm).

The most common custom pod implementations use the Rev Throughbore Encoder along with the Rotocaster 35 mm omni wheel. For this specific case, the encoder resolution is provided in the line below.

```java
odo.setEncoderResolution(74.4998181157); //Encoder Resolution for 35 mm deadwheel w/ Rev Throughbore
```

Finally, you want to reset the Pinpoint from previous runs to ensure proper function

```java
odo.resetPosandIMU();
```


### Code Implementation

Putting it all together, here is an implementation of a base robot class with the Pinpoint being used for the odometry localization.

```java

    public List<LynxModule> allHubs;

    Motor fLeftMotor;
    Motor fRightMotor;
    Motor bLeftMotor;
    Motor bRightMotor;

    Motor extendo;

    Motor rightSlide, leftSlide;

    DistanceSensor specDistance;

    public Servo rotation;

    public Servo hang;
    public Servo claw;
    public Servo wrist;
    public Servo blocker;
    public Servo leftArm;
    public Servo rightArm;
    public Servo leftIntake;
    public Servo rightIntake;
    public Servo specimen;

    public IMU imu;






    
    public GoBildaPinpointDriver odo;

    public Drive dt;

    double leftArmDown = .785, rightArmDown = 0.166;
    double leftArmUp = 0.25, rightArmUp = 0.703;


    public void initHardware(OpMode opMode){

        //Enable Manual Bulk Caching
        allHubs = hardwareMap.getAll(LynxModule.class);
        for (LynxModule hub : allHubs) {
            hub.setBulkCachingMode(LynxModule.BulkCachingMode.MANUAL);
        }
        resetCache();

        //Initialize Motors
        fLeftMotor = new Motor(hardwareMap, "fLeft", false);
        bLeftMotor = new Motor(hardwareMap, "bLeft", false);
        bRightMotor = new Motor(hardwareMap, "bRight", false);
        fRightMotor = new Motor(hardwareMap, "fRight", false);

        bRightMotor.setDirection(DcMotorSimple.Direction.REVERSE);
        fRightMotor.setDirection(DcMotorSimple.Direction.REVERSE);

        extendo = new Motor(hardwareMap, "linkage");
        rightSlide = new Motor(hardwareMap, "rightSlide");
        leftSlide = new Motor(hardwareMap, "leftSlide");
        rightSlide.retMotorEx();
        leftSlide.retMotorEx();
        extendo.retMotorEx();
        rightSlide.useEncoder();
        leftSlide.useEncoder();
        extendo.useEncoder();


        imu = hardwareMap.get(IMU.class, "imu");
        imu.initialize(
                new IMU.Parameters( new RevHubOrientationOnRobot(
                        RevHubOrientationOnRobot.LogoFacingDirection.RIGHT,
                        RevHubOrientationOnRobot.UsbFacingDirection.UP
                )
                ));
        resetYaw();


        //Initialize IMU


        dt = new Drive(fLeftMotor, fRightMotor, bLeftMotor, bRightMotor, imu, opMode);




        odo = hardwareMap.get(GoBildaPinpointDriver.class,"odo");

        odo.setOffsets(184.15, -204.7875);

        odo.setEncoderResolution(74.4998181157);



        odo.setEncoderDirections(GoBildaPinpointDriver.EncoderDirection.REVERSED, GoBildaPinpointDriver.EncoderDirection.REVERSED);

        odo.resetPosAndIMU();
```

### Getting Position Data
Lastly, you must know how to actually get the position data from the Pinpoint for use in your programs. To do this, first ensure you have the proper initialization for the Pinpoint as described above. After calling this initialization in your OpMode, you can then find the position values. 

You first need to create a Pose2D object that will hold the PinPoint position. Then it becomes relatively intuitive to print each component of the position out.

```java
Pose2D pos = odo.getPosition();

double x = pos.getX(DistanceUnit.INCH);

double y = pos.getY(DistanceUnit.INCH);

double angle = pos.getHeading(AngleUnit.DEGREES);

```




