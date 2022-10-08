---
layout: post
title:  "HD map with JOSM & Lanelet2 - part 1"
date:   2022-10-08 14:20:08 +0800
categories: OP
---
## Summary
After getting a pointcloud map with for example [LeGo-LOAM](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM) in rosbag file, load it to JOSM with real scale for easier lane map drawing.

End results:

<img width="600" height="400" src="media/hd_map_with_josm/pcl_map.png">
<img width="300" height="200" src="media/hd_map_with_josm/lane1.png">
<img width="300" height="200" src="media/hd_map_with_josm/lane2.png">

## Prerequisite
* [ROS1](https://www.ros.org/), tested with melodic & noetic
* [JOSM](https://josm.openstreetmap.de)
* [Lanelet2](https://github.com/fzi-forschungszentrum-informatik/Lanelet2)
* [3D_Slam_tools](https://github.com/youliangtan/3D_Slam_tools)

###