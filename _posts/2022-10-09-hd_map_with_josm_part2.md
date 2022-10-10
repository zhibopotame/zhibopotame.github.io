---
layout: post
title:  "HD map with JOSM & Lanelet2 - part 2"
date:   2022-10-09 14:12:00 +0800
categories: OP
---

## Summary
After successfully loaded a map file from pcl rosbag in JOSM, we'll see how to draw a lane network, load it in Rviz with Lanelet2 and do global planning with mission planner.

1. [Install Lanelet2 plugin in JOSM](#1---install-lanelet2-plugin-in-josm)
2. [Draw lanes in JOSM](#2---draw-lanes-in-josm)
3. [Visualization in Rviz](#3---visualization-in-rviz)
4. [Global planning demo](#4---global-planning-demo)

End results:

<img width="1000" height="500" src="{{site.url}}/media/hd_map_with_josm/planning_preview.gif" alt="planning_preview">

## Prerequisite
* [ROS1](https://www.ros.org/) tested with melodic & noetic
* [JOSM](https://josm.openstreetmap.de)
* [lanelet2_map_loader](https://github.com/zhibopotame/lanelet2_map_loader)
* [lanelet2_global_planner](https://github.com/zhibopotame/lanelet2_global_planner)

## 1 - Install Lanelet2 plugin in JOSM
In [lanelet2_map_loader](https://github.com/zhibopotame/lanelet2_map_loader)->Lanelet2->lanelet2_maps->josm, there are 3 files: `laneletpresets.xml` `lanelets.mapcsss` `lines.mapcss`

Open JOSM
* Edit->Preferences->Map Settings -> Map Paint styles -> add (+) select `lanelets.mapcss` and `lines.mapcss`
* Edit->Preferences->Map Settings -> Tagging Presets -> add (+) select `laneletpresets.xml`

<img width="800" height="500" src="{{site.url}}/media/hd_map_with_josm/josm_plugin.png" alt="josm_plugin">


## 2 - Draw lanes in JOSM
Open JOSM and load your previous png file generated from rosbag, here for demo purpose:
* File->New Layer
* Imagery-> New picture layer from file...  ->[lanelet2_map_loader](https://github.com/zhibopotame/lanelet2_map_loader)->map_loader->data->map.png

Follow these two videos to draw lanes on your map image:

<iframe width="560" height="315" src="https://www.youtube.com/embed/3Yk8b8SB81o" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/em6H3DM63DM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Example result:

Open JOSM
* File->New Layer
* Imagery-> New picture layer from file...  ->[lanelet2_map_loader](https://github.com/zhibopotame/lanelet2_map_loader)->map_loader->data->map.png
* File->Open->[lanelet2_map_loader](https://github.com/zhibopotame/lanelet2_map_loader)->map_loader->data->hs_pcl.png

<img width="800" height="400" src="{{site.url}}/media/hd_map_with_josm/josm_with_lanes.png" alt="josm_with_lanes">

## 3 - Visualization in Rviz
After generating your own .osm file, in map_loader/launch/lanelet2_map_loader.launch, modify
{% highlight html %}
L1 <arg name="file_name" default="$(find map_loader)/data/hs_pcl.osm"/>
L5 <arg name="pointcloud_map_path" default = "$(find map_loader)/data/ds.pcd"/>
L6 <arg name="pcl_grid_map" default = "$(find map_loader)/data/map.yaml"/>
{% endhighlight %}

{% highlight shell %}
roslaunch map_loader lanelet2_map_loader.launch
{% endhighlight %}
Check pointcloud2 to see your pcd file.
<img width="800" height="400" src="{{site.url}}/media/hd_map_with_josm/rviz_with_lanes.png" alt="rviz_with_lanes">

## 4 - Global planning demo
{% highlight shell %}
roslaunch mission_planner mission_planning.launch
{% endhighlight %}
* use `2D Nav Goal` to send goal
* use `2D Pose Estimate` to modify start position

You can play with `path_resolution` in mission_planning.launch to smooth out the global path.

<img width="800" height="400" src="{{site.url}}/media/hd_map_with_josm/rviz_mission_planner.gif" alt="rviz_mission_planner">
