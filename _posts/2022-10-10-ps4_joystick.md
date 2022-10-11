---
layout: post
title:  "On Ubuntu, play sound files on PS4 joystick"
date:   2022-10-10 11:12:00 +0800
categories: TECH
---

## Summary
Sometime it can be handy while remote controlling, you can get an update on the current state, like voice indication or by lights.

1. [Connect your joystick to Ubuntu](#1---connect-your-joystick-to-ubuntu)
2. [Prepare your sound file](#2---prepare-your-sound-file)
3. [Play the sound file](#3---play-the-sound-file)
4. [Global planning demo](#4---global-planning-demo)

## Prerequisite
* [ROS1](https://www.ros.org/)
* [ros-joystick_drivers](https://github.com/ros-drivers/joystick_drivers)
* example code: [ps4_joystick_sound_player](https://github.com/zhibopotame/ps4_joystick_sound_player)

## 1 - Connect your joystick to Ubuntu

* Pairing

<img width="500" height="300" src="{{site.url}}/media/ps4_joystick/ds_1.jpg" alt="ds_1">

* In Ubuntu Bluetooth settings, add new device

<img width="500" height="300" src="{{site.url}}/media/ps4_joystick/bluetooth.png" alt="bluetooth">

* Test
{% highlight shell %}
rosrun joy joy_node 
rostopic echo /joy
{% endhighlight %}

## 2 - Prepare your sound file
Convert `mp3` to `sbc` file 
```
gst-launch-1.0 -q filesrc location=INPUT.mp3 ! decodebin ! audioconvert ! audiosample ! sbcenc ! "audio/x-sbc,rate=32000,channels=2,channel-mode=dual,blocks=16subbands=8,allocation-method=loudness,bitpool=25" ! queque ! filesink location=OUTPUT.sbc sync=false
```

## 3 - Play the sound file
* Udev permission

in [ps4_joystick_sound_player](https://github.com/zhibopotame/ps4_joystick_sound_player)
{% highlight shell %}
sudo cp cfg/99-hidraw-permissions.rules /etc/udev/rules.d
{% endhighlight %}
* If your username is not in dialout group, add to it
{% highlight shell %}
sudo adduser USERNAME dialout
{% endhighlight %}

* Demo 
{% highlight shell %}
roslaunch ps4_joystick_sound_player joy_sound_player.launch 
{% endhighlight %}

## 4 - Change joystick's light color
[joy_sound_player.cpp](https://github.com/zhibopotame/ps4_joystick_sound_player/blob/master/src/joy_sound_player.cpp#L159)
{% highlight cpp %}
void JoySoundPlayer::play_sound(std::string SBCFile, std::vector<char> RGB, char volume){...}
{% endhighlight %}
