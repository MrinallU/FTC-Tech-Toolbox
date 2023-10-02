# IMU

:::note Resources
* [FTC's Official IMU Guide](https://ftc-docs.firstinspires.org/en/latest/programming_resources/imu/imu.html) - **Must Read**
:::

The IMU, short for inertial measurement unit, is a sensor located within your control hub that provides information about your robot's rotational position (typically used to attain a measurement of the robot's heading). It helps you understand how your robot is moving and how it's oriented. Most effective control systems rely on this sensor to make accurate decisions and control the robot's behavior.

To create a robust control system, it's important to develop a strong understanding of IMU programming. This involves learning how to read the sensor's data, interpret it correctly, and use it to make informed decisions about controlling the robot's movements. By leveraging the capabilities of the IMU, you can enhance the performance and stability of your robot's control system.

Here are some of the use cases for the IMU: 
* [Robot Localization](../odo/What%20is%20Localization.md) - Allows you to get the current angle of the robot
* [Turret Alignment](../Commonly%20Programmed%20Modules/Turrets.md)
* Anti-Tipping (Code in this section!) - Querying your roll, pitch, or yaw and driving backward when they get too high, thus preventing your robot from tipping over. 
* [Accurate Turning](../Commonly%20Programmed%20Modules/Tank.md)  - Make your robot turn to a specific angle. 

:::info
Because the official FTC guide is so comprehensive when going over IMU setup and programming we will just provide you with a template.
:::

## Implementation
### Robot Class
```java 
public abstract class Robot extends LinearOpMode {
 
  // Gyro and Angles
  public IMU gyro;

  public void initHardware() throws InterruptedException {
    // Hubs
    List<LynxModule> allHubs;
    allHubs = hardwareMap.getAll(LynxModule.class);
    for (LynxModule hub : allHubs) {
      hub.setBulkCachingMode(LynxModule.BulkCachingMode.MANUAL);
    }

    // add your parameters are needed (process described in the FTC docs)
    gyro = hardwareMap.get(IMU.class, "imu");
    gyro.resetYaw();
  }

  //Get the robot's current heading
  public double getAngleImu() {
  //We choose to normalize the IMU's angle reading but you don't need to. 
    return normalize(
        getRobotYawPitchRollAngles().getYaw(AngleUnit.DEGREES));  
  }
  
    // Remaps the given angle into the range (-180, 180].
  public static double normalize(double degrees) {
    double normalized_angle = Angle.normalizePositive(degrees);
    if (normalized_angle > 180) {
      normalized_angle -= 360;
    }
    return normalized_angle;
  }
  
    // BULK-READING FUNCTIONS
  public void resetCache() {
    // Clears cache of all hubs
    for (LynxModule hub : allHubs) {
      hub.clearBulkCache();
    }
  }
  
  
  @Override
  public abstract void runOpMode() throws InterruptedException;
}
```

### OpMode

```java 
@TeleOp(name = "TeleOP", group = "OdomBot")
public class Test_TeleOp extends Robot {

  @Override
  public void runOpMode() throws InterruptedException {
    initHardware();
    telemetry.addData("Status", "Initialized");
    telemetry.update();

    waitForStart();
    matchTime.reset();
    dt.update();
    while (opModeIsActive()) {
      // Updates
      resetCache();
      currAngle = getAngleImu();

     
      telemetry.addData("Angle ", currAngle));
      telemetry.update();
    }
  }
}
```

## Anti-Tip

Here's a bonus implementation of anti-tipping code that you can incorporate into your control system. When your robot starts to tip over, one of the angle values (roll, pitch, or yaw) will change depending on the orientation of your IMU. This code will help you detect tipping and take appropriate actions to prevent it.

As such anti-tipping code can be implemented as follows: 

In the function that determines the robot's drive powers, check if the angle value indicating tipping has exceeded a certain limit. If it has, instead of using the originally planned drive powers, make the robot drive in the opposite direction.

:::info
`gyro.getAngularOrientation().thirdAngle` may have to be modified to `.firstAngle()` or `.secondAngle()` due to the orientation of your IMU. You can determine which angle it is by outputting each value using telemetry and tipping the robot slightly to determine which one changes in a simple teleop. The angle that changes should be the one present in the anti-tip code. 
:::
```java 
public void setDrivePowers(double bLeftPow, double fLeftPow, double bRightPow, double fRightPow) {
   int angleThreshold = 10; // if the angle goes over 10 degrees, robot drives backward
   if(gyro.getAngularOrientation().thirdAngle > angleThreshold){ // anti-tip
     stopDrive();
     driveBackward(); 
     return;
    }

    // else, continue
    bLeftMotor.setPower(bLeftPow);
    fLeftMotor.setPower(fLeftPow);
    bRightMotor.setPower(bRightPow);
    fRightMotor.setPower(fRightPow);
}
```