# limo

Lidar-Monocular Visual Odometry.
This library is designed to be an open platform for visual odometry algortihm development.
We focus explicitely on the simple integration of the following key methodologies:

* Keyframe selection
* Landmark selection
* Prior estimation
* Depth integration from different sensors.

The core library keyframe_bundle_adjustment is a backend that should faciliate to swap these modules and easily develop those algorithms.

* It is supposed to be an add-on module to do temporal inference of the optimization graph in order to smooth the result
* In order to do that online a windowed approach is used
* Keyframes are instances in time which are used for the bundle adjustment, one keyframe may have several cameras (and therefore images) associated with it
* The selection of Keyframes tries to reduce the amount of redundant information while extending the time span covered by the optimization window to reduce drift
* Methodologies for Keyframe selection:
  * Difference in time
  * Difference in motion

* We use this library for combining Lidar with monocular vision.

## Note

This is work in progress, detailed install instructions and examples will follow.
Here in this repo we use a version with built in prior estimation since it is faster and less modules are required, however results are slightly worse than in kitti version up to now.

## Details
This work was accepted on IROS 2018.
See https://arxiv.org/pdf/1807.07524.pdf .
Until publication in proceedings of IROS, cite arxiv article

@ARTICLE{2018arXiv180707524G,
   author = {{Graeter}, J. and {Wilczynski}, A. and {Lauer}, M.},
    title = "{LIMO: Lidar-Monocular Visual Odometry}",
  journal = {ArXiv e-prints},
archivePrefix = "arXiv",
   eprint = {1807.07524},
 primaryClass = "cs.RO",
 keywords = {Computer Science - Robotics, Electrical Engineering and Systems Science - Image and Video Processing},
     year = 2018,
    month = jul,
   adsurl = {http://adsabs.harvard.edu/abs/2018arXiv180707524G},
  adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}

## Installation

### Requirements

In any case:

* ceres: follow the instructions on [http://ceres-solver.org/installation.html](http://ceres-solver.org/installation.html)
* png++: ```shell sudo apt-get install libpng++-dev```
* install ros https://wiki.ros.org/kinetic/Installation
* install catkin_tools: ```shell sudo apt-get install python-catkin-tools```
* install opencv_apps: ```shell sudo apt-get install ros-kinetic-opencv-apps```

### Build

* initiate a catkin workspace:
    ```shell 
    cd *your_catkin_workspace*
    catkin init
    ```

* clone limo into src of workspace:
    ```shell 
    cd *your_catkin_workspace*/src
    git clone https://github.com/johannes-graeter/limo.git
    ```

* clone dependencies and build repos
    ```shell 
    cd *your_catkin_workspace*/src/limo
    bash install_repos.sh
    ```

* unittests:
    ```shell 
    cd *your_catkin_workspace*
    catkin run_tests --profile limo_release
    ```
    
### Run
* get test data from [https://www.mrt.kit.edu/graeterweb/04.bag](https://www.mrt.kit.edu/graeterweb/04.bag)
    1. this is a bag file generated from Kitti sequence 04 with added semantic labels.
    2. there is more under the same address all named ??.bag (supported: 00.bag, 04.bag)

* in different terminals
    1. `roscore`
    2. `rosbag play 04.bag -r 0.1 --pause --clock`
    3. ```shell
       source *your_catkin_workspace*/devel/setup.sh
       roslaunch demo_keyframe_bundle_adjustment_meta kitti_standalone.launch
       ```
    4. unpause rosbag (hit space in terminal)
    5. `rviz *your_catkin_workspace*/src/demo_keyframe_bundle_adjustment_meta/res/default.rviz`

* watch limo trace the trajectory in rviz :)

### Todo

* runtime is ok for individual modules, however communication between nodes must be enhanced to ensure online usage (nodelets...). 
* add and try rocc landmark selection
* for less packages and better runtime we do not use external priors from liviodo as in Kitti, but internal priors and one motion only-adjustment in between keyframes. However results are slightly worse than on Kitti. Tune so that is not the case.

### Try it out

If you just want to give it a quick peek, I prepared a ready-to-use virtualbox image (packed with Ubuntu 16.04.04, ros kinetic and all dependencies for limo).

* download it from [https://www.mrt.kit.edu/graeterweb/limo_full.ova](https://www.mrt.kit.edu/graeterweb/limo_full.ova).
* Password for the vm-image is "1234".
* Find all modules in ~/workspaces/limo/ .
* Run example (~/04.bag) as described above.
* Note that the runtime in the virtual machine is slower than on a normal system.
