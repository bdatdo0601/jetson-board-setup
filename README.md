# Nvidia Jetson TX2 setup

In order to fully utilize the process power for computer vision and machine learning on Jetson,
Nvidia provided [**Jetpack**](https://developer.nvidia.com/embedded/jetpack) toolkit,
which include Operating System image and Components (MutimediaAPI, cuDNN, etc).
However, **Jetpack** only allow installation through a host machine running **Ubuntu 16.04**.
Furthermore, starting from Jetpack 4.2, the process is carry through with NVIDIA's Jetson SDK Manager.

This is a guide to setup Nvidia Jetson TX2 through a virtual machine, which in theory should work for both MacOSX, Windows and non-Ubuntu system (although the VM setup might be different). So far the guide has been tested with:

- Mac OSX Mojave 10.15.0

# Pre-requirement

## Host machine

- A Laptop (or Desktop) with Internet connection and at least 2 USB hub
- [Virtual Box 6.0](https://www.virtualbox.org/)
- [Virtual Box Extension Pack for version 6.0](https://download.virtualbox.org/virtualbox/6.0.4/Oracle_VM_VirtualBox_Extension_Pack-6.0.4.vbox-extpack): this is used to allow VM to get access to USB Ports
- [Ubuntu 16.04 Desktop Image](http://releases.ubuntu.com/16.04/)

## Target machine

- Nvidia board
- Micro USB to USB cable
- Montior with HDMI to NVIDIA Board
- Keyboard & mouse

# Virtual Machine setup

1. Setup a new Linux Ubuntu VM using Virtual Box 6.0 with the following requirements:
   - \>2048 MB of RAM
   - \>50 GB of Storage
2. Under setting, configure VM with the following configuration:
   - Display: 64 MB of Video memory, 1 monitor
   - Network: Bridged Adapter with `name` attached through your Internet Connection driver
   - Ports: Under `USB` tab, choose `enable USB Controller` with `USB 3.0 (xHCI) Controller`
3. Once finished, Start VM, load Ubuntu Desktop Image and Install Ubuntu with minimal setting (it is recommended to configure screen resolution to be at least `1280x1080`)
4. Once Ubuntu is installed, go to [Nvidia Jetpack Download Center](https://developer.nvidia.com/embedded/jetpack) and download the `SDK Manager` for your machine

# Installation/Setting Up Jetson Board

The Installation process will be separate into two different part: _Flashing Jetson OS_ and _Installing Components_. **They must be done separately** in order to ensure success.

## Flashing Jetson OS

1. Put the Jetson Board into **force USB recovery** mode

```
1. Power down the device. If connected, remove the AC adapter from the device. The device must be powered OFF, and not in a suspend or sleep state.
2. Connect the Micro-B plug on the USB cable to the Recovery (USB Micro-B) Port on the device and the other end to an available USB port on the host PC.
3. Connect the power adapter to the device.
4. With the system powered on:
• Press and hold the RECOVERY FORCE button.
• While depressing the RECOVERY FORCE button, press and release the RESET button.
• Wait 2 seconds and release the RECOVERY FORCE button.
```

2. Once the board is connect with the PC, on the Virtual machine, go to `Machine --> Settings --> Ports`, under `USB` tab, add `NVIDIA Corp. APX` in to device filter
3. Make sure the VM machine recognize the Jetson Board through USB port
4. Open `SDK Manager`, login with your NVIDIA developer account
5. Going through each step along with the instruction for each step as below
   - Step 1: choose your board, ensure you have check mark on all field
   - Step 2: choose everything under only `host machine` and `flash OS` section (we will install `components` later). Agree to `Terms and condition`, continue and enter your Ubuntu's `sudo` password when prompted
6. Wait for the instllation to complete. Once finished, you should see `Ubuntu Configuration Wizard` on the Jetson machine. Choose `Finish and exit` on the host Ubuntu's `SDK manager`
7. Setup the `Wizard`, giving a `sudo` username and password for your Jetson OS. The OS flashing is now complete

## Installing SDK Components

1. Login to Jetson OS and connect Jetson Board to Host machine with the VM
2. Shutdown the VM, go to its `Setting`:
   - In `Network`, enable another adapter attached to `Bridge Adapter` with name `...Nvidia Tegra`
   - In `Ports`, under `USB` tab, add `NVIDIA Linux for Tegra` to device filter
3. Relaunch the VM, make sure it can see the Jetson Board through USB port (there should be a mounted filesystem)
4. open `SDK Manager`, login with your NVIDIA developer account
5. Going through each step along with the instruction for each step as below
   - Step 1: choose your board, ensure you have check mark on all field
   - Step 2: choose everything under only `host machine` and `SDK components` section. Agree to `Terms and condition`, continue, enter your Ubuntu's `sudo` password when prompted, and enter your Jetson Board `sudo` username and password when prompted.
6. Wait for installation to complete. You should now have all SDK components
   - **Note**: if you run into error while installing components, this might be some dependencies error on Jetson OS. you can fix this by fix broken installs, update and upgrade dependencies on you Jetson OS and rerun `SDK manager` process
   ```bash
   $ sudo apt-get install --fix-broken
   $ sudo apt-get update && sudo apt-get upgrade
   $ sudo apt-get autoremove
   ```

# Testing

In order to verify SDK components, open a new terminal on Jetson OS and try

```
nvcc --version
```

this should return current version of CUDA compiler driver

```
~$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
...
Cuda compilation tools, release 10.0, V10.0.166
~$
```

# Additional Components

These components can be installed directly on Jetson OS

## Installing OpenCV with GStreamer

1. you would need gstreamer library from `linux-for-tegra` under `nv_tegra/nv_sample_app/nvgstapps.tbz2` (a version will be attached with this repo)
2. Follow the instruction here: https://jkjung-avt.github.io/opencv3-on-tx2/
