# In the case if TX2 doesn't recognize the robot (/dev/ttyACM0)

Unfortunately, sometimes the Kernel Module on NVIDA Jetson TX2 disabled driver USB_ACM, so if even you connect the Neato robot to TX2, you will see nothing new in the /dev directory (It should be /dev/ttyACM0). In order to enable the ACM driver, we have to build the new Kernel Module.

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
