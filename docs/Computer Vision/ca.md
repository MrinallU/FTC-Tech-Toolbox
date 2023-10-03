# Intro to FTC Vision Portal

As of the 2023-2024 season, there have been some changes made to the standard systems made for computer vision that teams have been used to. The FTC SDK has released a new Vision Portal, which incorporates all vision tools within the SDK without the need for external libraries.

# Major Changes
One major reason for these changes has been the new April Tag support within the FTC SDK, allowing for accurate reading of April Tags on the field with an inch of accuracy. The FTC uses its own VisionProcessor class now to process frames for april tags as well as for OpenCV, this is what you are using and extending in your classes from now on.

We will discuss setup and use with VisionPortal and the Vision Processor in the following pages.
