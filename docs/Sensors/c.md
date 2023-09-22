# Distance Sensors

The REV 2M distance sensor is the best bet for teams looking to make use of a distance sensor in their code. This time-of-flight sensor is very useful for determining the robots distance from objects, up to 2 meters. 

## Implementation

Initialize the Sensor
```java 
DistanceSensor distance_sensor;
distance_sensor = hardwareMap.get(DistanceSensor.class, "distance");
```

Finding the distance returned by the sensor

```java 
distance_sensor.getDistance(DistanceUnit.INCH);
//Units can be MM, CM, INCH, or METER
```

### Using the Sensor Values
You can use the sensor to do an action if it is below a certain distance threshold to an object.

```java 
distance = distance_sensor.getDistance(DistanceUnit.INCH);
if(distance < 10){
    //Certain Action
}
```
You can also use the sensor to do something if it is more than a certain distance away from an object.
```java 
distance = distance_sensor.getDistance(DistanceUnit.INCH);
if(distance > 10){
    //Certain Action
}

```