# Ubuntu2204_ROS2_SSD
Install Ubuntu 22.04 ROS2 Humble on an External SSD and Dual Boot with Windows 11

# Install Ubuntu 22.04 on an External SSD and Dual Boot with Windows 11

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Step 1: Installation of Ubuntu 22.04

**1. Pre-requisites**:
- A Windows 11 or higher computer
- At least 25 GB of free disk space for Ubuntu 22.04
- A USB drive with at least 8GB
- Internet connection
- A freshly formatted External SSD drive. I bought a Samsung 980 Pro M2 SSD with 500 GB storage. Ensure you have a high read/write speed because your operating system performance can be bottlenecked by it (minimum 1050MB/s).
- Make sure you back up your computer (especially personal files, but also your OS) in case something bad happens, which can be the case since we are going to do a lot of formatting, partitioning, and changing BIOS/UEFL settings.

**2. Disable Secure Boot**:

For some computers, `Secure Boot` will still prevent you from installing or booting new operating systems. 
To disable this:
    - Turn your computer OFF. Then, turn it back ON and press the BIOS entry key during the boot process. This varies between hardware types, but is generally F1, F2, F12 …
    - Find the **Secure Boot** option. If possible, set it to **Disabled**. It is usually found in the Security tab, Boot tab, or Authentication tab.
    - **Save and Exit**. Your system will reboot.

Reference:
    [How to Disable UEFI Secure Boot to Dual Boot Any System](https://www.makeuseof.com/tag/disable-secure-uefi-dual-boot/)

**3. Create a new partition on your Windows computer for Ubuntu**:
- Click ***Win + R*** and type ***cmd*** to open the terminal
- Type `diskmgmt.msc` and enter to open the Disk management
- Right click the disk and format it

**4. Download Ubuntu 22.04 Image**:
Download ***Ubuntu 22.04*** from the official website, select ***64-bit PC (AMD64) desktop image***

[Ubuntu 22.04.6 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/)

**5. Download Rufus Software**:
***Rufus*** software is to make your USB drive bootable, select the ***Standard*** type with your computer platform.

[Rufus - Create bootable USB drives the easy way](https://rufus.ie/en/)

For most of you, choose the standard one with the platform Windows x64.

**6. Format the USB drive and make it bootable**:
- Insert your USB into the USB port
- In the File Explorer, right-click your USB and select ***Format*** option, and follow the instructions
- Right click the ***Rufus*** and run as an administrator
- Leave everything default, click ***SELECT*** option and select the ISO image file just downloaded (ubuntu-22.04.6-desktop-amd64.iso).
- Click ***START*** and follow the steps

**7. Install Ubuntu 22.04 Dual Boot**:
- Restart the computer. Press F11 (the button might be different for different computer brands, search for how to enter the ***Boot Menu*** for yours)
- Choose the USB drive as the boot device.
- Restart the computer it will automatically boot from the USB stick with the Ubuntu installation media.

**8. Once you have booted into Ubuntu, choose ‘Try or Install Ubuntu’ from Grub MENU**:

Now, you can connect your external drive to your PC.

**9. After a while, a loader with options of “Try Ubuntu” and “Install Ubuntu” will come up**:
You can choose either one. If you choose Try Ubuntu, Ubuntu will load from your USB for the time being, and on the desktop there is a shortcut to “Install Ubuntu”. I personally prefer this method because the fonts appear much bigger, and you can do other things with the temporary Ubuntu while the installation is ongoing.

**10. Follow the instructions to go ahead.**:
 
- In ***Update and other software*** tab, it is recommended to select ***Normal installation*** and tick both of the two ***Other options***
- In the screenshot below — my external and portable drive is identified as /dev/sdb2 — and it is currently unpartitioned. Unmount (right click and unmount) any currently mounted partitions on this drive and delete all partitions (again — be double sure you’re working on the correct drive).

**11. Now, you can click on the Install Ubuntu icon and proceed towards installing until you encounter the ‘Installation Type’ window**:

- Click on ‘Something else’ and proceed

  Now that we’re on the ‘Something else’ installation type screen — scroll down the list of available drive volumes until you see your device and the partitions we previously created. In this example /dev/sdb1, /dev/sdb2, and /dev/sdb3.

- Lastly, still in the installation type tab, change the device for boot loader installation to the same device as your root partition. After all, click ***Install Now*** and follow the instructions on your screen

After restarting your computer, the Ubuntu dual boot menu will be shown.

Reference:

https://medium.com/@abrarahmedsyed/how-to-create-a-truly-portable-plug-n-play-ubuntu-installation-on-an-external-ssd-hdd-7aaadc7d796a

## Step 2: Installation of ROS2 Humble on Ubuntu 22.04 on an External SSD and Dual Boot with Windows 11

**1. Set locale**

```bash
locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
```
**2. Setup Sources**

To add the ROS 2 apt repository, ensure the Ubuntu Universe repository is enabled.

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
```

Add the ROS 2 GPG key with apt.

```bash
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
```

Then add the repository to your sources list.

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

**3. Install ROS 2 packages**

After setting up the repositories, update your apt repository caches. This ensures your system is up to date before installing ROS 2 packages.

```bash
sudo apt update
sudo apt upgrade
```

**4. Install ROS 2 packages**

Generally, the “ros-humble-desktop” is installed on the Ground Control and Simulation System (our Desktop/Laptop), and the “ros-humble-ros-base” is installed on the Robot Computer(Raspberry Pi).

Here, we will install Desktop ROS Humble on Ubuntu 22.04:

```bash
sudo apt install ros-humble-desktop
```

**5. Install ROS2 Development tools**

Install the development tools: Compilers and other tools to build ROS packages.

```bash
sudo apt install ros-dev-tools
```

**6. Environment setup**

To start working on ROS 2, first, you need to source the setup script in each terminal session.

```bash
source /opt/ros/humble/setup.bash
```

**7. Test with running talker-listener example**

In one terminal, source the setup file and then run a Python talker:

```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_py talker
```

In another terminal source the setup file and then run a Python listener:

```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_py listener
```

Stop both scripts using Ctrl+C.

**8. Automate the environment setup process**

To automate the environment setup process and avoid sourcing the setup file manually each time, we can add the command to source the setup file in the “.bashrc” file. This way, the command will be executed automatically every time we open a new terminal or SSH session.

Edit the .bashrc file and add the command `source /opt/ros/humble/setup.bash` at the end of the file.:

```bash
nano ~/.bashrc
```

Reference:

https://medium.com/spinor/getting-started-with-ros2-install-and-setup-ros2-humble-on-ubuntu-22-04-lts-ad718d4a3ac2

