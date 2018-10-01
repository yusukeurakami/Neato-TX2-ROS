# Neato-ROS

This is a step by step instruction of making the Neato vacuum robot into self-driving platform using ROS.

## Setup of hardware and software

Robot: [Neato Botvac D4](https://www.neatorobotics.com/robot-vacuum/botvac-connected-series/botvac-d4-connected/) 

Onboard CPU(GPU): [Nvidia TX2](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems-dev-kits-modules/) 

Carrier board: Connect Tech [Orbitty Carrier for NVIDIA Jetson TX2/TX1](http://connecttech.com/product/orbitty-carrier-for-nvidia-jetson-tx2-tx1/) 


OS: Ubuntu 16.04 

ROS: Kinetics


## Steps

0. [Inspection of the robot](#inspection)
1. [Dis-assemble to remove brashes and a speaker](#disassembly)
2. [Driving command test](#drivingtest)
3. [TX2 Setup](#tx2setup)
4. Install ROS
5. Control robot through ROS node
4. Install ROS

<a name="inspection"></a>
### 0.Inspection of the robot

<a name="disassembly"></a>
### 1.Dis-assemble to remove brashes and a speaker

<a name="drivingtest"></a>
### 2.Driving command test

<a name="tx2setup"></a>
### 3.TX2 Setup

First, boot the TX2 on the development board. It will automatically take you to the desktop of user "nvidia".

In order to make new user for controlling the robot, open the terminal and use 'adduser' commmand.

```bash
sudo adduser newuser
```
It will ask for new user's password. You can ignore the rest of the info (e.g. Fullname, Room number etc.) and press enter.

```bash
Changing the user information for username
Enter the new value, or press ENTER for the default
    Full Name []:
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
Is the information correct? [Y/n]
```
Bring the new user into 'sudo' and 'video' group. 

*Caution!* Without adding to 'video' group, new user's desktop will be like [this](https://youtu.be/_vEGhCDQ_rE).

```bash
usermod -aG sudo newuser
usermod -aG video newuser
```

Of course, you can ssh into the account from other computer.


## Reference
https://devtalk.nvidia.com/default/topic/1030157/desktop-gui-not-loading-properly/

https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04

https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart

https://www.howtogeek.com/50787/add-a-user-to-a-group-or-second-group-on-linux/
