# Arduino IDE on Raspberry Pi with RPi-UNO-HAT

For using the Arduino IDE to program the AVR microcontroller on the RPi-UNO-HAT the jumpers **TX**, **RX** and **RES** have to be closed, which is also the default state of these jumpers.

* Install the Arduino IDE:

    ```
    $ sudo apt-get update
    $ sudo apt-get install arduino
    ```

* Install [Auto-Reset for AVRdude](https://github.com/CisecoPlc/avrdude-rpi) on GPIO4 (Pin 7):

    ```
    $ wget https://github.com/watterott/RPi-UNO-HAT/raw/master/docu/autoreset
    $ wget https://github.com/watterott/RPi-UNO-HAT/raw/master/docu/avrdude-autoreset
    $ chmod +x autoreset
    $ chmod +x avrdude-autoreset
    $ sudo mv autoreset /usr/bin
    $ sudo mv avrdude-autoreset /usr/bin
    $ sudo mv /usr/bin/avrdude /usr/bin/avrdude-original
    $ sudo ln -s /usr/bin/avrdude-autoreset /usr/bin/avrdude
    ```

* [Disable Linux serial console](http://elinux.org/RPi_Serial_Connection#Preventing_Linux_using_the_serial_port):

    ```
    $ sudo raspi-config
    ```

    Select:
    ```
    Advanced > Disable shell and kernel messages on the serial connection.
    ```

    Reboot the system:
    ```
    $ sudo reboot
    ```

  * If there are any problems the changes can also be made with nano:
        ```
        $ sudo nano /boot/cmdline.txt
        ```
        Remove references to ```console=ttyAMA0,115200``` and ```kgdboc=ttyAMA0,115200```.

        ```
        $ sudo nano /etc/inittab
        ```
        Remove or comment the line ```T0:23:respawn:/sbin/getty -L ttyAMA0 115200 vt100```.

* Test AVRdude:

    ```
    $ sudo avrdude -c arduino -p m328p -P /dev/ttyAMA0 -b 57600 -v
    ```

    The device signature of the ATmega328 is *0x1e950f*.

* Run the Arduino IDE.

* Select **Arduino Uno** under **Tools->Board**.

* Select the hardware serial port **/dev/ttyAMA0** under **Tools->Serial Port**.

    If the hardware serial port is not recognized then create a symlink to the standard Linux serial port *ttyS0*:

    ```
    $ sudo nano /etc/udev/rules.d/80-arduinopi.rules

    KERNEL=="ttyAMA0", SYMLINK+="ttyS0",GROUP="dialout",MODE:=0666
    ```

* Open the Blink Sketch under **File->Examples->01.Basics->Blink**.

* Start build and upload: **File->Upload**.

* The LED D13 on the RPi-UNO-HAT will start to blink after the upload is finished.
