# INTRODUCTION

The Retro Blaster Mini is a ROM dumping device producted by RetroStage. More information can be found [here](https://retrostage.net/?product=retroblaster-mini-programmer-dumper).

The RetroBlaster software is developed specifically for Microsoft Windows platforms (specifically, Microsoft .NET) and does not provide a native Linux version. It is possible to run (and use) the RetroBlaster software on Linux-based operating systems using [WINE](https://www.winehq.org/).

An important note is that the RetroBlaster software *DOES NOT* work under 64-bit (WOW64) WINE and _must_ be used in a 32-bit WINE prefix. Details on setting this up are below.

The instructions have been tested on the following operating systems:
* Debian 13 (Trixie)
* ElementaryOS 8.1

It is expected that the same instructions will work on other operating systems but some adaptations (such as how to install the WINE package) may be needed.

The general workflow is the following:
1. Install WINE from the package repository
2. Set up the WINE environment, including dotNET support
3. Map the USB interface of the RetroBlaster
4. Dumping ROMs

# INSTALLING WINE

On some systems (such as Ubuntu 26.04), the WINE environment now defaults to the 64-bit (WOW64) version to the exclusion of 32-bit support. To work around this, force the installation of 32-bit libraries.

Add support for installing 32-bit applications. This is *IMPORTANT* and will be needed later to ensure proper WINE support is available.

`$ sudo dpkg --add-architecture i386`  

`$ sudo apt update`

![Add 32-bit Support to DPKG](/img/dpkg-add-i386.png)

`$ sudo apt -y install wine wine32 wine64 winetricks`

![Install WINE with apt](/img/apt_install_wine_winetricks-ubuntu.png)

# SETTING UP THE WINE ENVIRONMENT
Once the `wine` and `winetricks` packages are installed, set up a basic WINE environment (called a "WINE prefix area") by issuing the following command:

`$ WINEARCH=win32 wine winecfg`

![winecfg Main Screen](/img/winecfg-screen.png)

Next, set up the Microsoft .NET 4.8 environment using the `winetricks` tool.

`$ WINEARCH=win32 winetricks dotnet48`

In order to access the serial port, the WINE environment will also need the `winbind` package to be installed. Install it now.

`$ sudo apt install winbind`


# MAPPING THE USB INTERFACE

In order to properly access the serial port, your user will need to be part of the `dialout` group.  You can check your group membership with the following command.

`$ groups`

![winecfg Main Screen](/img/groups-no-dialout.png)

If you do not see a group called `dialout` in the list, add yourself to the `dialout` group with:

`$ sudo usermod -aG dialout $USER`

Once you do this, log out and back in for the change to take effect. There is no need to reboot. A simple logout is sufficient.

The RetroBlaster Mini device exposes a virtual serial port for its communications.

On MS Windows systems, the RetroBlaster software can automatically detect the serial port interface of the RetroBlaster Mini.  However, on Linux systems this port must be specified manually in the RetroBlaster software.

Linux does not use the taxonomy of `COM` plus a number (eg, `COM1` or `COM3`) to represent a serial port.  Instead, Linux refers to serial ports as `TTY` ports (TRIVIA: see [here](https://www.kernel.org/doc/html/latest/driver-api/tty/index.html) for why).

In order to access the serial port, first identify which `COM` port WINE mapped the RetroBlaster Mini `TTY` device to. WINE automatically creates a mapping list in the `.wine` directory under `dosdevices`. You can check it by issuing the following command.

`$ ls -la ~/.wine/dosdevices`

You should see an output similar to the screenshot below.

![DOS devices listing](/img/dosdevices-serial-port.png)

Many devices will be present with names like `ttyS0` or `ttyUSB0`. The RetroBlaster Mini will expose a virtual serial port of the name `ttyACM`.  Most people will only have a single device called `ttyACM0`.  While rare, some other USB virtual serial ports (like on AMD-Xilinx FPGA boards) may also expose a `ttyACM` device. If there is uncertainty about the appropriate port number, unplug the RetroBlaster Mini and check the device list and then plug it in again and compare. Whichever one disappears and reappears during the test is the RetroBlaster Mini.

With this in mind, now you can launch the RetroBlaster software with the following command:

`WINEARCH=win32 wine <path_to_retroblaster_exe>`

It is imperative to include the WINEARCH=win32 because this tells wine that you are running a 32-bit WINE prefix environment rather than 64-bit (which won't work).

Once the RetroBlaster software open, specify the COM port you saw in the mapping by using *File => Force COM*.

![DOS devices listing](/img/retroblaster-set-com-port.png)

# DUMPING A ROM

TODO:
