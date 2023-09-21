---
sidebar_position: 8
---
# Lynx Modules

:::note Resources
* [GM0's Page on Lynx Modules](https://gm0.org/en/latest/docs/software/adv-control-system/lynx-module.html) - **Must Read!**
* [GM0's Lynx Module Code](https://gm0.org/en/latest/docs/software/tutorials/bulk-reads.html) - **Must Read!**
:::
  The Lynx Module is used as an object for each hub on your robot. Each hub on your robot has a Lynx Module created for it. Before doing anything else software related to your robot, it is a good idea to quickly write code that takes care of your control hub. Luckily, this process is easy. 

### Bulk Reading
One of the main uses for the Lynx Module is for bulk reading of sensor  outputs. It can bulk read all of your sensor outputs at once, and therefore make your loop run a lot faster. 
### Implementation
#### Off Mode
:::danger
We highly recommend that you **don't use** this mode! Making use of bulk reads can significantly speed up the loop times of your robot, making your autonomous and teleop periods alot smoother.
:::

```java 
// Paste this where you initialize your hardware

void initHardware(){
   // Always setup your lynx modules before your hardware.
   List<LynxModule> allHubs = hardwareMap.getAll(LynxModule.class);

   for (LynxModule hub : allHubs) {
      hub.setBulkCachingMode(LynxModule.BulkCachingMode.OFF);
   }
   
   initMotors(); 
   initServos(); 
}
```

#### Auto Mode
This is the simplest mode to use that utilizes bulk reads; a new bulk read is done when a hardware read is repeated.
:::danger
This is the simplest mode to use that utilizes bulk reads; a new bulk read is done when a hardware read is repeated.
:::

This implementation from Game Manual 0 gives a good implementation for using the Auto Mode of the Lynx Module
```java 
// From GM0
List<LynxModule> allHubs = hardwareMap.getAll(LynxModule.class);

for (LynxModule hub : allHubs) {
   hub.setBulkCachingMode(LynxModule.BulkCachingMode.AUTO);
}

while (opModeIsActive()) {
   // Will run one bulk read per cycle; however, if e.g.
   // frontLeftMotor.getPosition() was called again,
   // a new bulk read would be issued
   int frontLeftEncoderPos = frontLeftMotor.getPosition();
   int frontRightEncoderPos = frontRightMotor.getPosition();
   int backLeftEncoderPos = backLeftMotor.getPosition();
   int backRightEncoderPos = backRightMotor.getPosition();
}
```

#### Manual Mode
> **GM0:**
> In manual mode the cache for bulk reads is only reset once manually reset. This can be useful, as it is the way to absolutely minimize extraneous reads, however if the cache is not reset, stale values will be returned. That being said, here’s a proper implementation of `MANUAL` mode:

:::danger
In manual mode the cache for bulk reads is only reset once manually reset. This can be useful, as it is the way to absolutely minimize extraneous reads, however if the cache is not reset, stale values will be returned. That being said, here’s a proper implementation of MANUAL mode::
:::

```java 
List<LynxModule> allHubs = hardwareMap.getAll(LynxModule.class);

for (LynxModule hub : allHubs) {
   hub.setBulkCachingMode(LynxModule.BulkCachingMode.MANUAL);
}

while (opModeIsActive()) {
   // Will run one bulk read per cycle,
   // even as frontLeftMotor.getPosition() is called twice
   // because the caches are being handled manually and cleared
   // once a loop
   for (LynxModule hub : allHubs) {
      hub.clearBulkCache();
   }

   int frontLeftEncoderPos = frontLeftMotor.getPosition();
   int frontLeftEncoderPos2 = frontLeftMotor.getPosition();
}
```

#### Common Error When Using Manual Mode. 
A common error that can stump teams using `Manual` mode is when executing code in a `for`, or `while` loop. **If you don't reset your cache in these other loops, your robot can go haywire!**

Here is a proper implementation of Manual mode when using a for or while loop in your code: 
```java 
@Autonomous(name = "Auto", group = "OdomBot")
public class Auto{

List<LynxModule> allHubs = hardwareMap.getAll(LynxModule.class);

for (LynxModule hub : allHubs) {
   hub.setBulkCachingMode(LynxModule.BulkCachingMode.MANUAL);
}

  @Override
  public void runOpMode() throws InterruptedException {
        initHardware(0, this, telemetry);
        sleep(500);
        telemetry.addData("Status", "Initialized");
        telemetry.update();
        
        waitForStart();
        
        // Of course reset the cache in your opMode as well. 
        for (LynxModule hub : allHubs) {
            hub.clearBulkCache();
         }
        
      // While the robot does something for two seconds, you must also reset the cache
      // in every instance of the loop.   
      ElapsedTime t = new ElapsedTime();
      while(t.milliseconds()<=2000){
         for (LynxModule hub : allHubs) {
            hub.clearBulkCache();
         }
         
          doSomething(); // if your function contains a loop, then make sure to reset the
          // cache there as well!
      } 
      
      
  }
}
```

This is the part that resets the bulk cache in manual mode.
```java 
for (LynxModule hub : allHubs) {
    hub.clearBulkCache();
}
```

#### Measuring Voltage
Something else you can do through the Lynx Module is measure the voltage and current that your hubs are receiving

#### Getting Battery Voltage
You can find the voltage of the 12V battery supplying robot power using the following line.
```java 
List<LynxModule> allHubs = hardwareMap.getAll(LynxModule.class);

for (LynxModule hub : allHubs) {
   //receive battery voltage in volts or millivolts
   double voltage = hub.getInputVoltage(VoltageUnit.VOLTS);
}

```

#### Measuring Current
You can also measure the total current draw of the hubs and everything plugged into them by using the `getCurrent()` method, along with the desired unit
```java 
List<LynxModule> allHubs = hardwareMap.getAll(LynxModule.class);

for (LynxModule hub : allHubs) {
   //receive current in amps or milliamps
   double current = hub.getCurrent(CurrentUnit.AMPS);
}
```

