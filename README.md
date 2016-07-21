# axp209-nolim
C.H.I.P. startup script to configure the AXP209 power controller to not limit supply current from
the MicroUSB.

## License

I want there to be NO barriers to using this code, so I am releasing it to the public domain.  But "public domain" does not have an internationally agreed upon definition, so I use CC0:

Copyright 2016 Steven Ford http://geeky-boy.com and licensed
"public domain" style under
[CC0](http://creativecommons.org/publicdomain/zero/1.0/): 
![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png "CC0")

To the extent possible under law, the contributors to this project have
waived all copyright and related or neighboring rights to this work.
In other words, you can use this code for any purpose without any
restrictions.  This work is published from: United States.  The project home
is https://github.com/fordsfords/axp209-nolim/tree/gh-pages

To contact me, Steve Ford, project owner, you can find my email address
at http://geeky-boy.com.  Can't see it?  Keep looking.

## Introduction

This project is not needed for CHIP users who flash the "no limit" OS.
But for other builds of the OS, this project can still be very useful.

The [CHIP](http://getchip.com/) single-board computer contains an AXP209 power
controller that manages input power from three sources: MicroUSB, CHG-IN, and
battery.

Some users experience sudden shutdowns of their CHIP, especially if they have
installed peripherals (plug-in boards, USB devices, or both).  See
[the troubleshooting guide](http://www.chip-community.org/index.php/Troubleshooting#Abrupt_Shutdowns)
if you have this problem; it usually means you need a better power
supply or cable.

There are a few users who have reported that even with a high-quality power
supply and cable, their CHIP plus peripherals requires more than the
900 mA limit imposed on MicroUSB power by the AXP209.  Those uses should use a
[better power design
approach](http://www.chip-community.org/index.php/Power#Power_Design_3:_MicroUSB_Plus_Battery).

If you fall in this category, and want use your CHIP while waiting for the
improved power design parts, it is possible to install the "axp209-nolim"
startup script to set the MicroUSB supply input to "no limit".  This
should be considered a stop-gap measure while waiting for an improved
power design.

But understand that this startup script introduces some risk.  Some laptops's
USB interfaces cannot provide more than 900 mA; if you connect your chip plus
peripherals to your laptop, it might overload the USB port and could damage
your laptop.

Next Thing Co, the maker of CHIP, has recommended ***AGAINST*** setting the AXP to
unlimited current.

Use this script at your own risk.  If you damage your laptop because this script 
allowed an overload condition, it's your fault, not mine.

You can find axp209-nolim on github.  See:

* User documentation (this README): https://github.com/fordsfords/axp209-nolim/tree/gh-pages

Note: the "gh-pages" branch is considered to be the current stable release.  The "master" branch is the development cutting edge.

## Quick Start

If you are having power problems, you should first log into CHIP with all
peripherals disconnected.  These instructions assume you are in a shell prompt on CHIP.

1. Get the shell script file onto CHIP:

        sudo wget -O/etc/init.d/axp209-nolim http://fordsfords.github.io/axp209-nolim/axp209-nolim
        sudo chmod +x /etc/init.d/axp209-nolim
        sudo update-rc.d axp209-nolim defaults

Now, do a graceful restart:

        sudo shutdown -r now

When you can log back into CHIP, test to verify that the scirpt worked:

        sudo i2cget -f -y 0 0x34 0x30

It should respond with:

        0x63

At this point, you can start your CHIP with peripherals attached.  If CHIP
shuts itself down, then your problem was not the current limiter, it is your
power supply or cable.  It is drooping its voltage when CHIP demands a current spike.
Get a better power supply or cable.


## UNDO - Restoring the normal current limit

Once you resolve your power issues, you should remove the axp209-nolim startup
script:

        sudo update-rc.d axp209-nolim remove
        sudo rm /etc/init.d/axp209-nolim
        sudo i2cset -f -y 0 0x34 0x30 0x60


## Misc Notes

* This startup script was cloned from /etc/init.d/udev-finish
* I learned about the SysV init scripts at
  https://wiki.debian.org/LSBInitScripts
* I still don't understand run levels in Debian.  :-(


## Release Notes

* 18-Jun-2016

    Initial release.
