# Advanced Pure Pursuit

Although standard pure pursuit suffices for the vast majority of use cases, there are still some improvements that can be made to take your autonomous movement system to the next level. 

### Adaptive Pure Pursuit
Unlike a typical pure pursuit algorithm, an adaptive pure pursuit will adjust your lookahead threshold to allow your robot to navigate a path in a smoother fashion. 

#### Theory
https://www.chiefdelphi.com/t/paper-implementation-of-the-adaptive-pure-pursuit-controller/166552

#### Implementation
https://github.com/xiaoxiae/PurePursuitAlgorithm

https://github.com/Team254/FRC-2017-Public

### Creating Smooth Paths via Cubic Spline Interpolation

Rather than inputting a plain set of control points into your pure pursuit algorithm, we can instead interpolate a smooth curve that intersects all of the control points to ensure the robot follows a smooth path while hitting all of the desired locations.

#### Theory
https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiXxqTz_fj_AhXaSzABHSZZAsAQFnoECA4QAQ&url=https%3A%2F%2Ftimodenk.com%2Fblog%2Fcubic-spline-interpolation%2F&usg=AOvVaw0WI9K5K8iYnUMAStnXxdG_&opi=89978449

#### Implementation

https://github.com/MrinallU/Cubic-Spline-Interpolator