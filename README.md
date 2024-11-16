# cc8_Simulation
This repository contains the package with the robot description of CC8 Rover (Version: 2022-2023).

## URDF Structure
The robot is described using a URDF file (/urdf/cc8.xacro) written in a macro language called xacro in which all links and joints are defined with their parent-child relationships.
### CC8 Model
![CC8 Model (cc8.xacro)](figures/cc8_visual.png)
### CC8 Link Tree Rviz 
![CC8 Link Tree URDF](figures/cc8_link_tree_rviz.png)
### Link Tree (Structure's links + Camera's links) Description
![CC8 Link Tree URDF](figures/cc8_link_tree.png)
In the hierarchy of links within the model, the initial link must be defined without inertial properties and is referred to as `base_footprint`. The URDF format currently does not support defining a closed kinematic loop within this tree structure. Therefore, the mechanical function of the differential bar is only represented in Gazebo through a plugin **(`libleo_gazebo_differential_plugin.so`)**.

> **NOTE**: When using this URDF model outside of Gazebo simulations, an alternative system must be implemented to replicate the function of the differential bar, ensuring the coordinated rotation of **`rocker_joint_L`** and **`rocker_joint_R`**. Without this mechanism, **`base_link`** is free to rotate when exposed to gravitational forces.

#### `/urdf` Directory:
- **cc8.xacro**: URDF file written in a macro language called Xacro in which all links and joints are defined with their parent-child relationships.
- **cc8.gazebo**: Included in `cc8.xacro`. It defines the Gazebo plugins used and properties for the links. Gazebo plugins used:
  - **gazebo_ros_control**: This plugin allows defining and loading all controllers used in the Gazebo simulation. It is important to define the robot's namespace, as this is required during the controller loading phase [(Gazebo guide for `ros_control`)](https://classic.gazebosim.org/tutorials?tut=ros_control).
  - **rocker_differential**: A plugin specific to the Leo Rover model.
- **cc8.trans**:Included in `cc8.xacro`. This file defines all the transmission characteristics of the joints and their actuators. When defining a transmission, ensure the `hardwareInterface` matches the type of controller chosen for that joint to maintain consistency.
- **zed2.xacro**: Included in `cc8.xacro`. This file contains two macros. **`zed2`** describes the model of the Zed2i stereo camera, defining the links and joints used to position the optical reference frames of the two cameras. **`zed2_gazebo`** allows the simulation of the stereo camera within Gazebo by defining the necessary sensors and plugins along with all associated parameters. This setup enables the availability of key outputs during a Gazebo simulation, such as RGB images from both cameras, depth data, point clouds, internal IMU data, etc. (These data are published on the same topics typically used by the real Zed camera).
`zed2.xacro` was copied from the `leo_description` package of Leo Rover.

#### `/meshes` Directory:
This directory contains all the meshes for the individual links and the stereo camera. These meshes are referenced in `cc8.xacro`, which provides the overall description of the rover. The formats used are `.stl` and `.dae`, with the latter being particularly useful for importing textures of individual components (e.g., wheel colors, etc.). These meshes are used exclusively for visualizing the components and are not used to determine contact between different links, as this could slow down the simulation. If contacts need to be considered, it is recommended to create meshes with simpler geometries.

#### `/launch` Directory:
- #### Gazebo launch (`cc8_gazebo.launch`):
  ![Gazebo Launch Include Tree](figures/launch_gazebo.png)
  To start the simulation (in a paused state) of the rover within Gazebo in an empty world, the `cc8_gazebo.launch` file must be used. This launch file, in turn, includes two other launch files. The first one launched is `empty_world.launch` (not included in this package but found in `gazebo_ros`), which loads the empty world. The second, `spawn_robot.launch`, loads the rover model into the newly created world.

  `spawn_robot.launch` itself includes two launch files: `spawn_model.launch`, which loads the URDF model built from `cc8.xacro`, and `spawn_controllers.launch`, which is responsible for loading the controllers used to operate the four steering joints and the six wheels. Additionally, `spawn_controllers.launch` loads the type of controller and the respective PID constants (kp, ki, kd) into the ROS parameter server by reading the `controllers.yaml` file located in the `/config` directory.

- #### Display launch (`cc8_display.launch`):
