Note
~~~~

This is an unofficial port of Gordon's WiringPi library. Please do not
email Gordon if you have issues, he will not be able to help.

For support, comments, questions, etc please join the WiringPi Discord
channel: https://discord.gg/SM4WUVG

WiringPi for Python
~~~~~~~~~~~~~~~~~~~

Manual Build and Install
============

Get/setup repo
--------------

.. code:: bash

    git clone --recursive https://github.com/edatec/phantom-wiringPi-Python.git
    cd phantom-wiringPi-Python

Don't forget the ``--recursive``; it is required to also pull in the
WiringPi C code from its own repository.

Prerequisites
-------------

To rebuild the bindings you **must** first have installed ``swig``. ``python3-dev`` and ``python-setuptools`` are already pre-installed in the Phantom system.
phantom wiringPi should also be installed system-wide for access to the ``gpio`` tool.

.. code:: bash

    sudo apt-get install swig

Build & install with
--------------------

``sudo python setup.py install``

Or Python 3:

``sudo python3 setup.py install``


Usage
=====

You should add **sudo** in front of python commands.

.. code:: python

    import wiringpi

    # Currently, only wiringPiSetupSys is supported ant it MUST be called before using IO functions:
    wiringpi.wiringPiSetupSys()   # For /sys/class/gpio with GPIO pin numbering

**General IO:**

.. code:: python

    wiringpi.pinMode(6, 1)       # Set wiringpi pin 6 to 1 ( OUTPUT )
    wiringpi.digitalWrite(6, 1)  # Write 1 ( HIGH ) to pin 6

.. code:: python

    wiringpi.pinMode(6, 0)                  # Set wiringpi pin 6 to 0 ( INPUT )
    pin_input = wiringpi.digitalRead(6)     # Read wiringpi pin 6
    if pin_input:
        print("Input is 1")
    else:
        print("Input is 0")

**Setting up a peripheral:**

WiringPi supports expanding your range of available "pins" by setting up
a port expander. The implementation details of your port expander will
be handled transparently, and you can write to the additional pins
(starting from PIN\_OFFSET >= 64) as if they were normal pins on the Pi.

.. code:: python

    wiringpi.mcp23017Setup(PIN_OFFSET, I2C_ADDR)

This example was tested on a quick2wire board with one digital IO
expansion board connected via I2C:

.. code:: python

    wiringpi.mcp23017Setup(65, 0x20)
    wiringpi.pinMode(65, 1)
    wiringpi.digitalWrite(65, 1)

**Soft Tone:**

Hook a speaker up to your Pi and generate music with softTone. Also
useful for generating frequencies for other uses such as modulating A/C.

.. code:: python

    wiringpi.softToneCreate(PIN)
    wiringpi.softToneWrite(PIN, FREQUENCY)

**Bit shifting:**

.. code:: python

    wiringpi.shiftOut(1, 2, 0, 123)  # Shift out 123 (b1110110, byte 0-255) to data pin 1, clock pin 2

**Serial:**

.. code:: python

    serial = wiringpi.serialOpen('/dev/ttyS0', 9600)  # Requires device/baud and returns an ID
    wiringpi.serialPuts(serial, "hello")
    wiringpi.serialClose(serial)  # Pass in ID

**SPI:**

The ``wiringPiSPIDataRW()`` function needs to be passed a ``bytes``
object in Python 3. In Python 2, it takes a string. The following should
work in either Python 2 or 3:

.. code:: python

    wiringpi.wiringPiSPISetup(channel, speed)
    buf = bytes([your data here])
    retlen, retdata = wiringpi.wiringPiSPIDataRW(0, buf)

Now, ``retlen`` will contain the number of bytes received/read by the
call. ``retdata`` will contain the data itself, and in Python 3, ``buf``
will have been modified to contain it as well (that won't happen in
Python 2, because then ``buf`` is a string, and strings are immutable).

**Full details of the API at:** http://www.wiringpi.com



