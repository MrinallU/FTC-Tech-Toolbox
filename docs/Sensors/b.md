# Color Sensors
:::note Resources
* [Official FTC Video Docs](https://www.youtube.com/watch?v=iQufRF1HFRc) - **Must Watch!**
* [FTC Tricks](https://ftc-tricks.com/overview-color-sensor/)
It is commonly known that colors can be broken down into three basic colors: red, green, and blue. The color sensor scans what is in front of it and returns the basic color values of the scene. By comparing these returned values to predefined threshold values, we can easily determine what color an object in front of the robot is. 
:::

## Initialization
```java 
ColorSensor colorSensor;
colorSensor = hardwareMap.get(ColorSensor.class, "color_sensor");
```

## Implementation
:::caution
You should always be calling the `enableLED()` function before reading the color sensor as it helps to eliminate inaccuracies caused by lighting. 
:::

```java 
colorSensor.enableLed(true);
```

Find red, green, blue, or alpha values from 0-255

```java 
int red = colorSensor.red();
int blue = colorSensor.blue();
int green = colorSensor.green();
int alpha = colorSensor.alpha(); //0 - Transparent, 255 - Opaque
```

Return a combined argb color value by using the argb() command.
```java 
int combined = colorSensor.argb();
```

### Rev Color Sensor V3
The newest color sensor by Rev Robotics, version 3, has some extra capabilities, and therefore a different class to use for its functions.

#### Initialization
```java 
RevColorSensorV3 colorSensorV3 = (RevColorSensorV3) hardwareMap.colorSensor.get("colorV3");
```

:::info
The main difference between the Rev V3 and other color sensors is the `getDistance()` function, meaning the color sensor can return the distance of an object within a certain range, from 1 cm - 10 cm.
:::

```java 
colorSensorV3.getDistance(DistanceUnit.CM);
```

:::info
It is recommended that you do not use the V3 Color Sensor to actually determine the distance between your robot and an object, but instead use it to determine if an object is in front of the sensor, maybe to see if there is an object in a specific robot module.
:::

## Color Thresholding
It is commonly known that colors can be broken down into three basic colors: red, green, and blue. The color sensor scans what is in front of it and returns the basic color values of the scene. By comparing these returned values to predefined threshold values, we can easily determine what color an object in front of the robot is.

Because the resources listed above provide a sufficient explanation of how the code below works, we will just provide you with an implementation.  

```java 
package org.firstinspires.ftc.teamcode.Sensors;

import com.qualcomm.robotcore.hardware.ColorSensor;
import com.qualcomm.robotcore.hardware.DistanceSensor;
import com.qualcomm.robotcore.hardware.HardwareMap;

import org.firstinspires.ftc.robotcore.external.navigation.DistanceUnit;

public class RevColor {

    public ColorSensor internalColorSensor;
    public DistanceSensor internalDistanceSensor;
    // Base Color Threshold
    public final double RED_THRESHOLD_LEFT = 700, RED_THRESHOLD_RIGHT = 700;
    public final double BLUE_THRESHOLD_LEFT = 700, BLUE_THRESHOLD_RIGHT = 700;
    public final double RED_THRESHOLD = 550;
    public final double BLUE_THRESHOLD = 550;
    // Complex Color Thresholds (not red, green, or blue)
    public final double YELLOW_RED_VALUE = 0.419;
    public final double YELLOW_GREEN_VALUE = 0.3675;
    public final double YELLOW_BLUE_VALUE = 0.2116;
    public final double[] YELLOW_CONSTANTS = {YELLOW_RED_VALUE, YELLOW_GREEN_VALUE, YELLOW_BLUE_VALUE};
    public final double YELLOW_THRESHOLD = 0.12;

    public final double WHITE_RED_VALUE = 0.30125;
    public final double WHITE_GREEN_VALUE = 0.37125;
    public final double WHITE_BLUE_VALUE = 0.325;
    public final double[] WHITE_CONSTANTS = {WHITE_RED_VALUE, WHITE_GREEN_VALUE, WHITE_BLUE_VALUE};
    public final double WHITE_THRESHOLD = 0.01;
    public final double WHITE_TOTAL_COUNT = 800;
    
    // check for black with the alpha value
    public final double BLACK_ALPHA_VALUE = 325; //Test value

    public RevColor(HardwareMap hardwareMap, String name){
        this.internalColorSensor = hardwareMap.get(ColorSensor.class, name);
        this.internalDistanceSensor = hardwareMap.get(DistanceSensor.class, name);
    }

    public double distance(){
        return internalDistanceSensor.getDistance(DistanceUnit.CM);
    }
    
    
    // utility method for easily getting the color value. 
    public int red(){ return internalColorSensor.red(); }
    public int green(){ return internalColorSensor.green(); }
    public int blue(){ return internalColorSensor.blue(); }
        
    public double total(){ return red() + green() + blue(); }
    
    // convert to array
    public double[] rgb(){
        double[] arr = new double[3];
        arr[0] = red();
        arr[1] = green();
        arr[2] = blue();

        return arr;
    }
    
    // Scale the rgb values (0 to 1)
    public double[] normalizedRGB(){
        double[] arr = new double[3];
        double[] originalArr = rgb();

        double total = 0;
        for(double i : originalArr)
            total+= i;

        for(int i = 0; i < 3; i++){
            arr[i] = originalArr[i] / total;
        }

        return arr;
    }

    public double arrayError(double[] arr1, double[] arr2){
        double total = 0;

        for(int i = 0; i < arr1.length; i++){
            total += Math.pow(arr1[i] - arr2[i], 2);
        }

        return Math.sqrt(total);
    }

    public boolean isBlack(){
        return (internalColorSensor.alpha() < BLACK_ALPHA_VALUE) ? true : false;
    }

    public int alphaValue(){
        return internalColorSensor.alpha();
    }

    public boolean isRed(){
        return internalColorSensor.red() > RED_THRESHOLD;
    }

    public boolean isBlue(){
        return internalColorSensor.blue() > BLUE_THRESHOLD;
    }

    public double yellowError(){ return arrayError(normalizedRGB(), YELLOW_CONSTANTS); }
    public double whiteError(){ return arrayError(normalizedRGB(), WHITE_CONSTANTS); }

    public boolean isYellow(){
        return yellowError() < YELLOW_THRESHOLD || (internalDistanceSensor.getDistance(DistanceUnit.CM)>6 && whiteError() > 0.02);
    }

    public boolean isWhite(){
        return whiteError() < WHITE_THRESHOLD && total() > WHITE_TOTAL_COUNT;
    }
    
    public String normalizedValues() {
        double red = internalColorSensor.red();
        double green = internalColorSensor.green();
        double blue = internalColorSensor.blue();

        double total = red + green + blue;
        return String.format("RGB: %.2f %.2f %.2f", red / total, green / total, blue / total);
    }
    
    // turn on the lights
    public void enableLED(boolean LEDMode){
        internalColorSensor.enableLed(LEDMode);
    }

    public boolean withinColorRange(){
        return isYellow() || isWhite();
    }
}
```

### Advanced Version
```java 
public ColorSensor internalColorSensor;
public DistanceSensor internalDistanceSensor;
// Base Color Threshold
public final double RED_THRESHOLD_LEFT = 700, RED_THRESHOLD_RIGHT = 700;
public final double BLUE_THRESHOLD_LEFT = 700, BLUE_THRESHOLD_RIGHT = 700;
public final double RED_THRESHOLD = 550;
public final double BLUE_THRESHOLD = 550;
// Complex Color Thresholds (not red, green, or blue)
public final double YELLOW_RED_VALUE = 0.419;
public final double YELLOW_GREEN_VALUE = 0.3675;
public final double YELLOW_BLUE_VALUE = 0.2116;
public final double[] YELLOW_CONSTANTS = {YELLOW_RED_VALUE, YELLOW_GREEN_VALUE, YELLOW_BLUE_VALUE};
public final double YELLOW_THRESHOLD = 0.12;

public final double WHITE_RED_VALUE = 0.30125;
public final double WHITE_GREEN_VALUE = 0.37125;
public final double WHITE_BLUE_VALUE = 0.325;
public final double[] WHITE_CONSTANTS = {WHITE_RED_VALUE, WHITE_GREEN_VALUE, WHITE_BLUE_VALUE};
public final double WHITE_THRESHOLD = 0.01;
public final double WHITE_TOTAL_COUNT = 800;

// check for black with the alpha value
public final double BLACK_ALPHA_VALUE = 325; //Test value

public RevColor(HardwareMap hardwareMap, String name){
    this.internalColorSensor = hardwareMap.get(ColorSensor.class, name);
    this.internalDistanceSensor = hardwareMap.get(DistanceSensor.class, name);
}

public double distance(){
    return internalDistanceSensor.getDistance(DistanceUnit.CM);
}


// utility method for easily getting the color value. 
public int red(){ return internalColorSensor.red(); }
public int green(){ return internalColorSensor.green(); }
public int blue(){ return internalColorSensor.blue(); }
    
public double total(){ return red() + green() + blue(); }

// convert to array
public double[] rgb(){
    double[] arr = new double[3];
    arr[0] = red();
    arr[1] = green();
    arr[2] = blue();

    return arr;
}

// Scale the rgb values (0 to 1)
public double[] normalizedRGB(){
    double[] arr = new double[3];
    double[] originalArr = rgb();

    double total = 0;
    for(double i : originalArr)
        total+= i;

    for(int i = 0; i < 3; i++){
        arr[i] = originalArr[i] / total;
    }

    return arr;
}

public double arrayError(double[] arr1, double[] arr2){
    double total = 0;

    for(int i = 0; i < arr1.length; i++){
        total += Math.pow(arr1[i] - arr2[i], 2);
    }

    return Math.sqrt(total);
}

public boolean isBlack(){
    return (internalColorSensor.alpha() < BLACK_ALPHA_VALUE) ? true : false;
}

public int alphaValue(){
    return internalColorSensor.alpha();
}

public boolean isRed(){
    return internalColorSensor.red() > RED_THRESHOLD;
}

public boolean isBlue(){
    return internalColorSensor.blue() > BLUE_THRESHOLD;
}

public double yellowError(){ return arrayError(normalizedRGB(), YELLOW_CONSTANTS); }
public double whiteError(){ return arrayError(normalizedRGB(), WHITE_CONSTANTS); }

public boolean isYellow(){
    return yellowError() < YELLOW_THRESHOLD || (internalDistanceSensor.getDistance(DistanceUnit.CM)>6 && whiteError() > 0.02);
}

public boolean isWhite(){
    return whiteError() < WHITE_THRESHOLD && total() > WHITE_TOTAL_COUNT;
}

public String normalizedValues() {
    double red = internalColorSensor.red();
    double green = internalColorSensor.green();
    double blue = internalColorSensor.blue();

    double total = red + green + blue;
    return String.format("RGB: %.2f %.2f %.2f", red / total, green / total, blue / total);
}

// turn on the lights
public void enableLED(boolean LEDMode){
    internalColorSensor.enableLed(LEDMode);
}

public boolean withinColorRange(){
    return isYellow() || isWhite();
}
```
