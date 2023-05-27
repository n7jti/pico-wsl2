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
### Setup USBIPD

## In WSL




