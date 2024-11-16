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
In the hierarchy of links within the model, the initial link must be defined without inertial properties and is referred to as base_footprint. The URDF format currently does not support defining a closed kinematic loop within this tree structure. Therefore, the mechanical function of the differential bar is only represented in Gazebo through a plugin **(libleo_gazebo_differential_plugin.so)**.

>**NOTE:** When using this URDF model outside of Gazebo simulations, an alternative system must be implemented to replicate the function of the differential bar, ensuring the coordinated rotation of **rocker_joint_L** and **rocker_joint_R**. Without this mechanism, **base_link** is free to rotate when exposed to gravitational forces.

#### /urdf Directory:
- **cc8.xacro:** URDF file written in a macro language called xacro in which all links and joints are defined with their parent-child relationships.
- **cc8.gazebo**: It's included in cc8.xacro, it define the gazebo plugins used and gazebo properties for links. Gazebo plugin used:
  - **gazebo_ros_control**: This plugin allow to define and load all controllers used in the Gazebo simulation. It's important to define the namespace of the robot, this is used during the loading phase of controllers [(Gazebo guide for ros_control)](https://classic.gazebosim.org/tutorials?tut=ros_control).
  - **rocker_differential:** This is a plugin of the Leo Rover model.
- **cc8.trans:** This file define all the transmission characteristic of the joints and their actuator, inside a transmission once you define the hardwareinterface you have to pay attenction to be consistent with the type of controller that you have choose for that joint.

