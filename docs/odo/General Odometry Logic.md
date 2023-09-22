---
sidebar_position: 2
---
# General Odometry Logic

The foundation of any autonomous movement system in a robot is the implementation of an accurate odometry localization algorithm. It allows the robot to accurately keep track of the its current position on the field. 

On the surface the current position of the robot may seem to have little value, however, it is actually a key value in many navigation algorithms which allows the robot to traverse any point of the field.

### Logic

General Logic As mentioned above, odometry allows the robot to keep track of its current position, relative to its origin. The algorithm starts by storing the robot’s initial position on the field which can be represented mathematically using a simple pose matrix:

$$
O = \begin{pmatrix}
x_0 \\
y_0\\
\theta_0
\end{pmatrix}
$$

After the origin point is known, odometry then returns the amount that it has moved from its current position, also known as displacement. 

We then add the displacement value to the robot's origin position to find the current position. This process is repeated every few milliseconds to ensure the position that is stored is as accurate as possible to the true position of the robot. The equation used to update the position continuously can be mathematically represented below:

$$
\begin{pmatrix}
x \\
y\\
\theta
\end{pmatrix}
 = O +
\begin{pmatrix}
\Delta x \\
\Delta y\\
\Delta \theta
\end{pmatrix}
$$

Simply explained, the current position of the robot is equal to the origin of the robot, the $O$ matrix, plus the displacement of the robot, the $\Delta$ matrix.  The $\Delta x$, $\Delta y$, $\Delta \theta$ and  symbols in the matrix represent the amount that the robot has strafed (sideways movement), moved forward, and turned, respective to the robot's origin.

Now that we know that odometry works by updating its position by using the robot’s origin or previously found position value and summing it by the robot’s displacement, a key question may be asked: How is the displacement value of the robot calculated?

Teams use odometry input sources, essentially specialized sensors, that return raw position data, and when processed, the displacement of the robot can be calculated. These sensors used as well as their implementations in code will be covered in the following sections.

## General Implementation
When implementing this process practically, the logic from the theoretical explanation above mostly carries over, however, in the code, there are a few small changes to the equation. At the beginning of the program, a current-position variable is initialized to the robot's starting point.

Then in every other iteration of the code, this value is updated continuously by adding the displacement values, or the $\Delta$ matrix, received from the odometry input sources. This means that the current-position matrix is set to the $O$ matrix to store the origin, and as the program starts it updates itself by summing itself by the displacement from the previously calculated position. This in turn leaves us with a modified equation, which is displayed in the pseudocode below

```java 
void updatePosition(){
    Point currentPosition = (0, 0, 0); // initialized to origin 
    // point, equal to $O$ matrix; changes
    // depending on the starting point of the robot on field
    Input OdometryInputSource = new Input();
    
        /* By continuously summing the amount the robot has moved from the previously 
        calculated position, the robot can be localized.
        Theoretical Equation: currentPosition = origin + overallDisplacement
        
        New Equation: currentPosition = 
        previouslyCalculatedPosition + displacementFromPreviousPosition
        */
    while(matchIsOccurring){
        Point displacement = OdometryInputSource.getDisplacement(); // receives
        // the $\Delta$ matrix of the equation
        

        currentPosition.x = currentPosition.x + displacement.x;
        currentPosition.y = currentPosition.y + displacement.y;
        currentPosition.theta = currentPosition.theta + displacement.theta;
    }
}
```