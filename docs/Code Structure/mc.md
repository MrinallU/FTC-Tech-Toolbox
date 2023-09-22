---
sidebar_position: 2
---
# Module Classes (Step 2)

Consider the base class to be the empty skeleton of your robot, a base class with no module classes is an empty robot. Each of your module classes will represent a hardware module on your robot such as your drive system, linear slide system, claws, etc. And each of your modules will contain some combination of sensors, motors, and servos. The purpose of your module classes will contain functions that control these hardware mechanisms to perform your functions.

By organizing your modules with classes, executing the purpose of the module can be as simple as calling the function in your opMode, allowing you to have an organized codebase. 

## Implementation
Suppose that you have a robot with a singular arm, controlled by a motor connected to an encoder. Following the code structure outlined, you create an Arm class containing the functions needed to control the arm motor and a base class that contains the module.  

### Base Class
Here we initialize the arm module as a global variable and fully initialize the class within our `initailizeHardware` function. Note that we also pass the motor needed for the class to function as a parameter within the class constructor. 
```java 
public abstract class Base extends LinearOpMode {

  // Sensors
  public IMU gyro;

  // Module Classes
  public Arm armModule = null; // This is an actual class with various methods

  // Initialize Hardware Function
  public void initHardware() throws InterruptedException {
    // Hubs
    List<LynxModule> allHubs;
    allHubs = hardwareMap.getAll(LynxModule.class);
    for (LynxModule hub : allHubs) {
      hub.setBulkCachingMode(LynxModule.BulkCachingMode.AUTO);
    }

    // Motors
    DcMotor armMotor = hardwareMap.get(DcMotor.class, "Drive Motor");  
    armMotor.setMode(DcMotor.RunMode.RUN_USING_ENCODER);
    armMotor.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);
    armMotor.setMode(DcMotor.RunMode.RUN_TO_POSITION);
  
   // Init Module class
    armModule = new Arm(armMotor); 
  }
  
  //Utility Functions
  public String formatDegrees(double degrees) {
    return String.format(Locale.getDefault(), "%.1f", AngleUnit.DEGREES.normalize(degrees));
  }
  
  // Allows you to connect opModes to the base class 
  @Override
  public abstract void runOpMode() throws InterruptedException;
}
```
### Module Class
Here we will create the functions required for the arm to execute its purpose. Take for example making the arm motor move some amount of encoder ticks. 
```java 
public class Arm{
    
    DcMotor motor; 
    // class constructor
    public void Arm(DcMotor armMotor){
        this.motor = armMotor; // reinit motors for use within class functions. 
    }
    
    public void moveArmMotorTicks(int ticksToBeMoved){ 
        motor.setTargetPosition(ticksToBeMoved);  //Sets Target Tick Position
        motor.setMode(DcMotor.RunMode.RUN_TO_POSITION); 
        motor.setPower(1);  //Sets Motor to go to position at 1 power.
    }

}
```

In the next module, you will learn how to integrate opModes with this type of code structure! 
