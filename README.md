# Raspberry Pi Pico 2040 C++ development using WSL2, a PicoProbe, and VS Code
Here are the Setup instructions for raspberry pi pico development using a pico-probe on WSL2 and VSCode.

## In Windows
First, let's get windows completely setup.  I couldn't find a single place with instructions for everything. But I was able to figure it all out through a compbination of sites. 

Here are the basic steps:
1. Setup WSL2 itself
2. Get WSL2 to setup Ubuntu 22.04 LTS distro
3. Install usbipd so that we can communicate with the pico-probe

### Setup WSL
https://learn.microsoft.com/en-us/windows/wsl/install 

The short version is that you run an (elevated?) powershell command line.  Using the Windows Terminal is a great way to do that.  You can easily switch back and forth between WSL and PowerShell as you go throug the instructions 

    # list avaiable distro's
    wsl --list --online
    
    # install Ubuntu 22.04 LTS
    wsl --install -d Ubuntu-22.04

### Setup Ubuntu 22.04

First things first, get all the security patches and updates

    # Repeate until nothing new installs
    $ sudo apt update
    $ sudo apt upgrade

You are looking for output like this to tell you that you are finshed

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


# Setup the Raspberry PI SDK
Next, we start follwoing the offical Raspberry PI quickstart documentation (mostly).  The goal here is to get a blinky LED on your PI using the standard command line tools.  Here's the offical documentation: https://www.raspberrypi.com/documentation/microcontrollers/c_sdk.html#quick-start-your-own-project

   sudo apt install git cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential libstdc++-arm-none-eabi-newlib

Next we clone the PICO SDK directory.  The official documentation is quiet about where to put it. But I recommend (based on other walk-throughs on this) to put in in ~/pico.  Once you clone the sub-directory then update the submodules. 


    $ cd ~
    $ mkdir pico
    $ cd pico
    $ git clone https://github.com/raspberrypi/pico-sdk.git
    $ cd pico-sdk
    $ git submodule update -init

To use the SDK in another project you need to do two things
1. Set the PICO_SDK_PATH to that directory (for me $HOME/pico/pico-sdk)
2. Copy pico_sdk_import.cmake from $HOME/pico/pico-sdk/external to a directory of your new project. 
3. Include pico_sdk_import.cmake in your CMakeFiles.txt and call pico_sdk_init() like so

    cmake_minimum_required(VERSION 3.13)

    \# initialize the SDK based on PICO_SDK_PATH
    \# note: this must happen before project()
    include(pico_sdk_import.cmake)

    project(my_project)

    \# initialize the Raspberry Pi Pico SDK
    pico_sdk_init()

    \# rest of your project

### Setup USBIPD

## In WSL




