# Neato-TX2-ROS

This is a step by step instruction of making the Neato vacuum robot into self-driving platform using ROS.

## Setup of hardware and software

Robot: [Neato Botvac D4](https://www.neatorobotics.com/robot-vacuum/botvac-connected-series/botvac-d4-connected/) 

Onboard CPU(GPU): [Nvidia TX2](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems-dev-kits-modules/) 

Carrier board: Connect Tech [Orbitty Carrier for NVIDIA Jetson TX2/TX1](http://connecttech.com/product/orbitty-carrier-for-nvidia-jetson-tx2-tx1/) 


OS: Ubuntu 16.04 

ROS: Kinetics


Bill of Material

https://docs.google.com/spreadsheets/d/1LVPZXWMjtY5SHHGqkMlHwrtYCdRxLl3Qpumzwb6sIFI/edit?usp=sharing


## Steps

0. [Inspection of the robot](#inspection)
1. [Dis-assemble to remove brashes and a speaker](#disassembly)
2. [TX2 Setup](#tx2setup)
3. [Driving command test](#drivingtest)
4. Install ROS
5. Control robot through ROS node
4. Install ROS

<a name="inspection"></a>
### 0.Inspection of the robot

<a name="disassembly"></a>
### 1.Dis-assemble to remove brashes and a speaker

<a name="tx2setup"></a>
### 2.TX2 Setup

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
usermod -aG dialout newuser

```

Of course, you can ssh into the account from other computer.


Unfortunately, the Kernel Module on NVIDA Jetson TX2 disabled driver USB_ACM, so if even you connect the Neato robot to TX2, you will see nothing new in the /dev directory (It should be /dev/ttyACM0). In order to enable the ACM driver, we have to build the new Kernel Module.

But, don't be afraid. There is a nice tutorial in the internet.
Following video explains how to set the configuration of Kernel and build it.

Build Kernel and ttyACM module - NVIDIA Jetson TX2

https://youtu.be/tDZF7ntLbxc

First you have to clone the [git repo](https://github.com/jetsonhacks/buildJetsonTX2Kernel).

```bash
$ git clone https://github.com/jetsonhacks/buildJetsonTX2Kernel.git
$ cd buildJetsonTX2Kernel
# For L4T 28.1, do the following:
$ git checkout vL4T28.1 
$ ./getKernelSources.sh
```
Some people might be stuck on the last line above be shown that the current version of Kernel on TX is not matching with the one you are trying to download from the Nvidia developer's website. I tried to checkout to "vL4T28.2.1" but caused the error saying that the version is not matched.

After ```./getKernelSources.sh``` following screen will come up.

![img1](/image/kernelconf.png)

This is the Kernel configuration panel. Looks intimidating, right? 

Well, we only have to do two things.

1. Name the local Kernel version.
2. Add ttyACM module in the configuration.

Watch the Youtube video above from [1:45](https://youtu.be/tDZF7ntLbxc?t=105).

And then do follow.

```bash
$ ./makeKernel.sh
$ ./copyImage.sh 
$ reboot
```

After rebooting, try ``` $ uname -r ``` and check the version name of the Kernel is changed to the one you named at the Kernel congurator.

Okay, Now check if ttyAMC module is recognized by TX2 correctly.

```bash
$ ls /dev/tty*
```
And finally, got follow.

![img2](/image/ACM0.png)


<a name="drivingtest"></a>
### 3.Driving command test


## Reference
https://devtalk.nvidia.com/default/topic/1030157/desktop-gui-not-loading-properly/

https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04

https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart

https://www.howtogeek.com/50787/add-a-user-to-a-group-or-second-group-on-linux/

https://devtalk.nvidia.com/default/topic/1001789/jetson-tx2/do-i-need-to-install-ftdi-kernel-module-for-tx2-and-teensy-3-2-arduino-/

https://www.jetsonhacks.com/2017/03/25/build-kernel-and-modules-nvidia-jetson-tx2/
