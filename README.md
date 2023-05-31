# Raspberry Pi Pico 2040 C++ development using WSL2, a PicoProbe, and VS Code
Here are the Setup instructions for raspberry pi pico development using a pico-probe on WSL2 and VSCode.  This repo contains a working and properly configured VScode example that flashes an LED and Writes to the standard UART.  

## In Windows
First, let's get windows completely setup so that we can use WSL2 (Linux) for our ARM development on the PICO.  I couldn't find a single place with instructions for everything. But I was able to figure it all out through a combination of sites. 

Here are the basic steps:
1. Setup WSL2 itself
2. Get WSL2 to setup Ubuntu 22.04 LTS distribution
3. Install usbipd so that we can communicate with the pico-probe

### Setup WSL
https://learn.microsoft.com/en-us/windows/wsl/install 

The short version is that you run an (elevated?) powershell command line.  Using the Windows Terminal is a great way to do that.  You can easily switch back and forth between WSL and PowerShell as you go through the instructions 

    # list available distributions
    wsl --list --online
    
    # install Ubuntu 22.04 LTS
    wsl --install -d Ubuntu-22.04

### Setup Ubuntu 22.04

First things first, get all the security patches and updates

    # Repeat until nothing new installs
    $ sudo apt update
    $ sudo apt upgrade

You are looking for output like this to tell you that you are finished

    alan@AlanLu-Surface:~$ sudo apt update
    Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease
    Hit:2 http://archive.ubuntu.com/ubuntu jammy InRelease
    Hit:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
    Hit:4 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    All packages are up to date.
    alan@AlanLu-Surface:~$ sudo apt upgrade
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    Calculating upgrade... Done
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    alan@AlanLu-Surface:~$


## Install the Raspberry Pi Pico SDK into WSL2 
Next, we start following the official Raspberry PI Pico quick start documentation (mostly).  The goal here is to get a blinking LED on your PI using the standard command line tools.  Here's the official documentation: https://www.raspberrypi.com/documentation/microcontrollers/c_sdk.html#quick-start-your-own-project

Start by opening the Ubuntu command line from Windows Terminal.  

    $ sudo apt install git cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential libstdc++-arm-none-eabi-newlib ninja-build gdb-multiarch

Next we clone the PICO SDK directory.  The official documentation is quiet about where to put it. But I recommend (based on other walk-throughs on this) to put in in ~/pico.  Once you clone the sub-directory then update the submodules. 


    $ cd ~
    $ mkdir pico
    $ cd pico
    $ git clone https://github.com/raspberrypi/pico-sdk.git
    $ cd pico-sdk
    $ git submodule update --recursive --init


## Using the Raspberry Pi Pico SDK from the command line

To use the SDK in a project you need to do a few  things

Set the PICO_SDK_PATH to your Pico SDK directory (for me $HOME/pico/pico-sdk)

    $ export PICO_SDK_PATH=$HOME/pico/pico-sdk


Copy pico_sdk_import.cmake from $HOME/pico/pico-sdk/external to the root directory of your new project, and include pico_sdk_import.cmake in your CMakeFiles.txt and call pico_sdk_init() like so:

    #CMakeList.txt
    cmake_minimum_required(VERSION 3.13)

    # initialize the SDK based on PICO_SDK_PATH
    # note: this must happen before project()
    include(pico_sdk_import.cmake)

    project(my_project)

    # initialize the Raspberry Pi Pico SDK
    pico_sdk_init()

    # rest of your project

Follow the pico quickstart guide and confirm that your pico, and SDK are working properly: https://www.raspberrypi.com/documentation/microcontrollers/c_sdk.html#quick-start-your-own-project

## Using CMake and the Raspberry Pi Pico SDK from VS Code

Once you can build a Pico .uf2 file from the command line, you can start working on getting it working in VS Code.  Here is the source of my info on getting Pi Pico C++ development working from WSL on Windows. 

https://paulbupejr.com/raspberry-pi-pico-windows-development/

Install VS Code: https://code.visualstudio.com/download

Install the following extensions

1. Remote -- WSL https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl
2. C/C++ -- https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools
3. CMake Tools -- https://marketplace.visualstudio.com/items?itemName=ms-vscode.cmake-tools
4. Coretx Debug -- https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug

Install the pico-examples

    $ cd
    $ cd pico
    $ clone -b master https://github.com/raspberrypi/pico-examples.git
    $ cd pico-examples

Start vscode

    $ code .

Follow the instructions in the blog above to continue: https://paulbupejr.com/raspberry-pi-pico-windows-development/

1. Configure CMake Extension

The CMake extension configuration is in the .vscode/settings.json file.  One change from the blog is that the cmake.environment is set, rather than the cmake.buildEnvironment. This resolved an error seen during CMake configuration.  

2. Configure Intellisense

The intellisense configuration is in the .vscode/c_cpp_properties.json. 

3. Configure debugging

The debugger configuration (which uses OpenOCD) is in .vscode/launch.json.  To make full use of this you need to build the pico version of OpenOCD as well as the Picoprobe software.  OpenOCD gets installed into WSL, and the Picoprobe software gets installed in a 2nd Pico which is used as a debug dongle. 


## Hardware debugging using Picoprobe, OpenOCD and VSCode

Follow the instructions in the "Getting Started with Raspberry Pi Pico, C/C++ development with Raspberry Pi Pico and other RP2040-based microcontroller boards":  https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf

The instructions for Pico Setup for command line use starts in Chapter 1.  But if you started at the top, that is already done. What you are looking for here is setting up the Picoprobe on a 2nd Pico to use as a debug dongle with OpenOCD.  You'll find those instructions in Appendix A. 

Note: Because we are using WSL, we will follow the linux directions. 

1. Get a 2nd Pico.
2. Wire it up as per the drawing.  Use good quality breadboards and jumpers.  If you have bad or intermittent connections you will get errors when OpenOCD is launched. 
3. Build OpenOCD from the Raspberry Pi Fork on Github
4. Build and Flash picoprobe
5. If you wire-up the UART you can also use the UART in Linux (WSL2) as well. 

## Configuring USB for WSL2

To use USB with WSL2 you'll follow the instructions here: https://learn.microsoft.com/en-us/windows/wsl/connect-usb

1. Install the USBIPD-WIN project.  Latest version is 3.0 and you can find it here: https://github.com/dorssel/usbipd-win/releases
2. Installation instructions are in the README file for USBIPD-WIN found here: https://github.com/dorssel/usbipd-win
4. Once installed, find the busid of the PicoProbe

```
    PS C:\Users\alanlu> usbipd wsl list

    BUSID  VID:PID    DEVICE                                                        STATE
    2-10   8087:0033  Intel(R) Wireless Bluetooth(R)                                Not attached
    4-2    045e:07c6  Surface Ethernet Adapter                                      Not attached
    9-3    045e:0904  USB Input Device                                              Not attached
    10-1   045e:07a5  USB Input Device                                              Not attached
    10-2   046d:0843  Logitech Webcam C930e                                         Not attached
    11-5   04d8:0b2a  USB Input Device                                              Not attached
    12-3   046d:0a8f  Logi USB Headset, USB Input Device                            Not attached
    12-4   2e8a:000c  CMSIS-DAP v2 Interface, USB Serial Device (COM4)              Not attached
    12-5   04d8:0b2a  USB Input Device                                              Not attached

    PS C:\Users\alanlu>
```

Then you can share it with WSL2 (Linux).  You will need to run this from an elevated command window

```
    PS C:\Users\alanlu> usbipd wsl attach --busid 12-4
    PS C:\Users\alanlu> usbipd wsl list
    BUSID  VID:PID    DEVICE                                                        STATE
    2-10   8087:0033  Intel(R) Wireless Bluetooth(R)                                Not attached
    4-2    045e:07c6  Surface Ethernet Adapter                                      Not attached
    9-3    045e:0904  USB Input Device                                              Not attached
    10-1   045e:07a5  USB Input Device                                              Not attached
    10-2   046d:0843  Logitech Webcam C930e                                         Not attached
    11-5   04d8:0b2a  USB Input Device                                              Not attached
    12-3   046d:0a8f  Logi USB Headset, USB Input Device                            Not attached
    12-4   2e8a:000c  CMSIS-DAP v2 Interface, USB Serial Device (COM4)              Attached - Ubuntu-22.04
    12-5   04d8:0b2a  USB Input Device                                              Not attached

    PS C:\Users\alanlu>
```

If you get an error telling you to install software in Linux, follow the instructions in the error message

Once it is attached to WSL2, you should find the pico-probe in linux using lsusb

```
    alan@AlanLu-Surface:~$ lsusb
    Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    Bus 001 Device 009: ID 2e8a:000c Raspberry Pi Picoprobe CMSIS-DAP
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    alan@AlanLu-Surface:~$
```









