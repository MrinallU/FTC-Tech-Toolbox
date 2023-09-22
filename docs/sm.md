---
sidebar_position: 4
---
# State Machines and Timers

:::note Resources
* [GM0's Page on State Machines](https://gm0.org/en/latest/docs/software/concepts/finite-state-machines.html) - **Must Read!**
:::
A state machine is a structure used that allows your robot to execute tasks automatically and sequentially. In the driver-controlled period of your matches, state machines allow you to control several modules of your robot with the press of a button. 

Here is an example of a robot that uses a state machine to control 2 motors and 6 servos to score a game element with the press of a single button: 

<iframe width="100%" height="422" src="https://www.youtube.com/embed/pMx-p1Y1lZE" title="Super 7 Cycling" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

As you can see in the video, the scoring mechanism used is very complex with multiple moving parts. Assigning every individual motor and servo to its own button would overwhelm the drivers. To avoid these issues, programmers use state machines to automate these processes. 

## Timers
An important aspect of state machines is that they can automatically wait for one task to be completed before starting another one. Take, for example, waiting for your claw servo to fully grab onto a game element before making your linear slide motors lift the mechanism. 

Waiting for tasks becomes easy by using timers, provided by Java's `ElapsedTime` class. 

You can initialize a timer like this: 

```java 
ElapsedTime timer = new ElapsedTime(); 
```
Check how much time has passed like this:

```java 
timer.millseconds(); 
```
And reset the timer like this:
```java
timer.reset();  
```

## Implementation

We can make a state machine through the following process:

* Name each process through Java's Enum utility
* Create a switch statement branch containing the name of each Enum as a statement
* Initialize a variable denoting which step the state system should be executing. 
* Within the actual branches of the switch statement: 
  * Execute the task, and reset the timer used to measure the amount of time the task has taken
  * If the timer says that enough time has passed such that the task is guaranteed to be completed, move on to the next step. 

The following code should make this easier to understand: 
:::caution
Here are some notes on the provided code:
* Some of the branches do not make use of timers, instead making use of encoder measurements to check when the lift is finished raising. However, the `getPosition` method can sometimes be buggy so if you find that your state machine doesn't work well, try only making use of timers.
* You cannot make use of `getPosition` with servos since it always returns the target position of the servo rather than its true position. As such, you must use timers when if your task is dependent on servo movement.
:::

:::info
The following code is from [GM0's Page on State Machines](https://gm0.org/en/latest/docs/software/concepts/finite-state-machines.html) (also listed as a resource).
:::

```java 
@TeleOp(name="FSM Example")
public class FSMExample extends OpMode {
    // An Enum is used to represent lift states.
    // (This is one thing enums are designed to do)
    public enum LiftState {
        LIFT_START,
        LIFT_EXTEND,
        LIFT_DUMP,
        LIFT_RETRACT
    };

    // The liftState variable is declared out here
    // so its value persists between loop() calls
    LiftState liftState = LiftState.LIFT_START;

    // Some hardware access boilerplate; these would be initialized in init()
    // the lift motor, it's in RUN_TO_POSITION mode
    public DcMotorEx liftMotor;

    // the dump servo
    public Servo liftDump;
    // used with the dump servo, this will get covered in a bit
    ElapsedTime liftTimer = new ElapsedTime();

    final double DUMP_IDLE; // the idle position for the dump servo
    final double DUMP_DEPOSIT; // the dumping position for the dump servo

    // the amount of time the dump servo takes to activate in seconds
    final double DUMP_TIME;

    final int LIFT_LOW; // the low encoder position for the lift
    final int LIFT_HIGH; // the high encoder position for the lift

    public void init() {
        liftTimer.reset();

        // hardware initialization code goes here
        // this needs to correspond with the configuration used
        liftMotor = hardwareMap.get(DcMotorEx.class, "liftMotor");
        liftDump = hardwareMap.get(Servo.class, "liftDump");

        liftMotor.setTargetPosition(LIFT_LOW);
        liftMotor.setMode(DcMotor.RunMode.RUN_TO_POSITION);
    }

    public void loop() {
        liftMotor.setPower(1.0);

        switch (liftState) {
            case LiftState.LIFT_START:
                // Waiting for some input
                if (gamepad1.x) {
                    // x is pressed, start extending
                    liftMotor.setTargetPosition(LIFT_HIGH);
                    liftState = LiftState.LIFT_EXTEND;
                }
                break;
            case LiftState.LIFT_EXTEND:
                 // check if the lift has finished extending,
                 // otherwise do nothing.
                if (Math.abs(liftMotor.getCurrentPosition() - LIFT_HIGH) < 10) {
                    // our threshold is within
                    // 10 encoder ticks of our target.
                    // this is pretty arbitrary, and would have to be
                    // tweaked for each robot.

                    // set the lift dump to dump
                    liftDump.setTargetPosition(DUMP_DEPOSIT);

                    liftTimer.reset();
                    liftState = LiftState.LIFT_DUMP;
                }
                break;
            case LiftState.LIFT_DUMP:
                if (liftTimer.seconds() >= DUMP_TIME) {
                    // The robot waited long enough, time to start
                    // retracting the lift
                    liftDump.setTargetPosition(DUMP_IDLE);
                    liftMotor.setTargetPosition(LIFT_LOW);
                    liftState = LiftState.LIFT_RETRACT;
                }
                break;
            case LiftState.LIFT_RETRACT:
                if (Math.abs(liftMotor.getCurrentPosition() - LIFT_LOW) < 10) {
                    liftState = LiftState.LIFT_START;
                }
                break;
            default:
                 // should never be reached, as liftState should never be null
                 liftState = LiftState.LIFT_START;
        }

        // small optimization, instead of repeating ourselves in each
        // lift state case besides LIFT_START for the cancel action,
        //It's just handled here
        if (gamepad1.y && liftState != LiftState.LIFT_START) {
            liftState = LiftState.LIFT_START;
        }

        // mecanum drive code goes here
        // But since none of the stuff in the switch case stops
        // the robot, this will always run!
        updateDrive(gamepad1, gamepad2);
   }
}
```

## Case Study
[Take a look at the code file which controls the robot from the video above!
](https://github.com/MrinallU/S7-PowerPlay/blob/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode/NewRobot/Modules/SlideSystem.java)
:::caution
Please note that our state machine does not make use of enums, so we recommend that you use this code as a reference but still maintain the cleaner structure shown in the example above. 
:::
