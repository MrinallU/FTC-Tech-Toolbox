# Vuforia

Vuforia is a library often used with vision detection for FTC. To use Vuforia, you will need to obtain a Vuforia license key. Luckily, this process is simple and steps can be found [here](https://library.vuforia.com/getting-started/vuforia-license-manager).

### Instantiating Vuforia

Once you have gotten the license key, you must start the initialization of Vuforia. Start with these two lines.

```java 
String VUFORIA_KEY = "Vuforia Key Goes Here"
VuforiaLocalizer vuforia;
```

### Parameters

Now we must further initialize the Vuforia Engine but passing the necessary parameters for it to run. Just paste this line into your initialization.

```java 
int cameraMonitorViewId = hardwareMap.appContext.getResources().getIdentifier("cameraMonitorViewId", "id", hardwareMap.appContext.getPackageName());
VuforiaLocalizer.Parameters parameters = new VuforiaLocalizer.Parameters(cameraMonitorViewId);
```

Now we since we have created the parameters object, we can pass in the actual parameters.
```java 
parameters.vuforiaLicenseKey = VUFORIA_KEY;
parameters.cameraDirection = BACK; //Making sure to use the right camera
```

Paste these lines to pass everything into and actually startup the Vuforia Engine
```java 
vuforia = ClassFactory.getInstance().createVuforia(parameters);

vuforia.setFrameQueueCapacity(1);

vuforia.enableConvertFrameToBitmap();

```