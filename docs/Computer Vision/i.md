# Object Distance Estimation

This module shows you how to estimate the distance of a detected game element. 

For mono camera depth estimation use this tutorial: 

https://medium.com/mlearning-ai/distance-estimation-with-single-camera-opencv-python-298a96383c2b

The following tutorial only covers finding the object's distance on the z-axis (how far in front the object is compared to the camera).  However to navigate to the object we will need the distances on the x and y axis (how far the object is to the side of the camera and above the camera). Our code will implement the specified additions. For more information on the derivation of these equations, refer to page 5 of this paper: 

https://github.com/MrinallU/Image-Depth-Estimation-Via-Stereo-Vision/blob/master/Paper%20(1).pdf

## Implementation
:::info
Here are a couple of important notes about the tutorial:
* The focal length for your webcam can easily be found through a simple Google search (ex. logitech xyz focal length)
* Remember all of the measurement values must be in the same units, so make sure your code converts the units correctly.
* To attain the pixel width of the object (object_width_in_frame) you must measure the width of the detected object's bounding box.
:::

Your distance computation code should look something like this: 
```java 
/*Finds the distance of a detected object in real-world units
* camera_center_pixel_x = horizontal_resolution/2
* camera_center_pixel_y = vertical_resolution/2
* Focal_Length: focal length of the camera
* real_object_width: measured width of the game element (must be in the same units as the focal length)
* object_width_in_frame: width of the bounding box (in pixels)
*/
public double[] Distance_finder(
double Focal_Length, 
double camera_center_pixel_x, 
double camera_center_pixel_y,
double real_object_width, 
double object_width_in_frame, 
double object_x_pixel_coordinate, 
double object_y_coordinate){ 
    // how far in front the object is from the camera
    distance_z = (real_object_width * Focal_Length)/object_width_in_frame; 
    // how far to the side the object is from the camera
    distance_x = (distance_z / Focal_Length) *
    (object_x_pixel_coordinate - camera_center_pixel_x);        
    // how far above the object is from the camera 
    distance_y = (distance_z / Focal_Length) *
    (object_y_pixel_coordinate - camera_center_pixel_y); 
    return {distance, distance_x, distance_y}
}
```