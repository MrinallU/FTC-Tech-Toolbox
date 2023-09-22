---
sidebar_position: 3
---
# OpMode Classes (Step 3)
Going along with the robot established in the previous module, we will now create an OpMode for our robot with a single motor connected to an encoder. Creating the opMode is very simple. 

## Implementation

The only difference between a standard opMode and this opMode is that this will be an extension of the base class. 
:::info
Look at how clean the opMode is now! This is because there are no variable declarations or hardware initializations. All boilerplate code and utility functions are stored in other files where they can easily be edited and applied to all other opModes with minimal effort.
:::
### Opmode

```java 
@TeleOp(name = "SampleTeleop", group = "samples")
public class SampleTeleop extends Base { // extends base instead of linearopmode
  @Override
  public void runOpMode() throws InterruptedException {
    
    initHardware(); // inits hardware
    telemetry.addData("Status", "Initialized");
    telemetry.update();

    waitForStart();
    matchTime.reset();

    while (opModeIsActive()) {
      // Updates
      //only call reset cache if you are using manual mode in your lynxmodule 
      //resetCache();
      
      // move arm up when 'a' is pressed
      if(gamepad1.a){
        armModule.moveArmMotorTicks(500); 
      }
            
      // move arm back down when 'b' is pressed
      if(gamepad1.b){
        armModule.moveArmMotorTicks(0); 
      }
      
      // Display Values
      telemetry.addLine("Program is running"); 
      telemetry.update();
    }
  }
}
```

### Module Class: 
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

### Base Class:
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