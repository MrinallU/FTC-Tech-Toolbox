---
sidebar_position: 6
---
# Gamepad Controls



In the driver-controlled period of the FTC match your drivers use controllers to drive and control robots
### Controllers
Currently, there are four controllers legal for FTC use.
* [XBOX 360 Wired Controller](https://www.amazon.com/Microsoft-Wired-Controller-Windows-Console/dp/B004QRKWLA)
* [Logitech F310 Wired Controller](https://www.amazon.com/Logitech-940-000110-Gamepad-F310/dp/B003VAHYQY/ref=asc_df_B003VAHYQY/?tag=hyprod-20&linkCode=df0&hvadid=385267839105&hvpos=&hvnetw=g&hvrand=10943927897885031362&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1015153&hvtargid=pla-329121721635&psc=1&tag=&ref=&adgrpid=77420502894&hvpone=&hvptwo=&hvadid=385267839105&hvpos=&hvnetw=g&hvrand=10943927897885031362&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1015153&hvtargid=pla-329121721635)
* [ETPark Ps4 Wired Controller](https://www.amazon.com/Controller-Etpark-Playstation-Vibration-Anti-Slip/dp/B086QZ1Z67/ref=asc_df_B086QZ1Z67/?tag=hyprod-20&linkCode=df0&hvadid=642173926262&hvpos=&hvnetw=g&hvrand=15950086324327018534&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1015153&hvtargid=pla-949390119106&psc=1&gclid=Cj0KCQjw98ujBhCgARIsAD7QeAgZ6yP5Fku1masZEQLRXgWx2b4PIxxOQOPODWaCC19tnuYka_DMPX4aAsIEEALw_wcB)
* [Dual Shock Ps4 Controller](https://www.amazon.com/DualShock-Wireless-Controller-PlayStation-Black-4/dp/B01LWVX2RG/ref=sr_1_3?hvadid=557328939520&hvdev=c&hvlocphy=1015153&hvnetw=g&hvqmt=e&hvrand=1941213234762459107&hvtargid=kwd-317071107017&hydadcr=22934_13472370&keywords=ps4+dualshock+controller&qid=1685318366&sr=8-3)

:::caution
Even though the Dual Shock 4 Controller has wireless capabilities, a USB must be used connect it to the driver station. Wireless controller connections are not allowed for FTC.
:::

### What Controller Should I Use?

The usual consensus for the best controller available is the Dual Shock 4. The joysticks, triggers, and buttons feel better than the ones on other controller, as well as the fact that it has a touchpad. It also provides haptic feedback with the ability to vibrate to tell your drivers something, however this controller also comes with the highest price tag. If looking for a cost effective option, our team recommends the Logitech F310 controller as it is found at most stores for $20, and it is a reliable controller.'
### Gamepad Controls
The gamepad has two main types of input. A Boolean output that returns either true or false depending on if the button is the pressed or not. The other type is a double output returning a 0-1 double.

### Boolean Outputs

* Buttons A, X, Y, B
* D-pad Buttons
* Left and Right Bumpers
* Misc Buttons(Share, Options, Start, Touchpad)

### Double Outputs
* Left and Right Triggers
* Both Joysticks

### Assigning Gamepad Controls
When referencing a gamepad in the code, you can use either gamepad1 or gamepad2 which refers to either the Driver 1 or Driver 2 controller. This will be followed by the button that you are trying to assign a command to.

```java 
gamepad1.a                  //a button on Driver 1 Controller
gamepad2.dpad_up            //dpad up on Driver 2 Controller

gamepad1.left_stick_x;      //0-1 value based on left horizontal joystick movement
gamepad1.left_trigger;   //0-1 value based on trigger position 
```

:::info
When using the y value for joysticks, moving the joystick down will return a positive value and moving it up will return negative. If you wish to switch it simply multiply the returned value by -1.
:::

### Assigning a Button to a Task
Here is some basic code to move a servo to different positions depending on when a button is it. Assume a servo has already been initialized.
```java 
if(gamepad1.a){
    servo.setPosition(0.5);  //Task if a is pressed
}else if(gamepad1.b){
    servo.setPosition(1);    //Task if b is pressed
}
```

### Using a Double Output
There are two ways that you can use a double output, assigning the output as a power to a motor, or using it just like a button by making it return true or false. 
### Assigning Double Output to a Motor

One of the most common uses for the triggers and the joysticks is to assign the value outputted by them to a motor. The more pressed down or moved it is the higher power will be outputted by the motor. In this example, assume the motors sweeper and flywheel have already been instantiated.
```java 
double sweeperPower = gamepad1.right_trigger;
double flywheelPower = gamepad1.left_stick_y * -1;

sweeper.setPower(sweeperPower);
flywheel.setPower(flywheelPower);
```

As shown in this example, the right trigger value is assigned to the sweeper motor and the left joystick y axis value is assigned to the trigger motor. Remember, the y axis of a joystick initially returns a negative number going up, so to change that the power is multiplied by negative 1. 

:::info
To explain a little more about how the joystick is programmed with the gamepad, there are two joysticks on each gamepad, you reference which one you want to use by using left_stick or right_stick. Each joystick also has two axis, an x-axis, left and right movement, and a y-axis, up and down movement. Therefore, there are four different joystick values that can be obtained on each gamepad. This will be important when programming your drive train.
:::

### Using a Trigger/Joystick as a Button
In some cases, you might have used up all buttons on your gamepad and just want to use a trigger or joystick as a button to initiate another action. The solution is simple, create a boolean checking if the value outputted is larger then a small double value, and if so the "button" is being pressed.
```java 
boolean isPressed;

isPressed = gamepad1.right_trigger > 0.1; 

if(isPressed){
    //Task to Complete
}
```

### Detecting Button Presses
#### The Wrong Way
Now, in many cases, it is simpler for the driver to use the same button for different tasks regarding the same module. For example, toggling the positions of a servo, or turning a motor on and off. Your first idea for how to do this would look something like this. Assume servo is initialized.
```java
boolean downPosition = true; //alternating between down and up

if(gamepad1.a){
    downPosition = !downPosition; //switches position
    if(downPosition){
        servo.setPosition(0); //Moving down
    }else{
        servo.setPosition(1); //Moving up
    }
}
```

This logic says that if 'a' has been pressed, to change the value of the `downPosition` boolean. If it has been changed to true, the servo should move to the down position, if it has been changed to false, the servo should be moved up.

#### Why Wouldn't this Work?
The logic may seem right but there is a reason this wouldn't work. As you know, FTC tele-op's run in loops. These loops keep looping at rapid speeds, one loop only takes a few milliseconds. Therefore, if you press the 'a' button the program will loop many times while you are still pressing it, causing the servo to try to move up then immediately down, and so on as the program keeps looping. All this will do is result in a servo that jitters when you press 'a'. 

#### The Right Way
```java 
boolean lastMovement = false, currMovement = false;
boolean downPosition = true;

lastMovement = currMovement;
currMovement = gamepad1.a;    

if(currMovement && !lastMovement){
    downPosition = !downPosition;
    if(downPosition){
        servo.setPosition(0); //Move Down
    }else{
        servo.setPosition(1); //Move Up
    }
}
```

This may seem confusing, but there is a logical explanation behind this code. Here we are setting up two booleans `lastMovement` and `currMovement`. We set `lastMovement` equal to `currMovement`, and afterward, we set `currMovement` to the boolean gamepad1.a.

Now, we only enter the if statement to switch the servo position if `currMovement` is true and `lastMovement` is false. The first loop when 'a' is pressed will result in the `lastMovement = currMovement` line having both booleans at false. 

However, the next line will change `currMovement` to true, this will allow the servo position to change. However, on the next loop, since the button is still being pressed, in the next loop, the `lastMovement = currMovement` line will set `lastMovement` to true, leading to both `currMovement` and `lastMovement` being true, meaning the statement will not be executed. Therefore, this code will ensure that an action will only occur once for each press of a button.

