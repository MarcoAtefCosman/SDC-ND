# Introduction to Sensor Fusion
### Why sensor fusion is important
- Sensor fusion is an essential part of self-driving cars because it improves single-sensor-results and compensates for sensor failures or weaknesses.
- Every sensor has advantages and disadvantages and may fail from time to time, for example: What if the camera is blinded by the sun? What if the view is disturbed by rain drops? Also, the camera image is a 2D projection of the 3D world, so how can we measure the accurate distance of other vehicles? To solve these challenges, additional sensors like radars or lidars are needed. They have different physical measurement principles and can improve the reliability of the environment perception system.

### Overview of autonomous vehicle sensors
- Autonomous driving will not be possible with only a single sensor or even sensor type.
- In order to build an autonomous system that performs equally well as a human driver (and better), we will need to use multiple sensor types with complementary properties that can compensate each others weaknesses.

### History of sensor fusion and perception
- Lidar is an active sensor, which means that it does not rely on ambient signals such as sunlight. Just like a camera, lidar is an optical sensor, which emits a beam of laser light, that reflects off of surfaces and returns to the sender. Based on the time-of-flight (ToF) of the beam of light, the lidar sensor can calculate the distance to the reflective target.
- Lidar technology has been around since the 1930s and among its first applications was the "Lunar Ranging Experiment" in 1969 on board Apollo 11. Later it was used as an airborne sensor to create accurate topological maps and elevation models, for example in archaeology.
- One of the first successful uses in automotive occurred in 2005, when the Stanford Racing Team won the DARPA Grand Challenge. Since then, significant improvements of LiDAR technology have been developed, making it one of the cornerstones of the autonomous vehicle sensor suite.
- A problem of Lidar sensors is the price, which is still orders of magnitude higher than for other established sensors such as radar. However, it can be expected that within 3-5 years, the technology will be miniaturized and cheap enough to be used in mass production.
- Most tracking algorithms were developed in the context of air surveillance and military applications. For example, the famous Kalman filter for tracking dates back to the 1960s, where it helped fly to the moon during Apollo space program.
- **The first tracking applications did not use sensor fusion, but mainly relied on radar data alone.** The same holds true for the first driver assistance systems. For example, the first adaptive cruise control systems around the year 2000 were realized with radar only, and the first parking systems were realized with ultrasonic sensors alone.
- It was only with the rise of advanced driver assistance systems and automated driving that single sensors reached their limits and more effort was put into developing sensor fusion systems. Today, most driver assistance systems rely on several sensors, and automated vehicles mostly use one central fusion system. The sensor fusion module creates one consistent model of the environment, and different assistance systems and applications use the same fused world representation.

