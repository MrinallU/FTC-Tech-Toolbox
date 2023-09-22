# Streaming Frames

Once you have your camera plugged in and set up, you actually need to stream the frame and get your camera to record the frames. Both OpenCV and Vuforia make this relatively easy.

### OpenCV

In Open CV, there is a specific set of lines you must use to "open" the camera stream so it is streaming all the frames.

```java 
webcam.openCameraDeviceAsync(
        new OpenCvCamera.AsyncCameraOpenListener() {
          @Override
          public void onOpened() {
            webcam.startStreaming(320, 240, OpenCvCameraRotation.UPRIGHT);
          }

          @Override
          public void onError(int errorCode) {}
        });
```

This will start your camera streaming, as well as allow you to specify the rotation of the camera being used. Make sure you have already intialized and set up your Open CV Camera before using this.

### Vuforia

In Vuforia, instead of having a continuous stream, you will be taking a bitmap image and passing that in as your input image. 

```java 
Bitmap bm;
VuforiaLocalizer.CloseableFrame closeableFrame = vuforia.getFrameQueue().take();
bm = vuforia.convertFrameToBitmap(closeableFrame);
```
This will return a usable bitmap, that you can use to do your image processing.
