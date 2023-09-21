---
sidebar_position: 4
---
# Motors and Encoders

DC Motors in FTC are used for movement of large mechanisms and can be used for fast and continuous rotation, or precise movement for things like an arm. It is very important to learn how to efficiently program motors.

## Motor Initialization

The first step in using a motor is to initialize it as a variable in the code. This is done through the use of an object named **hardwareMap** that is used for easy initialization of FTC Objects.
```java 
DcMotor driveMotor;       
driveMotor = hardwareMap.get(DcMotor.class, "Drive Motor");  
```
In this example, first the `driveMotor` variable is created through the use of the `DcMotor` object. Then the hardwareMap is used to initialize and name the motor, this should be the same name used in the configuration of the Motor on the phone.
## DcMotor Setup Usage

There are many methods that for the DcMotor that can be used before the motor is powered to change how it behaves when it runs.

## Set Direction

```java
driveMotor.setDirection(DcMotor.Direction.Forward);
driveMotor.setDirection(DcMotor.Direction.Reverse);
```
`setDirection` is used to change the way a motor rotates when it is set to power. `setDirection` should be used on drive train motors to ensure that all motors are spinning the same direction when sent to an equal power.

## Motor Encoders

In the realm of FTC robotics, motors connected to an encoder serve as advanced motors with an extra ability: they can communicate to the robot how much they've rotated and at what speed. Imagine you're steering a remote-control car, and you want it to travel a particular distance or turn a specific angle. Regular motors might get close, but they can't give you exact control. Encoder motors, on the other hand, offer precision.

An encoder is typically connected via a special wire that is connected to the motor. This device keeps track of rotations, and each complete rotation is divided into smaller parts called ticks. Think of a tick as a tiny mark on a ruler that measures how much the motor has turned. For instance, a motor may measure 100 ticks for every rotation of the motor shaft. 

FTC teams harness the power of these encoder motors to program their robots with accurate movements. When the robot's wheels turn, the encoders count the ticks. So, if the robot's wheel turns 100 ticks, the robot knows it has moved a specific distance. It's akin to the robot reading its own mini-map that shows exactly where it is.

On the other hand, the motor's velocity is also measured by the encoder through the number of ticks per second the motor is traveling. 

## Run Mode
```java 
driveMotor.setMode(DcMotor.RunMode.RUN_WITHOUT_ENCODER);
driveMotor.setMode(DcMotor.RunMode.RUN_USING_ENCODER);
driveMotor.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);
driveMotor.setMode(DcMotor.RunMode.RUN_TO_POSITION);
```

### RUN WITHOUT ENCODER
This mode causes a motor to not use the plugged in encoder values to do things like control its position or speed.
### RUN USING ENCODER

This causes the motor to use a motor encoder(if plugged in), to help control its position or speed. When a motor is set to `RUN_USING_ENCODER` it automatically uses the encoder to help keep the motor consistently running at any speed set to the motor. 

### STOP AND RESET ENCODER
This will reset the encoder tick value the motor has to 0. It will ensure that the encoder ticks are consistent per each run, and causes the motor to work as expected each run. If using the encoder ticks as a measurement, `STOP_AND_RESET_ENCODER` should be used at the beginning of your code. 

### RUN TO POSITION

Run To Position is a very useful mode for motors. It allows for a motor to run to a specific target tick value that is set, and the motor will go that position and hold it. It uses an in-built PID Control Loop to accomplish this.

:::info 

When using RUN_TO_POSITION, the power should not ever be negated as the motor will go towards whatever direction the tick count is. If the motor needs to go in the opposite direction, use a negative tick count

:::

## Finding Motor Position
When using encoders, there is a useful method that can be used to find the motors position in encoder ticks.
```java 
int position;
position = motor.getCurrentPosition();
```

:::info

We recommend looking up the webpage of whatever motor you are using and finding the ticks per revolution, the value counted by the encoder per rotation. This can help you in terms of calculating how far you want to turn your mechanisms.
:::

## Velocity Control
When setting the motor to `RUN_USING_ENCODER` mode, setting power to a motor causes it to move at a constant velocity rather than an actual power, this means that a motor connected to an encoder will be less resistant to battery fluctuations when compared to a motor that is not connected to an encoder. 

Alternatively, you can specify the exact ticks per second the motor should be spinning at:
```java
((DcMotorEx)motor).setVelocity(ticksPerSecond); 
```

## Motor Class Implementation
Here is a convenient motor class that contains shortcuts to most of the methods listed above. 
```java 
package org.firstinspires.ftc.teamcode.Utils;

import com.qualcomm.robotcore.hardware.DcMotor;
import com.qualcomm.robotcore.hardware.DcMotorEx;
import com.qualcomm.robotcore.hardware.DcMotorSimple;
import com.qualcomm.robotcore.hardware.HardwareMap;
import com.qualcomm.robotcore.hardware.PIDFCoefficients;

public class Motor {
  DcMotor motor;
  int multiplier = 1;

  public Motor(HardwareMap hardwareMap, String name) {
    this.motor = hardwareMap.dcMotor.get(name);
    motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.BRAKE);

    try {
      resetEncoder(true);
    } catch (Exception e) {
      noEncoder();
    }
  }

  public Motor(HardwareMap hardwareMap, String name, boolean useEncoder) {
    this.motor = hardwareMap.dcMotor.get(name);
    motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.BRAKE);

    try {
      resetEncoder(useEncoder);
    } catch (Exception e) {

    }
  }

  public void setDirection(DcMotorSimple.Direction d) {
    motor.setDirection(d);
  }

  public DcMotorSimple.Direction getDir() {
    return motor.getDirection();
  }

  public void coast() {
    motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.FLOAT);
  }

  public void negateEncoder() {
    multiplier = -1;
  }

  public int encoderReading() {
    return motor.getCurrentPosition() * multiplier;
  }

  public void setPower(double power) {
    this.motor.setPower(power);
  }

  public double getPower() {
    return this.motor.getPower();
  }

  public void resetEncoder(boolean useEncoder) {
    this.motor.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);

    if (useEncoder) {
      useEncoder();
    } else {
      noEncoder();
    }
  }

  public void setFloat() {
    this.motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.FLOAT);
  }

  public void noEncoder() {
    this.motor.setMode(DcMotor.RunMode.RUN_WITHOUT_ENCODER);
  }

  public void flip() {
    this.motor.setDirection(DcMotorSimple.Direction.REVERSE);
  }

  public void useEncoder() {
    this.motor.setMode(DcMotor.RunMode.RUN_USING_ENCODER);
  }

  public void setTarget(double target) {
    this.motor.setTargetPosition((int) target);
  }

  public void setPid(double p, double i, double d, double f) {
    this.retMotorEx()
        .setPIDFCoefficients(DcMotor.RunMode.RUN_USING_ENCODER, new PIDFCoefficients(p, i, d, f));
  }

  public void toPosition() {
    this.motor.setMode(DcMotor.RunMode.RUN_TO_POSITION);
  }

  public boolean isBusy() {
    return this.motor.isBusy();
  }

  public DcMotorEx retMotorEx() {
    return (DcMotorEx) motor;
  }
}```