---
sidebar_position: 10
---
 
# The Sleep Command

The FTC SDK provides a `sleep()` command in which you can give a desired number of milliseconds to the function and it will cause your thread to sleep for that number of milliseconds. We will be using this command extensively when writing autonomous programs. 

```java 
sleep(500); //sleeps for 500 ms
```

Sleep can be useful in auto to wait between doing certain tasks, however, **do not use the sleep command in tele-op**. It causes the whole thread to sleep and whatever movement the loop last received will continue, causing your robot to go haywire.

:::caution
It is also highly advised to not use while loops during tele-op. Similar to the sleep, it can cause your robot to go haywire, due to the way it runs. If you need something similar to while loops or sleeps in your tele-ops, look into our **State Machines and Timers Section**.
:::