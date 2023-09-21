---
sidebar_position: 5
---
# Kicker 

A kicker is a commonly used feeding mechanism that transports objects through a robot. This is typically accomplished by making a fast servo oscillate between two positions rapidly. 

A use case for a kicker can be found in the FTC 2020-2021 game Ultimate Goal. Our robot made use of a kicker to feed in the rings that were collected by our intake sweeper into our flywheel shooter. 

<iframe width="100%" height="422" src="https://www.youtube.com/embed/42KxhA5kUL0" title="Control Award State Submission" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

It is clear that servos need some amount of time to reach a given position, thus setting the close position of the kicker right after the open position will cause the servo to not move at all. Instead what you should do is maintain a timer of how long the servo has been running to reach a certain position. When the timer passes the threshold, make the servo move back to the original position to complete the oscillation. 

We can implement this idea using the following steps: 
* Set a variable indicating the total amount of time one complete oscillation of the servo should take (moving from the start position to the end position and back to the start). 
* When the designated button for the kicker is not being held, constantly reset the timer so the kicker doesn't move from the start position. 
* When the button is being held, the timer will continue running until it reaches a point where it is high enough that you can make the servo go to its second position. 
* As the timer continues running even higher you will reach a point where the servo can go back to its original position, completing the oscillation. 

:::info
We understand that this description can be quite confusing, but please take a look at the code, as a practical implementation may be more intuitive. Worst case, to use this code you simply need to replace the kicker bounds with positions you need your kicker to oscillate between and adjust your kickerCycleTime variable as needed. 
:::

The following code should make this description clearer.  
```java 
@TeleOp(name = "KickerDemo", group = "FTC_Guide")
public class KickerDemo {
    Servo kicker; // servo
    double kickerCycleTime = 500; // total amount of milliseconds you want 
    //one oscillation to take
    boolean retDone = false, pushDone = true,
    ElapsedTime kTime = new ElapsedTime();
    double kickerEngPos = 0, kickerOpenPos = 1; // kicker positions 
    
    @Override    
    public void init() {
      kicker = hardwareMap.servo.get("kicker");
    }

    @Override
    public void start(){
        
    }

    @Override
    public void loop() {
             // Kicker (press and hold x to oscillate the servo)
            if(!gamepad2.x){
                kTime.reset(); // reset the timer when idle. 
                if(pushDone && !retDone){
                    kicker.setPosition(kickerOpenPos);
                }

                retDone = false;
                pushDone = false;
            }

            if(kTime.milliseconds() % kickerCycleTime > 10 && kTime.milliseconds() % 
            kickerCycleTime < kickerCycleTime / 2 && !pushDone){
                pushDone = true;
                retDone = false;
                kicker.setPosition(kickerEngPos);
            } else if(kTime.milliseconds() % kickerCycleTime > kickerCycleTime / 2 &&
             !retDone){
                pushDone = false;
                retDone = true;
                kicker.setPosition(kickerOpenPos);
            }
    }

    @Override
    public void stop(){
        
    }
}

```