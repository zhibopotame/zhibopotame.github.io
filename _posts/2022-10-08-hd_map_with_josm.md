---
layout: post
title:  "HD map with JOSM & Lanelet2 - part 1"
date:   2022-10-08 14:20:08 +0800
categories: TECH
---
## Summary
After getting a pointcloud map with for example [LeGo-LOAM](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM) in rosbag file, load it to JOSM with real scale for easier lane map drawing.

1. [Convert bag to pcd](#1---convert-bag-to-pcd)
2. [Convert pcd to bt](#2---convert-pcd-to-bt)
3. [Convert bt to pgm map file](#3---convert-bt-to-pgm-map-file)
4. [Visualization in JOSM](#4---visualization-in-josm)




End results:

<!-- <img width="600" height="400"  src="{{site.url}}/media/hd_map_with_josm/pcl_map.png" alt="PCL Map" style="margin-bottom:5px" > -->
<img width="600" height="400" src="{{site.url}}/media/hd_map_with_josm/lane1.png" alt="pcl map with lane 1" style="margin-bottom:5px">
<img width="600" height="400" src="{{site.url}}/media/hd_map_with_josm/lane2.png" alt="pcl map with lane 2">

## Prerequisite
* [ROS1](https://www.ros.org/) tested with melodic & noetic
* [pcl-ros](http://wiki.ros.org/pcl_ros)
* [JOSM](https://josm.openstreetmap.de)
* [Lanelet2](https://github.com/fzi-forschungszentrum-informatik/Lanelet2)
* [3D_Slam_tools](https://github.com/youliangtan/3D_Slam_tools)

## 1 - Convert bag to pcd
{% highlight shell %}
rosrun pcl_ros bag_to_pcd <file_in.bag> <topic> <output_directory> [<target_frame>]
{% endhighlight %}
## 2 - Convert pcd to bt
{% highlight shell %}
roslaunch 3D_Slam_tools straightener.launch input_path:="PATH/TO/PCD.pcd" output_path:="PATH/TO/OUTPUT.bt"
{% endhighlight %}
Use arrow key to adjust the map distortion if needed, ctrl-c to close and save to .bt file. 

## 3 - Convert bt to pgm map file
{% highlight shell %}
roslaunch 3D_Slam_tools octomap_mapping.launch path:="PATH/TO/OUTPUT.bt" z_min:=-10  z_max:=0.5
{% endhighlight %}
* Setting a large `z_min` allow the map to keep all the ground points which are very useful for creating lanes.
* Adjust `z_max` to remove useless point clouds, like tree and building.

Run map_saver to save the map:
{% highlight shell %}
rosrun map_server map_saver
{% endhighlight %}

Example output:

**map.yaml**
{% highlight yaml %}
image: map.pgm
resolution: 0.050000
origin: [-220.099997, -104.099997, -nan]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.196
{% endhighlight %}

**map.pgm**

<img width="200" height="300" src="{{site.url}}/media/hd_map_with_josm/map.png" alt="map from pcl">
## 4 - Visualization in JOSM

**Add PicLayer plugin in JOSM:**

    mkdir -p ~/.josm/plugins
    open josm
    Edit -> Preferences -> Plugins click Download list, wait for the plugins list, check the PicLayer plugin checkbox, click update plugins
    checkout files in ~/.josm/plugins,  restart if needed

**Convert pgm to png**
{% highlight shell %}
convert map.pgm  -fuzz 20% -transparent white output.png
{% endhighlight %}
It becomes a rgba image with transparent background, very useful to overlay on satellite image.
<img width="200" height="300" src="{{site.url}}/media/hd_map_with_josm/map_rgba.png" alt="map_rbga from pcl">

**Add png in JOSM**

<img width="600" height="300" src="{{site.url}}/media/hd_map_with_josm/josm_ui.png" alt="josm_ui">

    Imagery->New picture layer from file... -> choose the previous output.png
    Imagery->Mapbox Satellite, this is usually a good satellite image

<img width="1000" height="500" src="{{site.url}}/media/hd_map_with_josm/josm_with_map.png" alt="josm_map">

Save calibration, open .cal file, change `INITIAL_SCALE` value to 100* the resolution in map.yaml ( in map.pgm resolution=0.05 so 0.05*100 = 5.0)

{% highlight shell %}
#JOSM PicLayer plugin calibration data
#Wed Apr 28 13:10:02 CST 2021
POSITION_X=1.3358406457062645E7
POSITION_Y=3539617.8704096507
INITIAL_SCALE=5.0  
M00=0.3857612552908848
M11=0.3857612552908848
M10=-0.922598641835333
M02=29.753357212335082
M01=0.922598641835333
M12=79.0004318155288
{% endhighlight %}

Reload the png, the png will be at the right scale, follow step 2 and 3 in the previous picture to align (in step 2, you can put the order and opacity of layers that's why a rgba image comes handy)

Result:
<img width="1000" height="500" src="{{site.url}}/media/hd_map_with_josm/josm_map_overlay.png" alt="josm_map_overlay">

## 0 - Next 

How to draw lane with josm, load it in RVIZ with Lanelets and planning with mission planner.

<img width="1000" height="500" src="{{site.url}}/media/hd_map_with_josm/planning_preview.gif" alt="planning_preview">