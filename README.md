# SKYWARD BOUND
I developed Skyward Bound during my time at Harvard Summer School as part of the course DGMD S-17: Robotics, Vehicles, Drones, AI.

Skyward Bound aims to open a gateway to the concept of sky-based autonomous drone racing. Skyward Bound is a drone simulator that replicates racing conditions for a 3D drone that is programmed to detect and maneuver around obstacles in its path to reach the finish line in the shortest amount of time possible. The simulator environment and race courses were built on Unity and Unity’s inbuilt ray-casting system was used to implement obstacle avoidance. This simulation has 3 different racetracks to portray various situations and environments the drone may encounter in a real world race.

I was inspired by high school robotics competitions such as FIRST, and I surmised that the next generation of challenges could be done via 3D drones rather than 2D robots. My prototype includes 3 race tracks, randomized obstacles (trees/cacti/rocks), and a flying drone which uses ray casting for object detection.

In real life, drone races would probably start off with A2 level drones that would use CV only to assist manual drone racers. However, just like modern robotics competitions are now using I modeled my prototype as if it were an A4 drone (which is what I envision the completed stage of drone racing will be in the far future). I believe that this sort of autonomous drone racing competition would allow both companies to find talented CV-specialized candidates and stem further research into CV/object-detection algorithms for drones.

# SYSTEM ARCHITECTURE
1. Observer Pattern
   - Drone shoots out rays to detect collisions.
   - Any collisions that rays encounter are sent as an event to the controller.
   - The controller (object using the command pattern) decides how to evade the object by switching the state, confirms that each step has been taken, and returns the state.
2. Events
   - An event occurs when an object is detected.
   - Ex: A balloon obstructs the drone's path to the right. The observer creates an event “right_collision” and switches state to “move_left” flight, decides exactly what direction drone should move (via a vector) based on current speed and location of the object, sends a command to move, confirms the move with a re-measurement, and switches the drone back to “normal” flight.
3. Modes of Flight
   - Normal Flight: Straight lines are detected and/or there are no obstacles.
   - Evasive Flight: Drone is in motion and there is an approach  within the path of the drone.
   - Reassessment Flight: Drone needs to stop and find a new path and return to Normal flight.

![image](https://github.com/user-attachments/assets/37e85acf-10a1-4c4a-af1a-42e1387c63c8)

# DESIGN ITERATIONS
1. Original Design:
   - Use OpenCV in Python to determine frame-by-frame which point to move to optimally avoid all obstacles and be as efficient as possible.
   - Issue: For reasons such as expenses and lack of support, using OpenCV with C# was not desirable.
2. Iteration 1:
   - Build a Python server with a UDP connection to the drone’s camera in Unity.
   - Issue: Unfeasibly slow for this project’s purposes (we were reaching frame rates as low as 4 FPS).
3. Iteration 2 (Final Design):
   - I referred to the MIT lecture by Lex Fridman on self-driving cars4 and learned that there are two hotly debated methods: using computer vision like I originally planned to do, or to use SLAM (Simultaneous Localization and Mapping) techniques such as Lidar to shoot lasers using satellites to determine points of collision.
- I determined that I could use Unity’s in-built ray-casting feature to shoot rays in four different directions (left, right, up, and down) to “predict” collisions before they occur and correct for them.

![image](https://github.com/user-attachments/assets/8851d630-1289-42b6-9eff-d669aa8a67d2)

# SIMULATION CONTROLS
1. Detection Radius: how far the rays shoot outwards
   - A ray too small will not properly detect collisions fast enough, however a ray too big can cause early assessment and lead to premature decisions.
2. Forward Speed: indicates how fast the drone moves forward
3. Turn Speed: how fast the drone corrects itself in evasive flight once the ray detects a collision ![image](https://github.com/user-attachments/assets/28334225-619b-4008-89ff-ad3c2e0f78af)

# RACE COURSES
1. Course 1:
   - Designed to test drone’s performance  in tight spaces
   - Drone tries to reach the blue area at the end of the rectangular box and avoids the obstacles in its path
   - Drone faced some problems with colliding into the obstacles from time to time due to the rays from ray-casting not rendering the colliders
   - I solved this issue by switching from box colliders to mesh colliders. Box colliders are usually expected to be more efficient as they require far less surface space, but the rays that are cast seemed to work better with mesh colliders even though this required more polygons.
    ![image](https://github.com/user-attachments/assets/8a114b3e-9b0f-47ab-9f1f-ff6a2f000eb8)

2. Course 2 & 3:
   - Designed to test drone’s performance  in increasingly wider and more open spaces akin to real-life drone-racing competitions
   - In general, drone performed much better on Courses 2 & 3 than on Course 1 - May be because of the drone’s difficulty to correct itself when so many of the rays that the drone shoots out returns a collision
     ![image](https://github.com/user-attachments/assets/d3b15f01-9b38-4eea-943f-1af15d76223b)
     <img width="149" alt="image" src="https://github.com/user-attachments/assets/b44ca58f-5f78-4bb9-97af-0bca0fd584bb">

# LEARNINGS & CONCLUSIONS
1. OpenCV Natively in Unity:
   - Surprisingly, OpenCV is not a simple integration into Unity. There are forums such as StackOverflow where people have been asking for years “how to add OpenCV to Unity?” and there’s no simple solution to the problem. There is a $100 plugin you can buy for Unity, but that is not practical for many people, especially students. There are a couple of free plugins for Unity to add OpenCV to Unity, but those have not been updated for years and do not support the latest versions of OpenCV or Unity.
2. Unity Performance:
   - There is no “out of the box” solution to develop a method to a screenshot in Unity for each frame. Unity provides two opportunities to read the frames of the screen. The first is while the frame is being generated. The problem with this approach is that you will end up with frames which are incomplete.
3. Ray-casting with Unity:
   - Another issue I had was determining how to properly ray-cast with Unity. Sometimes the rays were finicky and the correction velocities were off, so figuring out the exact parameters for each level took quite a while. On top of that, I had to experiment with many types of rays to find the most optimal angles for the rays. I tried 4 rays, 6 rays, and even 8 rays, but I decided that for the sake of this project, the simplicity that comes with 4 rays allows us to debug much quicker.
4. Unity and Python Bridge to support OpenCV:
   - Another challenge with the UDP server was the maximum size of bytes that are allowed for a UDP message would not enable sending a base64 encoded image of even 640x480 resolution. This meant that images could not be transmitted between Unity and Python in memory. While it is possible that a message queue could have been created, the amount of work to integrate a message queue into Unity and Python was determined to not be a good usage of time.
   - The result then was to have C# scripts in Unity write the screenshot frame to disk and send the name of the file written to disk as the message on the UDP server. Python would receive the message and read the image from disk.

# DEMO


https://github.com/user-attachments/assets/9ae3730d-b510-4a33-9b67-8ab0cb3f3351


Software Used: Unity, Python, C#

References:
MIT Lecture by Lex Fridman: https://www.youtube.com/watch?v=sRxaMDDMWQQ&feature=youtu.be
