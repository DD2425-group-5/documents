Link to github repositories:
https://github.com/DD2425-group-5

We have one repository for each part:

* `documents` - just various documents, no code here
* `mapping` - stuff to do with mapping
* `controllers` - motor controller and the exploration.
* `navigation` - phase 2, object fetching
* `utilities` - various utilities. 
* `vision` - everything vision-related
* `scripts` - scripts for setup. No code.
* `hardware` - sensors, odometry
* `launch` - launch files
* `localization` - never used
* `ras_arduino_msgs` - just forked repository, we haven't changed anything in it.


# Compiling

Different packages can have dependencies of each other. So most packages should
probably be pulled before compiling. We had all the repositories in the
`~/catkin_ws/src/project directory.`

We tried to fix the `CMakefiles.txt` and `package.xml` files to make compilation
easy. However, for some strange reason, the package mapping_msgs won't compile
with just `catkin_make`. So the package has to be compiled first. So assuming
all repositories are pulled, compile with:

	 catkin_make --pkg mapping_msgs
	 catkin_make


# Running the code

We did at one point have a global launch file, but then we didn't update it and
never bothered to fix it. So we use multiple launch files. To launch everything
(this is phase 1, launches explorer, mapping and vision):

    roslaunch project_launch arduino.launch       //run the python script
	roslaunch ir_sensors ir_sensors.launch 		  //IR sensor launch file
	roslaunch mapping_launch topological.launch   //launches map recording
	roslaunch odometry odometry.launch			  //launch
	roslaunch motor_controller3 pcontrol.launch   //motor controller
	roslaunch vision_master object_detection.launch 	//everything with vision
	roslaunch complex_explorer complex_explorer.launch	//the explorer, once this is run the robot starts moving.


We didn't really use the fetch, but it works and here is the launch command:

	roslaunch fetch fetch.launch

It should be ran instead of the complex_explorer with all of the above commands.


# Details
For extra commands or running certain parts separately.


## Controllers
The basic explorer is just the command

    roslaunch complex_explorer complex_explorer.launch

It requires motor controller, odometry, ir sensors and the python script to be
running. We also have a simple explorer, which we did not use but it works:

    roslaunch simple_explorer simple_explorer.launch

It has the same dependencies as complex_explorer.

For the motor controller, there is no point in using motor_controller 1 or 2. So
the command is just

    roslaunch motor_controller3 pcontrol.launch 

It requires only the python script to be running.

## Mapping
The main mapping launch command is
 
    roslaunch mapping_launch topological.launch

The mapping generates a map in the form of a rosbag. If we already have a map,
we can load the map, and the topological mapping will just add to that map:

    roslaunch mapping_launch topological.launch mapfile:=~/some/location.bag

Instead of the topological map, there is also a launch file for running the
segment map, which didn't look very good. The following records segments from a
run.

    roslaunch mapping_launch segment_storage.launch

Then, to extract lines from these segments, use

    roslaunch mapping_launch segment_stitching.launch segfile:=~/some/location.bag
	
to read from the given bag file. Segment stitching has several parameters that
can be set to affect the behaviour of the stitching. The map representation is
launched using

    roslaunch mapping_launch map_representation.launch

and will listen for the map.


## Vision
To launch everything in vision:

    roslaunch vision_master object_detection.launch

It launches the main components and records a rosbag with the evidence.
It does not require other packages to be ran but having the camera and speaker
connected will stop a bunch of error messages.

Parts can also be ran separately:

    roslaunch color_detection colorDetector.launch 

This launches the color detection node. It only requires the camera to be connected and
that the `openni` node is running.

    roslaunch pcl_methods cube_identifier.launch 

This command runs the cube detection. It depends on the color detection and the camera.

## Hardware
There are two main things to launch: ir_sensors and odometry

    roslaunch ir_sensors ir_sensors.launch 	
    roslaunch odometry odometry.launch

ir_sensors are used to make sense of the sensors' raw data and odometry for
localization. Both require the python script to be running.
