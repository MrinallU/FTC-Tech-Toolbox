# VisionProcessor OpMode

The usage for the Vision Processor within the OpMode also differs a bit than previous years. The first step is to initalize the Vision Portal, this can be done through the `VisionPortal.Builder()` class.

### Initialization
```java
@Autonomous(name="Vision Test")
public class CameraTest extends LinearOpMode {
    
    private VisionPortal portal;
    
    @Override
    public void runOpMode() throws InterruptedException {
        
        portal = new VisionPortal.Builder()
                .setCamera(hardwareMap.get(WebcamName.class, "Webcam 1"))
                .setCameraResolution(new Size(640, 480))
                .setCamera(BuiltinCameraDirection.BACK)
                .build();
        

        waitForStart();




    }
}
```
This will add the necessary configuration you need for your camera and allow you to start streaming the frames on your driver station. Take note that `Webcam 1` should be replaced with the name of your webcam within your config.  

### Adding In A Processor
Now we are going to add in our previously defined, `RedPropThreshold` into our autonomous. Doing this will allow the `processFrame()` function within the RedPropThreshold to be run with the current frame.
```java
@Autonomous(name="Vision Test")
public class CameraTest extends LinearOpMode {
    private RedPropThreshold redPropThreshold; //Create an object of the VisionProcessor Class
    private VisionPortal portal;
    
    @Override
    public void runOpMode() throws InterruptedException {
        redPropThreshold = new RedPropThreshold();
        portal = new VisionPortal.Builder()
                .setCamera(hardwareMap.get(WebcamName.class, "Webcam 1"))
                .setCameraResolution(new Size(640, 480))
                .setCamera(BuiltinCameraDirection.BACK)
                .addProcessor(redPropThreshold)
                .build();
        

        waitForStart();




    }
}