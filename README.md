# Robotic-Arm-Manupulation-and-Object-Detection



https://github.com/Sk4587/Robotic-Arm-Manupulation-and-Object-Detection/assets/46374770/402dbb69-6813-4192-b8ab-f26a8f6d478a


## Objective

1. To count the total number of objects, excluding the black obstacles.
1. Determine which shape is more common (or if they are equal)
1. Pick up and place one example of the most common shape into the basket whilst
avoiding touching any of the obstacles


## Approach

1. A cartesian path is designed for smaller movements, and a circular path is designed for larger movements.

1. The robot arm moves to the desired positions by a circular path. Since the robot's base does not rotate 360 degrees, for the circular motion, we choose the direction of the circle as either clockwise or anticlockwise rotation to avoid completely rotating the robot's base.


### Planning and Execution

Preference is given to the robustness of the execution to maximise the task's success rate rather than focusing on the run time. Hence, a number of fail-safe methods have been added.

In general, the robot does the following.

1. The robot arm takes a circular path to the initial scan position and then a square path through the entire workspace.

1. The robot arm records all the observations of shapes that are not obstacles and stores their estimated shapes and centroids into a vector called `shape_centeroid_vector`. The centroid from the image frame is translated with respect to the base frame of the robot arm. By trial and error, the approximate focal length of the camera is estimated at around 1650. 

1. `shape_centeroid_vector` is then analysed. Objects and shapes with nearby centroids are grouped together and treated as one object, and its centroid is approximated as the average of all its observed centroids. We then store all the unique objects in the `unique_objects` vector, from which we estimate the total number of objects found, the shape of the most common object, and the number of objects of the most common shape. 

1. Sometimes, the detection of the target box fails. In that case, the robot arm searches for the target again at an elevated height at the corners at the back of the workspace (corners at the negative x-axis).

1. Although methods have been implemented to find the centroid of the objects during the scan, they are not accurate enough to perform a pick operation. Hence, the robot arm moves to the approximate estimated position and aligns itself until the object's centroid is at the centre of the image frame (320,240). A number of fail-safes have been implemented for this step. Sometimes, when the robot arm fails to detect the object, it takes too long to align itself or drifts away too much from the initial point. The robot arm resets its position and tries to scan for objects at different heights. If the execution takes more than 30 seconds, it returns the last estimated centroid.

1. Once the centroid is estimated, depending on the shape and orientation of the object, the robot arm adjusts its position and aligns itself in a pose to pick up the object. Once the object is picked, the robot arm drops the object at the target. 

1. The robot returns to its initial position after picking and placing.

During our testing, although the robot sometimes failed to detect some objects during the scan, it successfully picked and placed objects with multiple orientations and sizes about 90% of the time. 
