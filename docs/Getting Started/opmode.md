---
sidebar_position: 3
---
# Creating an OpMode
:::note Resources

* [Our tutorial on opmode creation](https://www.youtube.com/watch?v=UmsXnZxoDmI)
:::


In FTC your code is written in op-modes, you use op modes for both Tele-op and Autonomous. You will learn the basics in setting up your first op-mode.

## Two Types
There are two types of op-modes, the OpMode and the LinearOpMode, both of them are programmed a bit differently, but ultimately achieve the same purpose. Our video on OpMode Creation goes in depth into both. 

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
