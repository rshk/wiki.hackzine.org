Arduino: arscons
####

Tired of using that stupid java thing to write / upload your firmwares to arduino?
Use arscons_ then (or `my slightly improved fork`_).

Simply download the SConstruct file and put it in your project directory, alongside the ``.pde`` file.
Please keep in mind that the directory should have the same name of the pde (without extension, of course).

Then use the following commands::

    scons ARDUINO_HOME=/path/to/arduino/
    scons ARDUINO_HOME=/path/to/arduino/ upload

to build & upload your "sketch".

Compiling on Gentoo Linux amd64
====

I had some issues using arscons on 64bit Gentoo. I solved by adding two missing paths to ``CFLAGS``.

You can find a working fork here: https://github.com/rshk/arscons

More info can be found here: http://www.arduino.cc/playground/linux/gentoo

If you don't need the whole java ide, you just need to:

    modprobe ftdi_sio

(if that's missing, enable the following two in kernel config):

    Device Drivers -> USB support -> USB Serial Converter support -> USB FTDI Single Port Serial Driver
    Device Drivers -> USB support -> USB Modem (CDC ACM) support

And install the following deps:

    emerge sys-devel/crossdev
    emerge dev-embedded/avrdude
    USE="-openmp" crossdev -t avr -s4 -S --without-headers

To use the ``/dev/ttyUSBX`` devices, just add your user to the ``uucp`` group.


.. _arscons: https://github.com/suapapa/arscons
.. _my slightly improved fork: https://github.com/rshk/arscons
