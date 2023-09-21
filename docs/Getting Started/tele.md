---
sidebar_position: 8
---
# Telemetry

When programming in FTC, telemetry can be used to display messages on the driver station. It can be very useful as it can help easily identify issues or unusual readings with your robot. 

### Adding a Line
A new telemetry line can be created using the `addLine()` method which takes in a string as a parameter to display. It can be displayed above some data to categorize it.

```java 
telemetry.addLine("Sensor Info");
```

### Adding Data
Using the `addData()` method will allow you to display a specific data value to the driver station along with a string caption.
```java 
telemetry.addData("Position", 1);
```

### Using a Variable

Oftentimes, telemetry is used to display a variable related to a motor or servo to show information about how it is running. To do this, you should use addData() and use a variable for the second parameter.
```java 
int position;
position = motor.getCurrentPosition();
telemetry.addData("Motor Pos", position);
```

### Updating Telemetry
After adding the telemetry line or data, it will not actually show up on the driver station unless you use `telemetry.update()` after it to update. 

```java 
telemetry.addData("Position", 1);
telemetry.update();
```

### Telemetry in an OpMode
Usually, teams use teleop once after all initialization code to show everything has been initialized, and have different telemetry show in loops during their opmodes, here is an example of telemetry in a Linear OpMode.

```java 
@TeleOp(name = "TeleOp)
public void RoboTele extends LinearOpMode{
    @Override
    public void runOpMode() throws InterruptedException {
        //All initialization code
        telemetry.addLine("Robot Initialized");
        telemetry.update(); //Update once
        
        waitForStart();
        
        while(opModeIsActive){
            //Teleop Functions
            
            
            telemetry.addData("Data", 3);
            telemetry.update(); //Updates each teleop cycle
        }
    }
}
```

### Transmission Time
You can also change how often telemetry updates or refreshes on the driver station screen if you want to change it from the default.
```java 
//Parameter is # of milliseconds between updates
telemetry.setMsTransmissionInterval(200);
```

