Arduino: Disable auto-reset
###########################

To disable the automatic reset of the Arduino board upon serial connection,
just stick a **120 Ohm resistor** between the ``RESET`` and ``+5V`` pins.

This is especially useful when the running application is expected to
communicate over serial port, and thus you don't want a reset each time a
connection is made.

In order to be able to upload your sketches again, simply unplug the
resistor during the upload, or follow this procedure:

1. Keep pressed the ``S1`` switch on the arduino board
2. Click upload / use the usual command to upload the firmware image
3. Once you see the ``RX`` LED blinking, release the ``S1`` switch.
