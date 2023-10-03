# VisionProcessor Class

As stated before, instead of using `OpenCVPipeline`, we should be using the `VisionProcessor` in our detection classes. Your setup for a new frame processor should look something like this.

```java
public class RedPropThreshold implements VisionProcessor {
    Mat testMat = new Mat();

    @Override
    public void init(int width, int height, CameraCalibration calibration) {

    }

    @Override
    public Object processFrame(Mat frame, long captureTimeNanos) {
        return null;
    }

    @Override
    public void onDrawFrame(Canvas canvas, int onscreenWidth, int onscreenHeight, float scaleBmpPxToCanvasPx, float scaleCanvasDensity, Object userContext) {

    }
}
```

As you can see, this follows a similar template to previous OpenCV Pipelines however the methods are a bit different as well as the class now `implements VisionProcessor` instead of `extends OpenCVPipeline`