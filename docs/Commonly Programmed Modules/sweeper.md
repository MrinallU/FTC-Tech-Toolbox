---
sidebar_position: 6
---
# Active Intake / Sweepers

:::note Resources
* [GM0's page on active intake](https://gm0.org/en/latest/docs/common-mechanisms/active-intake/index.html) - - terminology/what is a sweeper
:::

A sweeper is a general term for a mechanism designed to pick up game elements using some component of rotational motion powered by a motor.

A sweeper is very easy to program, in most cases it is as simple as setting power to a motor. However, there you can implement certain features in your code which can make the sweeper more robust. Here are some examples: 

* Velocity control via encoders and PID
* Monitoring the sweeper motor's voltage to automatically detect any blockages and automatically rev the sweeper to resolve the issue.

## Implementation

:::info
Note that hooking an encoder up to the motor will automatically trigger the SDK's library to run, keeping your motor at a constant speed and greatly reducing the impact of battery level!
::: 

```java
sweeperMotor.setPower(0.5)
```

## Automatically Clearing Blockages

Sometimes your sweeper can get blocked due to various reasons. We can automatically resolve these blockages by constantly monitoring the sweeper voltage to see if it spikes beyond a certain amount. If the voltage exceeds the threshold then a blockage is detected and the sweeper rotates in the opposite direction to eliminate the blockage. 

:::info
You will need to experiment with the sweeper to find the proper voltage threshold. You can find this value by blocking your sweeper on purpose and recording the voltage your sweeper goes up to through telemetry, finally, copy this telemetry value into your threshold variable. 
:::

Paste this into your opMode, replacing variables as needed: 
```java
if(sweeperMotor.getCurrent(CurrentUnit.AMPS)>currentThreshold){
    sweeperMotor.setPower(reversePower); 
}
```

## Case Study
Take a look at how another top FTC team: TechNova makes use of their sweeper by implementing some of these techniques seen in this module. 

<iframe width="100%" height="422" src="https://www.youtube.com/embed/WBQp8gWczO8" title="12611 Freight Frenzy Control Award Submission" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

