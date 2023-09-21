---
sidebar_position: 5
---
 
# Servos



Usually in FTC, Servos are used where not as much power is required to move mechanism. Servos are used for movement of smaller things, or for continuous rotation of certain mechanisms. They result in more fine movement as they can move between positions from 0-1.
## Servo Initialization

Just like for Motors, servo initialization references the hardwareMap object to instantiate the Servo. The main difference in initialization of a servo is the usage of the Servo object when creating the servo variable as well as using Servo.class in the hardwareMap configuration.
```java
Servo claw;
claw = hardwareMap.get(Servo.class, "claw");
```
## CR Servos

CR Servos, or Continuous Rotation Servos are a mode for servos where the servo continuously rotates, similar to the movement of a motor. This can be used in many situations such as the spinning of a small wheel. For example, many teams used CR Servos for the spinning of the carousel wheel in Freight Frenzy.

### CR Servo Initialization
CR Servos have a slightly different initialization
```java 
CRservo wheel;
wheel = hardwareMap.get(CRServo.class, "wheel);
```
## Setting Servo Position
The `setPosition` method takes in a 0-1 double value and sets the servo to a specific position given to it. It is used for the normal non CR servos.
```java
claw.setPosition(0.5);
```
## Setting CR Servo Powers
For, CR Servo usage, you must set it to a certain power. It is the setPower method, the line looks very similar to setting power to a motor. 
```java 
wheel.setPower(0.5);
```
:::info
To find more servo methods with descriptions of what they do, they can be found [here](https://ftctechnh.github.io/ftc_app/doc/javadoc/com/qualcomm/robotcore/hardware/Servo.html)
:::

:::caution
The servo has a method called get position. However, due to the way the servo receives this data, it is not the actual current position of the servo. It is the postition that the servo is set to be going to. Therefore, there is not big usage for this method in your program. 
:::

## Exercise

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

```mdx-code-block
<Tabs>
<TabItem value="Task">
```
Create a new servo object and name it a claw. Crop it's range to set its new bounds to be between the original 0.2 and 0.8 value. Then, with its new bounds, set it to a position of 0.4;

Hint: Look at page with all servo methods.

Next, initialize a servo for a intake wheel you will spin to intake objects. Initialize the servo with the variable and configuration name of "intake". Then set it to run at 75% of its speed.
```mdx-code-block
</TabItem>
<TabItem value="Solution">
```

```java
Servo claw;
CRservo intake;

claw = hardwareMap.get(Servo.class,"claw");
intake = hardwareMap.get(CRServo.class, "intake");

claw.scaleRange(0.2, 0.8);

claw.setPosition(0.4);
intake.setPower(0.75);
```

```mdx-code-block
</TabItem>
</Tabs>
```