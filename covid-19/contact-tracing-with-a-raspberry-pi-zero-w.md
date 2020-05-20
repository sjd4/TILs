Contact tracing with a Raspberry Pi Zero-W
------------------------------------------

[](https://twitter.com/CoolSWEng)

[](https://www.facebook.com/diomidis.spinellis)[](https://github.com/dspinellis/)[](https://www.youtube.com/user/dspinellis/)[](https://www.linkedin.com/in/dspinellis)

Contact tracing via smartphone apps has been widely touted as an important way to control and limit the spread of the COVID-19 epidemic. However, basing contact-tracing on phone apps has several limitations. Here are instructions for constructing a contact tracing device prototype with a [Raspberry Pi Zero-W](https://www.raspberrypi.org/products/raspberry-pi-zero-w/). The constructed device is compatible with the Apple/Google Bluetooth contact tracing specification. It runs the [Epidose](https://github.com/dspinellis/epidose) software, which is based on the [DP3T](https://github.com/DP-3T/documents) "unlinkable" design.

### Materials

![Required materials](https://www.spinellis.gr/blog/20200520/materials.jpg)

Required materials

Assemble the following materials:

-   [Raspberry Pi Zero-W](https://www.raspberrypi.org/products/raspberry-pi-zero-w/)
-   MicroSD card (minimum recommended size 8 GB)
-   Small (3mm) red LED
-   330 Ohm, 0.25 W resistor
-   Single SPST momentary push button upload of their contacts to the Health Authority.

![Circuit diagram](https://www.spinellis.gr/blog/20200520/circuit.png)

Circuit diagram

The objective of this construction is to provide the Raspberry Pi with a switch to authorize the contact upload and a LED to indicate that a user is at risk. The circuit diagram above shows this setup.

### Connect the resistor to the LED

![Resistor LED connection](https://www.spinellis.gr/blog/20200520/solder.jpg)

Resistor LED connection

Solder one end of the resistor (it doesn't matter which) to the LED's *cathode* (the shorter of the LED's two wires) to obtain a 17mm long connection. The connected assembly should be able to fit in [pins 20 and 40](https://pinout.xyz/#) of the Raspberry Pi.

### Isolate the LED-resistor connection

![Isolating the connection](https://www.spinellis.gr/blog/20200520/isolate.jpg)

Isolating the connection

Cut a 17mm length from the smallest heat-shrink tube that can fit around the resistor, and slide it to cover the entire LED-resistor connection. Heat it to shrink.

### Solder components on the Raspberry Pi

![Assembled Raspberry Pi](https://www.spinellis.gr/blog/20200520/final.jpg)

Assembled Raspberry Pi

-   Solder the free end of the LED (its anode) to pin 40.
-   Solder the free end of the resistor to pin 20.
-   Solder the push button between pins 14 and 10 (orientation isn't important).

### Power up and install the Epidose software

-   Connect the Raspberry Pi to a power source. A USB power bank can make this setup portable.
-   Follow the instructions in the [Epidose repository](https://github.com/dspinellis/epidose) repository to install and configure the software.
-   You can test the hardware's operation by running the command `sudo venv/bin/python epidose/device/device_io.py -t`. This allows you to toggle the LED by pressing the button. Press Control-C and then press the button to terminate the program.

[Read and post comments](https://www.spinellis.gr/cgi-bin/comment.pl?date=20200520#comments), or share through
