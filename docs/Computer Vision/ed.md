# VisionPortal Thresholding

Now that we have the basics of VisionPortal down, we can start thresholding the frames for our desired task. The color thresholding is still rooted in OpenCV, so this part will be very similar to Color Thresholding with EasyOpenCV. In CENTERSTAGE teams need to build a vision detector system that can detect a red or blue prop. The following code shows an example of how this pipeline can be done with color thresholding.

:::info
It is important to have reviewed the previous Color Thresholding page in this section before continuing, the fundamental principles used in that are the same for Vision Processor.
::: 

# Red Prop Processor Implementation
```java

public class RedPropThreshold implements VisionProcessor {
    Mat testMat = new Mat();
    Mat highMat = new Mat();
    Mat lowMat = new Mat();
    Mat finalMat = new Mat();
    double redThreshold = 0.5;

    String outStr = "left"; //Set a default value in case vision does not work

    static final Rect LEFT_RECTANGLE = new Rect(
            new Point(0, 0),
            new Point(0, 0)
    );

    static final Rect RIGHT_RECTANGLE = new Rect(
            new Point(0, 0),
            new Point(0, 0)
    );

    @Override
    public void init(int width, int height, CameraCalibration calibration) {

    }

    @Override
    public Object processFrame(Mat frame, long captureTimeNanos) {
        Imgproc.cvtColor(frame, testMat, Imgproc.COLOR_RGB2HSV);


        Scalar lowHSVRedLower = new Scalar(0, 100, 20);  //Beginning of Color Wheel
        Scalar lowHSVRedUpper = new Scalar(10, 255, 255);

        Scalar redHSVRedLower = new Scalar(160, 100, 20); //Wraps around Color Wheel
        Scalar highHSVRedUpper = new Scalar(180, 255, 255);

        Core.inRange(testMat, lowHSVRedLower, lowHSVRedUpper, lowMat);
        Core.inRange(testMat, redHSVRedLower, highHSVRedUpper, highMat);
        
        testMat.release();
        
        Core.bitwise_or(lowMat, highMat, finalMat);

        lowMat.release();
        highMat.release();

        double leftBox = Core.sumElems(finalMat.submat(LEFT_RECTANGLE)).val[0];  
        double rightBox = Core.sumElems(finalMat.submat(RIGHT_RECTANGLE)).val[0];

        double averagedLeftBox = leftBox / LEFT_RECTANGLE.area() / 255;
        double averagedRightBox = rightBox / RIGHT_RECTANGLE.area() / 255; //Makes value [0,1]




        if(averagedLeftBox > redThreshold){        //Must Tune Red Threshold
            outStr = "left";
        }else if(averagedRightBox> redThreshold){
            outStr = "center";
        }else{
            outStr = "right";
        }

        finalMat.copyTo(frame); /*This line should only be added in when you want to see your custom pipeline
                                  on the driver station stream, do not use this permanently in your code as
                                  you use the "frame" mat for all of your pipelines, such as April Tags*/
        return null;            //You do not return the original mat anymore, instead return null




    }


    @Override
    public void onDrawFrame(Canvas canvas, int onscreenWidth, int onscreenHeight, float scaleBmpPxToCanvasPx, float scaleCanvasDensity, Object userContext) {

    }

    public String getPropPosition(){  //Returns postion of the prop in a String
        return outStr;
    }
}
```

As you can see, the main functions used to threshold this frame are originated from OpenCv. You have not the seen the `bitwise_or` function in this guide yet. This function takes two mats and combines them so that pixels that are within the range on one mat or the other are all added into the new mat.

This is required due to the fact that there are two color ranges for red on the HSV Wheel. We do not know what range the processor will read into, so we must threshold the values for both scalars, and then combine them into one mat. Then all the elements in the selected rectangle regions are added up, then averaged based on the area of the region. Then two boxes are checked to see if the prop is present, if they are not it is assumed the prop is in the other/third location.

Furthermore note the rectangle interest regions have points for 0,0 on both the top left and bottom right bounding areas. We did not add in example rectangle regions as these must be taken from your image that is specific to you. The redThreshold value should also be tuned to make sure the prop will detect above it, and the gray field tiles will detect underneath it.

### Saving Image
In order to manipulate and determine the correct pixel bounding rectangle location, you should ideally have a file of the frame saved to work with. There is a simple way to do this in your OpMode program once the VisionPortal has been initialized.

```java
portal.saveNextFrameRaw(String.format(Locale.US, "CameraFrameCapture-%06d"));
 ```               


### Incorporation into OpMode
Now we will look into how to use the `getPropPosition()` method in our teleop program.
```java
@Autonomous(name="Vision Test")
public class CameraTest extends LinearOpMode {
    
    private VisionPortal portal;
    private RedPropThreshold redPropThreshold;
    
    @Override
    public void runOpMode() throws InterruptedException {
        
        portal = new VisionPortal.Builder()
                .setCamera(hardwareMap.get(WebcamName.class, "Webcam 1"))
                .setCameraResolution(new Size(640, 480))
                .setCamera(BuiltinCameraDirection.BACK)
                .build();
        

        waitForStart();
        telemetry.addData("Prop Position", redPropThreshold.getPropPosition());
        telemetry.update();                        //Will output prop position on Driver Station Console




    }
}