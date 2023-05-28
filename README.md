# Raspberry Pi Pico 2040 C++ development using WSL2, a PicoProbe, and VS Code
Here are the Setup instructions for raspberry pi pico development using a pico-probe on WSL2 and VSCode.

## In Windows
First, let's get windows completely setup.  I couldn't find a single place with instructions for everything. But I was able to figure it all out through a combination of sites. 

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

   $ sudo apt install git cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential libstdc++-arm-none-eabi-newlib ninja-build

Next we clone the PICO SDK directory.  The official documentation is quiet about where to put it. But I recommend (based on other walk-throughs on this) to put in in ~/pico.  Once you clone the sub-directory then update the submodules. 


    $ cd ~
    $ mkdir pico
    $ cd pico
    $ git clone https://github.com/raspberrypi/pico-sdk.git
    $ cd pico-sdk
    $ git submodule update -init


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

Install the pico-examples

    $ cd
    $ cd pico
    $ clone -b master https://github.com/raspberrypi/pico-examples.git
    $ cd pico-examples

Start vscode

    $ code .

Follow the instructions in the blog above to continue: https://paulbupejr.com/raspberry-pi-pico-windows-development/

1. Configure CMake Extension

One correction -- put the path to the Pico SDK in "cmake.environment" instead of "cmake.buildEnvironment". 

    "cmake.environment": {
        "PICO_SDK_PATH": "/home/alan/pico/pico-sdk/"
    },

You can change this by clicking "edit json" 

2. Configure Intellisense
3. Configure Compiler and Build
4. If you haven't already, install ninja-build

    $ sudo apt install ninja-build







