# Neato-TX2-ROS

This is a step by step instruction of making the Neato vacuum robot into self-driving platform using ROS.

## Setup of hardware and software

Robot: [Neato Botvac D4](https://www.neatorobotics.com/robot-vacuum/botvac-connected-series/botvac-d4-connected/) 

Onboard CPU(GPU): [Nvidia TX2](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems-dev-kits-modules/) 

Carrier board: Connect Tech [Orbitty Carrier for NVIDIA Jetson TX2/TX1](http://connecttech.com/product/orbitty-carrier-for-nvidia-jetson-tx2-tx1/) 


OS: Ubuntu 16.04 

ROS: Kinetics

[Bill of Material](https://docs.google.com/spreadsheets/d/1LVPZXWMjtY5SHHGqkMlHwrtYCdRxLl3Qpumzwb6sIFI/edit?usp=sharing)


## Steps

0. [Inspection of the robot](#inspection)
1. [Dis-assemble to remove brashes and a speaker](#disassembly)
2. [TX2 Hardware Setup](#tx2hardsetup)
3. [TX2 software Setup](#tx2softsetup)
4. [Driving command test](#drivingtest)
5. [Install ROS](#installROS)
6. [Control robot through ROS node](#controlROS)

<a name="inspection"></a>
### 0.Inspection of the robot

<a name="disassembly"></a>
### 1.Dis-assemble to remove brashes and a speaker

<a name="tx2hardsetup"></a>
### 2.TX2 hardware Setup

Take off the TX2 from the Devlopment board using the torque screw driver and snap plugin to the Orbitty board.
![img3](/image/tx2Orbitty.jpg)

Orbitty board need 9V-14V power supply. For the first setup, I used the [12V/2A outlet plug which has the terminal from the beginning](http://a.co/d/6whZ0oo).
![img4](/image/OutletPowerSupply.jpg)

In order to install the TX2 on the mobile robot, we need to supply the power by the battery with power regulator.
I will write about that later.

<a name="tx2softsetup"></a>
### 3.TX2 software Setup

In order to use the Orbitty board instead of the devlopment board that come with TX2, I had to flash the jetson on TX2. For this step, you need not only the TX2 but the host Linux(Ubuntu) computer that has 10GB+ extra capacity to prepare for the software to flash on TX2.

Basic steps are following.

#### 1. Install the latest Jetson using the Nvidia JetPack from [this webpage](https://developer.nvidia.com/embedded/jetpack). 
Right now (10/14/2018), JetPack 4.1 Early Access is avaliable but this is only for Xavier Dev Kit, so not apply to TX2. I installed JetPack 3.3.

After you moved the Jetpack, locate the file in the directory you want, and run as follows.

```bash
chmod +x JetPack-L4T-XX-linux-x64.run
./JetPack-L4T-XX-linux-x64.run
```
And you can see this.
![img5](/image/jetson_install1.png)

Check the location you want to install and go next. And you will see the jetPack Components Manager as follow.

    - Select "Host - Ubuntu" and set the Action column to "no action".
    - Select "Install on Target" and set it to "no action".
    - Select "Flash OS Image to Target" and set it to "no action"

Press next and wait until the jetson has been installed to the host PC.
![img6](/image/jetpackCompManager.png)

##### *If you see nothing in the JetPack Components Manager because of the proxy like picture below, [check this link](https://devtalk.nvidia.com/default/topic/1016678/jetson-tx2/jetpack-3-0-jetsontx2-corporate-proxy/)

![img7](/image/JetPackInstallerEmpty.png)

#### 2. Install the Connect Tech support package Setup from [this webpage](http://connecttech.com/product/orbitty-carrier-for-nvidia-jetson-tx2-tx1/). 
I used L4T r28.2X.

Locate the CTI-L4T-V###.tgz package into ``` <JetPack_install_dir>/64_TX2/Linux_for_Tegra_tx2/``` and Extract the BSP and run the installer.

```
tar -xzf CTI-L4T-V###.tgz
cd ./CTI-L4T
sudo ./install.sh
```

#### 3. Flash the Jetson into TX2

Firstly, connect the Orbitty board with host PC with the micro USB cable. And then, boot the Jetson into recovery mode by holding down the RESET and RECOVERY button and then press the POWER bottun on the Orbitty board. Release the buttons in order of POWER, RESET, RECOVERY button.

Try ```$lssub``` on the host PC and check if you can find the ```Bus 001 Device xxx: ID xxxx:xxxx NVidia Corp.```.

Open a terminal from the <JetPack_install_dir>/64_TX2/Linux_for_Tegra_tx2/ directory:

```$sudo ./flash.sh orbitty mmcblk0p1```

Just wait until it is done and if you got the screen like below, reboot it!

![img8](/image/complete.png)


[Reference1: Jetson™ Flashing and Setup Guide for a Connect Tech Carrier Board](https://github.com/NVIDIA-Jetson/jetson-trashformers/wiki/Jetson%E2%84%A2-Flashing-and-Setup-Guide-for-a-Connect-Tech-Carrier-Board)

[Reference2: Flashing NVIDIA Jetson TX2 or TX1 Module](https://youtu.be/9uMvXqhjxaQ)


#### 4. Make new user account for the robot (Not necessary)

When you reboot, it will automatically take you to the desktop of user "nvidia".

There is two default users for TX2, "Nvidia" and "Ubuntu". You can either change the name of either of the user's name or create a new user for a robot.

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
groupmod -n new_username old_username
usermod -aG sudo newuser
usermod -aG video newuser
usermod -aG dialout newuser

```

Of course, you can ssh into the account from other computer.

<a name="drivingtest"></a>
### 4.Driving command test

Let's control the Neato robot manually through the USB port.First, we need the "screen" pkg.


```bash
$ sudo apt update
$ sudo apt screen
```

After that we will connect to robot through "screen" command as follow.

```bash
screen /dev/ttyACM0
```
[If your TX2 doesn't recognize the ttyACM0, follow this instruction.](no_ttyACM0.md)


When you got the blank screen, try type ```help```.

if you see the commands for Neato robot, you are successfully connected. Try follows.
* The robot will move forward for 50mm (2'') make sure that your wiring has margin.


```bash 
$ setmotor speed 50 lwheeldist 50 rwheeldist 50
```


Does it works? Congratulation! Now you can control this robot through TX2.

You can play around with other command that you can see in "help".

You can find the command API manual [here](https://www.neatorobotics.com/lab/linux/).


<a name="installROS"></a>
### 5.Install ROS

I think most of the people trace [this webpage](http://wiki.ros.org/kinetic/Installation/Ubuntu) to install ROS-Kinetic but this did not work for me.

Instead, I used following git repo by [Jetsonhacks](https://www.jetsonhacks.com/)

[Jetsonhacks: installROSTX2](https://github.com/jetsonhacks/installROSTX2)

```bash
$ git clone https://github.com/jetsonhacks/installROSTX2
$ cd installROSTX2
$ ./installROS.sh -p ros-kinetic-desktop-full
```


## Reference
https://devtalk.nvidia.com/default/topic/1030157/desktop-gui-not-loading-properly/

https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04

https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart

https://www.howtogeek.com/50787/add-a-user-to-a-group-or-second-group-on-linux/

https://devtalk.nvidia.com/default/topic/1001789/jetson-tx2/do-i-need-to-install-ftdi-kernel-module-for-tx2-and-teensy-3-2-arduino-/

https://www.jetsonhacks.com/2017/03/25/build-kernel-and-modules-nvidia-jetson-tx2/
