---
sidebar_position: 1
---
# Base Class (Step 1)

When structuring your FTC code, we recommend using a central class that acts as a hub for all of your hardware initializations, global variables, module classes, and utility functions. You can then create your opModes such that it is an extension of this base class so you can use all the methods within it. 

The benefit of having a base class is that you only need to declare your hardware and create utility functions once, rather than in every opMode. This keeps your code clean and easy to read which can save a lot of time especially as your codebase becomes more complex. 

## Implementation
:::info
Here are some notes on the following code: 
* We make use of the LinearOpModes, however, you can modify the class such that it supports typical opModes as well. 
* If you are using `MANUAL` mode in your LynxModule make sure to add a function to clear the LynxModule hubs as well.
:::
```java
public abstract class Base extends LinearOpMode {

  // Globally Declared Sensors
  public IMU gyro;

  // Module Classes
  public Arm armModule = null; // This is an actual class with various methods
  
  // Global Variables
  public int exampleVariable = 0; 

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

Here is the function you should have to clear your cache if your LynxModule mode is manual.
```java 
public void resetCache() {
    // Clears cache of all hubs
    for (LynxModule hub : allHubs) {
      hub.clearBulkCache();
    }
}
```