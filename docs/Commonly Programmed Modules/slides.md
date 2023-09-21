---
sidebar_position: 4
---
# Linear Slides

:::note Resources
* [FTC Team PIEaters Linear Slide Tutorial](https://www.youtube.com/watch?v=mbSP4dkfD2g)  
:::

### Implementation
:::info
The contents of this module will often apply to a variety of other modules are operate by two motors as well. For instance, double reverse fourbars or double jointed arms. 
:::
Programming linear slides is usually as simple as setting a viable target position to your motors that control the slides. Note that encoders are required to do this. 
:::danger
When operating two motors together, we highly recommend that you remove any modules from the motors and test your positioning functions first. This will ensure that your motors are moving in the desired direction. Not doing this may lead to you risking breaking your motors or the slides! If one motor is not moving the correct way try setting another target position or reversing its direction. 
:::
```java 
public void extendVerticalSlides(int verticalSlideExtendPos) {
    verticalLeftSlide.setTarget(verticalSlideExtendPos); // the position you want the slides to reach
    verticalLeftSlide.retMotorEx().setTargetPositionTolerance(1); // set accuracy to 1 tick
    verticalLeftSlide.toPosition();
    verticalLeftSlide.setPower(1); // raise at some power

    verticalRightSlide.setTarget(verticalSlideExtendPos);
    verticalRightSlide.retMotorEx().setTargetPositionTolerance(1);
    verticalRightSlide.toPosition();
    verticalRightSlide.setPower(1);
 }
```

### Exercise (One Encoder, Two Slides)
Due to a lack of encoder slots you may only have one encoder spot left to control both of your slides. To work around this issue you can use the encoder readings from one motor to control both slides via a custom PID loop. Note that this may not be as accurate as using two separate  encoders. 

```java
int error = leftSlide.getCurrentPosition() - destinationPosition;
leftSlide.executePID(error); 
rightSlide.exectutePID(error);  
```

### Case Study
[Take a look at the code of FTC Freight Frenzy world championship winning team, Delta Force](https://github.com/TAVii119/FTC-Freight-Frenzy-2021-2022)!
<iframe width="100%" height="422" src="https://www.youtube.com/embed/qnAq9K8zn3o" title="FTC Freight Frenzy | 17713 Delta Force Robot Reveal" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>