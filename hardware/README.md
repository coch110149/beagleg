Hardware support
================

While BeagleG currently is tied to the BeagleBone Black **am335x** CPU family utilizing its neat
PRU, it can support various hardware that wire up the outputs to stepmotor drivers.

There are various [BeagleBone-Black] capes that provide such stepper motor drivers for 3D printers
and CNC machines.

This directory contains sub-directories with the name of the particular hardware. Each directory
contains the necessary hardware description used by BeagleG. You need to enable the hardware
you intend to use in the toplevel Makefile.

   * [BUMPS/](./BUMPS) From the maker of BeagleG, there comes the [BUMPS] board.
     It is designed to get the best possible performance by making sure that related
     pins (e.g. all the step outputs) are all on one GPIO port to be able to minimize the number
     of IO operations needed (native implementation).

   * [CRAMPS/](./CRAMPS) The [CRAMPS] board by Charles Steinkuehler is a popular cape.

(If you have access to other boards and run them with BeagleG, consider adding the support and
send a pull request)

## Load cape device tree
In each hardware subdirectgory directory, named after the cape, there is a device tree overlay
file that you need to install for your hardware (or just use the one that comes with your board).

We can enable your cape, you can use the
[start-devicetree-overlay.sh](./start-devicetree-overlay.sh) script in this directory:

    sudo ./start-devicetree-overlay.sh BUMPS/BeagleG.dts

(pass the DTS file of the board you are using as parameter)

This initializes the pinmux now, but we have to do this every time after boot. Also,
we'd like to have the cape installed as early as possible in the boot process
to properly set all the output values to safe values.

There is a script for that

    sudo ./install-devicetree-overlay.sh BUMPS/BeagleG.dts

In general, what this script is doing is to add the overlay name

    optargs=capemgr.enable_partno=BeagleG

to the `/boot/uboot/uEnv.txt` file to let the kernel know to enable that cape.

The kernel looks for the firmware in /lib/firmware - since at boot time the
root-fs is not mounted yet, just the init-rd ramdisk, we need to make sure
to have it in the uInitrd filesystem. The script copies it there.

The script assumes a certain way the boot happens, so it might depend on your distribution
if it actually works; you might want to inspect it first to what it does.

## Adding new hardware

Adding new hardware is, in the simple case, just creating a new subdirectory, providing a *.dts
file and adding a `beagleg-pin-mapping.h` file. Please check out the existing subdirectories
to get an idea. If you added a new board, consider sending a patch.

[BeagleBone-Black]: http://beagleboard.org/BLACK
[BUMPS]: http://github.com/hzeller/bumps
[CRAMPS]: http://reprap.org/wiki/CRAMPS