# Color Thresholding

:::note Resources
* [WPLib's Page on Color Thresholding](https://docs.wpilib.org/en/stable/docs/software/vision-processing/wpilibpi/image-thresholding.html) - **Must Read!**
* [Wizard.exe's Color Thresholding Tutorial](https://www.youtube.com/watch?v=-QFoOCoaW7I) - Solves the ultimate goal computer vision task.
* [RoboForce's Color Thresholding Tutorial](https://www.youtube.com/watch?v=rQjcZt6V9ac) - Detects yellow poles in FTC game Power Play.
:::

Most vision tasks in FTC ask you to identify the position of an object that is randomly placed in one of three areas before the match starts.

A straightforward way to solve this task is to scan the image your camera has returned for the object. Since we know that the object will be in one of three predetermined locations, we can scan the corresponding segments in the image for the color of the object.  

The region which contains the most pixels that match the color of the object is the correct region, making our pipeline return the object. Upon receiving the location of the object from the pipeline, you can tell your opMode to perform the corresponding action, for example, parking in a specific area of the field. 

## OpenCV
In order to turn a colored image, such as the one captured by your camera, into a binary image, with the target as the “foreground”, we need to threshold the image using the hue, saturation, and value of each pixel.

This model is known as the HSV model of representing an image. 

> **WPLib Documentation**
> Unlike RGB, HSV allows you to not only filter based on the colors of the pixels but also on the intensity of color and the brightness.
> * Hue: Measures the color of the pixel.
> * Saturation: Measures the intensity of the color of the pixel.
> * Value: Measures the brightness of the pixel.

Because HSV takes account of brightness it can help mitigate changes in lighting making it superior to thresholding with the default BGR model. 

Luckily, OpenCV allows you to convert between image models easily:

```java
Imgproc.cvtColor(input, hsvMat, Imgproc.COLOR_RGB2HSV);
```

From there we define the regions that should be scanned:

```java 
private Point topLeft1 = new Point(10, 0), bottomRight1 = new Point(40, 20);
```


We also have to define the actual threshold values for the color. We do this by establishing a lower bound and a higher bound. OpenCV has a built-in library function that returns a modified image that highlights the pixels that contain HSV values in between the established bounds.
```java 
public Scalar lower = new Scalar(0, 0, 0);
public Scalar upper = new Scalar(255, 255, 255);

Core.inRange(hsvMat, lower, upper, binaryMat); // thresholds image, and places results 
// in a separate image
```

Finally, we search through each of the pixels in the defined regions, the region with the most amount of white pixels is the correct location of the object. 
```java 
double w1 = 0, w2 = 0;
// process the pixel value for each rectangle  (255 = W, 0 = B)
for (int i = (int) topLeft1.x; i <= bottomRight1.x; i++) {
  for (int j = (int) topLeft1.y; j <= bottomRight1.y; j++) {
      if (binaryMat.get(i, j)[0] == 255) {
          w1++;
      }
  }
}

for (int i = (int) topLeft2.x; i <= bottomRight2.x; i++) {
  for (int j = (int) topLeft2.y; j <= bottomRight2.y; j++) {
      if (binaryMat.get(i, j)[0] == 255) {
          w2++;
      }
  }
}

if (w1 > w2) {
  location = "1";
} else if (w1 < w2) {
  location = "2";
}
```

Putting it all together we get the following pipeline:

```java 
public class rectangle_thresholder_pipeline extends OpenCvPipeline {
  private String location = "nothing"; // output
  public Scalar lower = new Scalar(0, 0, 0); // HSV threshold bounds
  public Scalar upper = new Scalar(255, 255, 255);

  private Mat hsvMat = new Mat(); // converted image
  private Mat binaryMat = new Mat(); // imamge analyzed after thresholding
  private Mat maskedInputMat = new Mat();

  // Rectangle regions to be scanned
  private Point topLeft1 = new Point(10, 0), bottomRight1 = new Point(40, 20);
  private Point topLeft2 = new Point(10, 0), bottomRight2 = new Point(40, 20);

  public rectangle_thresholder_pipeline() {
    
  }

  @Override
  public Mat processFrame(Mat input) {
    // Convert from BGR to HSV
    Imgproc.cvtColor(input, hsvMat, Imgproc.COLOR_RGB2HSV);
    Core.inRange(hsvMat, lower, upper, binaryMat);
    
    
    // Scan both rectangle regions, keeping track of how many
    // pixels meet the threshold value, indicated by the color white 
    // in the binary image
    double w1 = 0, w2 = 0;
    // process the pixel value for each rectangle  (255 = W, 0 = B)
    for (int i = (int) topLeft1.x; i <= bottomRight1.x; i++) {
      for (int j = (int) topLeft1.y; j <= bottomRight1.y; j++) {
        if (binaryMat.get(i, j)[0] == 255) {
          w1++;
        }
      }
    }
    
    for (int i = (int) topLeft2.x; i <= bottomRight2.x; i++) {
      for (int j = (int) topLeft2.y; j <= bottomRight2.y; j++) {
          if (binaryMat.get(i, j)[0] == 255) {
              w2++;
          }
      }
    }
    
    // Determine object location
    if (w1 > w2) {
      location = "1";
    } else if (w1 < w2) {
      location = "2";
    }

    return binaryMat;
  }

  public String getLocation() {
    return location;
  }
}
```

## Vuforia

The process of determining object location is very similar to that of OpenCV. However, there are a couple of key differences: 

* We do not convert from RGB to HSV, instead thresholding the image using RGB bounds.
* We do not convert the image to binary format and count the white pixels as there is no built-in thresholding function. 
  * Instead, we check we calculate the average RGB values of each rectangle region, and compare them to the threshold value. 

Here is an example of a thresholding for the orange rings in ultimate goal.

```java 
public class T2_Camera {
    private static final String VUFORIA_KEY = "AWnPBRj/////AAABmaYDUsaxX0BJg7/6QOpapAl4Xf18gqNd7L9nALxMG8K2AF6lodTZQ78nnksFc2CMy/3KmeolDEFGmp0CQJ7c/5PKymmJYckCfsg16B6Vnw5OihuD2mE7Ky0tT1VGdit2KvolunYkjWKDiJpX15SFMX//Jclt+Xt8riZqh3edXpUdREIXxS9tmdF/O6Nc5mUI7FEfAJHq4xUaqSY/yta/38qirjy3tdqFjDGc9g4DmgPE6+6dGLiXeUJYu32AgoefA1iFRF+ZVNJEc1j4oyw3JYQgWwfziqyAyPU2t9k9UDgqEkyxGxl4xS70KN/SBEUZeq4CzYfyon2kSSvKK/6/Vt4maMzG3LXfLt0PMiEPI1z+";
    private VuforiaLocalizer vuforia;
    private HardwareMap hardwareMap;

    private int blueThreshold = 100; // threshold value {0 to 255}

    public String outStr = "";

    // Rings Are YELLOW (255 R and 255 G)
    // Just look at blue value
    public T2_Camera(HardwareMap hardwareMap){
        this.hardwareMap = hardwareMap;
        initVuforia();
    }

    public int pollRings() throws InterruptedException{
        Bitmap bm;

        VuforiaLocalizer.CloseableFrame closeableFrame = vuforia.getFrameQueue().take();

        //Height is 720, width is 1280
        bm = vuforia.convertFrameToBitmap(closeableFrame);

        // Scans average RGB values for the defined rectangle bounds. 
        double[] bottomRing = calculateAverageRGB(bm, 345, 236, 365, 246);
        double[] topRings = calculateAverageRGB(bm, 345, 280, 365, 300);

            The  The

        // If the average red, green, or blue value of that rectangle matches the
        // threshold then it is the correct location.
        if(topRings[3] < blueThreshold){
            return 2;
        }
        else if(bottomRing[3] < blueThreshold){
            return 1;
        }

        return 0;
    }

   

    public double[] calculateAverageRGB(Bitmap bm, int left, int top, int right, int bottom){
        long numTotalPixels = 0;
        double[] colorValues = {0, 0, 0, 0};
        for(int x = left; x <= right; x++){
            for(int y = top; y <= bottom; y++){
                int pixel = bm.getPixel(x, y);
                colorValues[0] += Color.alpha(pixel);
                colorValues[1] += Color.red(pixel);
                colorValues[2] += Color.green(pixel);
                colorValues[3] += Color.blue(pixel);
                numTotalPixels++;
            }
        }

        for(int i = 0; i < colorValues.length; i++){
            colorValues[i] = colorValues[i] / numTotalPixels;
        }

        return colorValues;
    }
   
}
```

Our logic was to pick two spots on the ring stack, one spot on the first ring, and one spot on the fourth ring. If both spots have orange, there are four rings, if only one spot has orange, there is one ring, if neither of the spots has orange, there are no rings. The Calculate Average RGB function is taking in the section of the mat to analyze and returns the average RGB of the region. The amount of blue is analyzed to determine if the color of the rings is present or not.

