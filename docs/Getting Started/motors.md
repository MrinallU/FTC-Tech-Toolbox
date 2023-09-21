---
sidebar_position: 4
---
# Creating an OpMode

DC Motors in FTC are used for movement of large mechanisms and can be used for fast and continuous rotation, or precise movement for things like an arm. It is very important to learn how to efficiently program motors.

## Motor Initialization

The first step in using a motor is to initialize it as a variable in the code. This is done through the use of an object named hardwareMap that is used for easy initialization of FTC Objects.
```java 
DcMotor driveMotor;       
driveMotor = hardwareMap.get(DcMotor.class, "Drive Motor");  
```
In this example, first the driveMotor variable is created through the use of the DcMotor object. Then the hardwareMap is used to initialize and name the motor, this should be the same name used in the configuration of the Motor on the phone.
## OpMode
An OpMode class is composed of five different methods that you can write your code inside. The code in the five different methods runs in a different time/way.

* `init()` - Code inside this method will run once after the program is initialized.
* `init_loop()` - Code inside this method will loop repeatedly while the program is initialized.
* `start()` - Code inside this method will run once after the program is started.
* `loop()` - Code inside this method will run repeatedly for the duration of the program.
* `stop()` - Code inside this method will run once after the program is stopped.

The whole setup for the OpMode class with all five methods will look like this.

```java
import com.qualcomm.robotcore.eventloop.opmode.OpMode;

@TeleOp(name = "NonLinearTeleop")
public class FirstTeleop extends OpMode {  //Make sure the class extends OpMode
    @Override             //Override is used for each loop
    public void init() {
        //Happens once on init
    }

    @Override
    public void init_loop() {
        //Happens repeatedly during init
    }

    @Override
    public void start() {
        //Happens once after program starts
    }

    @Override
    public void loop() {
        //Happens repeatedly during the program
    }

    @Override
    public void stop() {
        //Happens once after stop
    }
}

```

## LinearOpMode
The other op-mode class is called LinearOpMode. It uses some different functions and has the code organized differently.
## Important LinearOpMode Methods
* `waitForStart()` - Waits for Program to be started after initialization.
* `opModeIsActive()` - Checks to see if the op-mode has been started, returns True or False.
* `runOpMode()` - Contains all op-mode code, runs once after the start of the OpMode.
## Tele-op Linear OpMode
```java 
@TeleOp(name = "LinearTeleop")
public class LinearTeleop extends LinearOpMode{
    @Override
    public void runOpMode() throws InterruptedException {
        //Initialization Code Goes Here
        waitForStart();
        while(opModeIsActive()){ //while loop for when program is active
            //Code repeated during teleop goes here
            //Analogous to loop() method in OpMode
        }

    }
}
```
## LinearOpMode Autonomous
The autonomous for LinearOpMode is very similar to the Teleop setup, however the while(opModeIsActive) is not necessary for most code as autonomous happens once. The while(opModeIsActive) can still be used for things such as constant telemetry readings during the autonomous.
```java 
@Autonomous(name = "LinearAuto")
public class LinearAuto extends LinearOpMode{
    @Override
    public void runOpMode() throws InterruptedException {
        //Initialization Code Goes Here
        waitForStart();
        //Auto Code Goes Here

    }
}
```
