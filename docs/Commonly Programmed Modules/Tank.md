---
sidebar_position: 1
---
# Tank Drive / Skid Steer (Part 1)
:::note Resources

* [GM0's Overview of the Tank Drivetrain](https://gm0.org/en/latest/docs/common-mechanisms/drivetrains/tank.html) - Explains terminology, provides vizualizations, goes over the pros and cons. Must Read!
* [Coding a Tank Drive in Blocks](https://www.youtube.com/watch?v=rpfhDDX_LOs) - Good for explaining the logic behind an implementation
* [FTC Team 7477's Tank Drive Control Award Video](https://www.youtube.com/watch?v=3SdU8ct94Rc&t=6s) - Shows that building a control system with a tank drive train is possible.

:::

 ## What is Tank?
A tank drive train is a method of driving that you would see outside of FTC robotics. For instance, one would control a tank drive train similarly to how an RC car is controlled, with one analog stick controlling the forward movement and the other controlling the turning of the robot.
A tank drive system consists of 4 powered normal wheels. It can go forward and back, as well as turn in place. Therefore, it has two main functions, drive and turn. 

#### Advantages
* Easy to build
* Can get a tank drive train built quickly
* Good for beginner programmers and builders
* Can traverse over harsh terrain (ie the barrier from FTC game Freight Frenzy)

#### Drawbacks
* Not as efficient as a mecanum drive train (no sideways movement)
* Autonomous programs will not be as reliable

## TeleOp Implementation
The drive system will have two variables that will decide the power set to each individual wheel, drive and turn. Drive will determine front and back motion, while turn will determine rotational motion. The drive variable should be set equally to each motor, since each motor needs to turn in the the same direction to go forward and backward. However, to turn in place, wheels on one side of the robot must move forward while the wheels on the other side move in reverse. This can be done by having a base `drive` power for all motors, and adding a turn `power` to one side while subtracting it from the other.

The following code will use the thought process described in the description above:

Initialize your motors like this: 
```java 
frontLeft.setDirection(DcMotor.Direction.FORWARD); // Set to REVERSE if using AndyMark motors
frontRight.setDirection(DcMotor.Direction.REVERSE);// Set to FORWARD if using AndyMark motors'
backRight.setDirection(DcMotor.Direction.REVERSE);// Set to FORWARD if using AndyMark motors
backLeft.setDirection(DcMotor.Direction.FORWARD); // Set to REVERSE if using AndyMark motors
```

Paste this into your opMode:
```java
double drive;
double turn;
double leftSidePow = 0, rightSidePow = 0;

drive = gamepad1.left_stick_y * -1; //set drive to power from left joystick
turn = gamepad1.right_stick_x; //set turn to power given from right joystick

leftSidePow = Range.clip(drive + turn, -1, 1);
rightSidePow = Range.clip(drive - turn, -1, 1);

frontLeft.setPower(leftSidePow);
backLeft.setPower(leftSidePow);
frontRight.setPower(rightSidePow);
backRight.setPower(rightSidePow);
```

### Why Reverse the Right Side?

When building a tank drive the motors on each side will be mirrored, meaning spinning all of them at a positive power will have each side spinning in opposite direction. First, you need to reverse one side of the drive train motors to have them all spinning in the same direction
:::info
You might've noticed something new here, the Range.clip function. In this example, this function sets a minimum of -1 for the power and 1 for the maximum power due to the fact that drive + turn, or drive - turn might be less than -1 or greater than 1 at times. Range.clip will limit the value at the given minimum and maximum so the motor's power bounds are not exceeded. 
:::

## Autonomous Implementation
:::caution
For more advanced teams using a tank drive system, we recommend using [Roadrunner's tank configuration](https://github.com/MrinallU/Super7-Robotics-Tank/blob/T3-Save/Utils/RoadRunner/drive/Tank.java) or making use of the pure pursuit algorithm for following more complex paths.
:::

:::info
The following code uses concepts from a PID controller to regulate the movements of the robot. For simplicity, we have only included the proportional component of the controller for both turning and forward movement. For improved accuracy try including the derivative component.
:::

The following code uses concepts from a PID controller to regulate the movements of the robot. For simplicity, we have only included the proportional component of the controller for both turning and forward movement. For improved accuracy try including the derivative component. 
* Turn to a specified angle.
* Drive a specified amount of encoder ticks forward or backward. 

#### Robot Class
```java
public abstract class Robot extends LinearOpMode {
    List<LynxModule> allHubs;
    public Motor  leftDrive   = null;
    public Motor  rightDrive  = null;
    public Motor  backleftDrive   = null;
    public Motor  backrightDrive  = null;
    public BNO055IMU imu;
    double currAngle = 0;

    // Normal moveToPosition PID Coefficients
    private final double k_p = 0.01; 

    /* Initialize standard Hardware interfaces */
    public void init() {
        // Save reference to Hardware map
        allHubs = hardwareMap.getAll(LynxModule.class);

        for(LynxModule hub : allHubs){
            hub.setBulkCachingMode(LynxModule.BulkCachingMode.AUTO);
        }

        // Motors
        leftDrive  = new Motor(hardwareMap, "fLeft");
        rightDrive = new Motor(hardwareMap, "fRight");
        backleftDrive  = new Motor(hardwareMap,  "bLeft");
        backrightDrive = new Motor(hardwareMap,  "bRight");
        
        // Start Motors
        leftDrive.setDirection(DcMotor.Direction.FORWARD); // Set to REVERSE if using AndyMark motors
        rightDrive.setDirection(DcMotor.Direction.REVERSE);// Set to FORWARD if using AndyMark motors'
        backrightDrive.setDirection(DcMotor.Direction.REVERSE);// Set to FORWARD if using AndyMark motors
        backleftDrive.setDirection(DcMotor.Direction.FORWARD); // Set to REVERSE if using AndyMark motors

        //IMU
        BNO055IMU.Parameters parameters = new BNO055IMU.Parameters();
        parameters.angleUnit           = BNO055IMU.AngleUnit.DEGREES;
        parameters.accelUnit           = BNO055IMU.AccelUnit.METERS_PERSEC_PERSEC;
        parameters.calibrationDataFile = "BNO055IMUCalibration.json"; // see the calibration sample opmode
        parameters.loggingEnabled      = true;
        parameters.loggingTag          = "IMU";
        parameters.accelerationIntegrationAlgorithm = new JustLoggingAccelerationIntegrator();

        imu = hardwareMap.get(BNO055IMU.class, "imu");
        imu.initialize(parameters);

        stopBot();
    }
    
    // HELPER FUNCTIONS
    private void stopBot() {
        leftDrive.setPower(0);
        rightDrive.setPower(0);
        backleftDrive.setPower(0);
        backrightDrive.setPower(0);
    }

    private void setDrivePowers(double v, double motorPower, double v1, double motorPower1) {
        leftDrive.setPower(v);
        backleftDrive.setPower(v1);
        rightDrive.setPower(motorPower);
        backrightDrive.setPower(motorPower1);
    }

    public double getAngle() {
        return imu.getAngularOrientation(
                AxesReference.INTRINSIC, AxesOrder.ZYX, AngleUnit.DEGREES
        ).firstAngle;
    }
    
    public double normalize(double degrees) {
        double normalized_angle = Angle.normalizePositive(degrees);
        if (normalized_angle > 180) {
            normalized_angle -= 360;
        }
        return normalized_angle;
    }
    
    public double angleDifference(double start, double end) {
        double raw_delta = end - start;
        return normalize(raw_delta);
    }


    // Turn to a specified angle
    /* Parameters:
    targetAngle: angle you want to travel to (-180 to 180)
    powerCap: Max power you want the robot to turn that (0 to 1)
    opMode: pass in the current opMode from the function
    */
    public void turnToV2(double targetAngle, double timeout, double powerCap, LinearOpMode opMode)  {
        double angleDiff = 100, currTime = 0;
        double prevAngleDiff = 100;
        double dAng, iAng = 0;

        ElapsedTime time = new ElapsedTime();
        // error threshold is 2 degrees (you can modify this)
        while (time.milliseconds() < timeout && Math.abs(targetAngle - getAngle()) > 2 && opMode.opModeIsActive())  {
            // error from input
            angleDiff = angleDifference(getAngle(), targetAngle);

            // Make the powers stable
            double power = Range.clip(k_p * angleDiff, -powerCap, powerCap);

            setDrivePowers(-power, power, -power, power);
        }
        // stop when pos is reached
        stopBot();
    }

    // Move some amount of ticks forward or backwards
    /* Parameters: 
    ticksMoved: Amount of ticks you want to move
    timeout: exit time limit (in milliseconds)
    powerCap: max motor speed (0 to 1)
    minDifference: Amount of error you want (in ticks) [should not be too small or too big]
    opMode: pass it in
    negate: [False: drive forward x amount of ticks, True: drive backward x amount of ticks]
    */
    public void moveTicks(double ticksMoved, double timeout, double powerCap, double minDifference, LinearOpMode opMode, boolean negate){
        double currTicks = leftDrive.encoderReading();
        double destTick;
        ElapsedTime time = new ElapsedTime();
        if(negate) {
             destTick = currTicks - ticksMoved;
        }else{
             destTick = currTicks + ticksMoved;
        }
        
        // while the robot has not met the error threshold and hasnt met the timeout limit. 
        while(Math.abs(currTicks - (destTick)) > minDifference && time.milliseconds() < timeout && opMode.opModeIsActive()){
            currTicks = leftDrive.encoderReading();

            double tickDiff = destTick - currTicks; // get the error between where you want to be and where you are now
            double drive = Range.clip((tickDiff * 0.055), -powerCap, powerCap); // Multiply by a constant to scale speeds down

            setDrivePowers(drive, drive, drive, drive);
        }
        stopBot(); // stop when pos is reached
    }

    public void moveTicksFront(double ticksMoved, double timeout, double powerCap, double minDifference, LinearOpMode opMode){
        moveTicks(ticksMoved, timeout, powerCap, minDifference, opMode, false);
    }

    public void moveTicksBack(double ticksMoved, double timeout, double powerCap, double minDifference, LinearOpMode opMode){
        moveTicks(ticksMoved, timeout, powerCap, minDifference, opMode, true);
    }
    
    @Override
    public abstract void runOpMode() throws InterruptedException;
}
```
#### Opmode
```java
@Autonomous(name="Autonomous", group = "Autonomous")
public class Autonomous extends Robot {
    @Override
    public void runOpMode() throws InterruptedException {
        init(); 
        
        //Move back 450 ticks with 40% max power with a timeout of 4 seconds
        // error threshold is 20 ticks
        moveTicksBack(450, 4000, 0.4, 20, this); 
        sleep(500);
        
        //Turn to an angle of 93 degrees with a 4 seconds timeout
        turnToV2(93, 4000, this);
        sleep(500);
 
        //Drive forward 200 ticks
        moveTicksFront(200, 4000, 0.4, 20, this);
        sleep(500);
        
    }
}
```

## Case Studies
Take a look at one of the world's best FTC teams: Brainstormers! 
They made use of a six-wheel tank drive train during their time at worlds during the 2021 game Frieght Frenzy.
<iframe width="100%" height="422" src="https://www.youtube.com/embed/TdaD5qsfuZ8" title="FTC 8644 Freight Frenzy Worlds Finalist Robot Explanation" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


Our team also made use of a tank drive train. Here is our code base: https://github.com/MrinallU/Super7-Robotics-Tank
<iframe width="100%" height="422" src="https://www.youtube.com/embed/fiMpo6rYm08" title="League Meet 4 187 Point Match" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>