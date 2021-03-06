---
title: "Full Stack Integration"
# excerpt: "Featuring Ouster OS1-64"
# header:
#   overlay_image: /assets/img/lidar-2/os1.jpeg
#   overlay_filter: 0.1 # same as adding an opacity of 0.5 to a black background
#   # actions:
#   #   - label: "More Info"
#   #     url: "https://ouster.com/products/os1-lidar-sensor/"
author: "Steven Lee"
categories: integration
tags: [lidar, slam, integration, simulation]
image: ## lidar/rviz-os1.gif
published: true
toc: true
toc_label: "My Table of Contents"
toc_icon: "cog"
mathjax: true
## classes: wide
---

## Integration Testing

Over the last few weeks, both the perception/spatial and control sub-team has
been busy at work, trying to integrate all the different components which we
have been working on throughout 2020.

This includes:
* Perception Components
  * LiDAR Pipeline
  * Stereo Camera Pipeline
* Localisation and Mapping
  * GPS/IMU Pipeline
  * SLAM
* Control Components
  * Path Planner
  * Path Follower


## ROS Distributed Setup
One of the challenges faced during our testing was the lack of computational power to run all the components together in real-time. To elaborate, the term **real-time** here refers to simulation time matching up with real life time. Under an overloaded condition, the **real-time factor** can drop from 1 to 0.6 or lower. Meaning that the simulation is progressing at 60% of the real life time.

One idea suggested by one of our supervisors was to look into a distributed setup, where we run the Gazebo simulation on a **Host** machine, while the full-stack software which processes sensor outputs is executed on another **Client** machine.

<figure>
  <img src="/assets/img/full-stack/host_client.png" alt="this is a placeholder image">
  <figcaption>ROS Distributed Setup</figcaption>
</figure>

Andrew from Perception team [investigated and documented this approach](https://github.com/MURDriverless/mursim_init/tree/master/remote_ros) which significantly improves the simulation performance. This allowed us to better validate our algorithms, and exclude the possibilities that our designs are underperforming due to computational bottleneck.

## RViz Visualisation

{% include video id="MLCrQq13dY8" provider="youtube" %}

Here, we provide some simulation results from RViz, which is a powerful visualisation tool that works well with ROS. The car starts by driving slowly through an exploratory lap where it maps out this **unknown race track**. After that, it completes 3 at a higher speed of 8-10 m/s.

* **Cylindrical markers** represent the traffic cone detected by LiDAR sensor. These are coloured to reflect the type of traffic cone detected.
* **Spherical markers** represent the map which SLAM has constructed, which build up over time.
* **Green path** in front of the car is generated by the path planner, which geometrically determines the best path forward given the cone positions provided by SLAM.
* **Yellow arrow** in front of the car illustrates the point which the pure-pursuit controller is attempting to follow.

Older footage of the car been simulated in the same track is shown below. Note that in this one video, the car travels rather slowly and there are some glitches which have since been fixed.

{% include video id="aRC7E3-BPtg" provider="youtube" %}

## Simulation Limitations

However, there are still some limitations which required workarounds to be implemented. Firstly, while LiDAR point cloud can be simulated in Gazebo simulator, the returning **laser beam intensity is not simulated**. This meant that we could not realistically simulate the cone classification part of the LiDAR pipeline which requires LiDAR intensity to operate. The workaround solution was to extract ground with information from the simulator itself and feed that into the algorithm.

Secondly, the stereo camera pipeline does not work as well as it does in real life. This is somewhat as expected as there is a pretty significant domain shift between the simulation input data and real-life input data.

At the moment, most of the full integration testing is conducted with LiDAR only.

## Next Steps

The next milestone, which is centred more around the control and optimisation sub-team is the fast lap path-planner and path-follower. So that the car can leverage the complete map information and attempt to complete the remaining laps as fast as possible.
