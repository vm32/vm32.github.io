## Hardware Hacking in Cybersecurity

Whenever we look at any embedded device in a Target Network, there are various ways in which it can be attacked. One of the most useful ways to attack it is using hardware based exploitation techniques. These techniques are quite effective as most of the devices we encounter daily don’t usually have security protections build around the hardware aspect.

When we talk about hardware hacking — we have physical devices as attack surfaces, and we have embedded devices to test on, One may try to start Testing that piece of hardware by enumeration some useful information with the help of a few tools that will be listed below.

By opening the casing of the device there’s a motherboard on which all the circuitry is present which can give us an how the device works like what IC the device is using. Once you get the name of the IC you can start looking for the DATA Sheet of that particular IC and understand how it’s working and move further by analyzing the device.

![Animated GIF](https://miro.medium.com/v2/resize:fit:640/format:webp/1*r1FgrQpFIfqtPSf7l-tqOg.gif)

It is possible that you can get an easy shell on the device with a UART device as most of the devices reveal their RXD and TXD pins, it is possible that someone can get a shell via these pins through a Serial connection.

You will need to gain access to the hardware’s electronics. This is bit more involved since a lot of devices are physically designed to make it difficult to access the embedded electronics. They have hard plastic shells and hidden screws that require uncommon bits to open.

Finally using the following hardware tools you will identify debug ports and serial protocol interfaces, dump firmware, and reverse engineer the target device.

These tools will allow you to explore your target device through the hardware’s various serial bus interfaces or allow you to dump the firmware image from the device for reverse engineering the software. The firmware image is a compressed file, containing the operating system and its files, it may contain interesting things like the code to the web interface that most of these devices have. You can then run that dumped code and reverse engineer it on an emulator like QEMU [QEMU](http://wiki.qemu.org/Main_Page)


![Sample PNG](https://miro.medium.com/v2/resize:fit:720/format:webp/1*ymTZWIy6oRu74lBGBft0fg.png)

Some of the main serial bus interfaces that the following hardware tools can connect to our

JTAG [JTAG](http://concerts.livenation.com/)

    UART http://jcjc-dev.com/2016/04/08/reversing-huawei-router-1-find-uart/
    I2C https://hackaday.com/tag/i2c-bus/
    SPI http://hackaday.com/2016/07/01/what-could-go-wrong-spi/

Researching all the serial interfaces and their protocols will help you understand how to effectively use the following hardware tools for reverse engineering and exploiting IoT devices.
# LIST OF HARDWARE TOOLS
# USB To TTL CONVERTER
![Sample PNG](https://miro.medium.com/v2/resize:fit:640/format:webp/0*tih6twW9e6mLC_tY)

It is a combination of two products: a USB to RS232 Adapter (USB-232–2) utilizing the FTDI chipset and the RS232 to 5V TTL Converter (TTL-232–5P). It can convert any standard full duplex USB port to a 5V TTL signal in either direction. The unit is powered from the USB port and requires no external power.

# LOGIC ANALYZER
![Sample PNG](https://miro.medium.com/v2/resize:fit:720/format:webp/0*ljIuPmrCUVw9YuXf.jpeg)

A logic analyzer is an electronic instrument that captures and displays multiple signals from a digital system or digital circuit. A logic analyzer may convert the captured data into timing diagrams, protocol decodes, state machine traces, assembly language, or may correlate assembly with source-level software.

# SHIKRA
![Sample PNG](https://int3.cc/cdn/shop/products/IMG_0902_1024x1024.JPG?v=1518652948)
This device is touted as a more stable tool compared to the Bus Pirate. The hardware is very reliable and stable for connecting to UART, JTAG, and SPI. Many people in the Software Exploitation via Hardware Exploitation community enjoy using this somewhat lesser-known device and is used in the SEXviaHEX http://www.sexviahex.com/ training.

If you want to pull the firmware image off a target IoT device for software exploitation then the Shikra is a great tool for the job. Just connect the Shikra to the target device’s SPI chip. You may need an 8-pin SOIC clip to connect the Shikra to the SPI interface.

blackhillsinfosec [blackhillsinfosec](https://www.blackhillsinfosec.com/hardware-hacking-with-shikra/)

In the how-to-use guide linked above, it was claimed to have taken the Bus Pirate 30 minutes to dump a 4MB firmware image of a device compared to just under a minute for the Shikra to do the same job. The Shikra may be something less people are familiar with, but it provides consistent, powerful and fast performance for certain jobs.
# BUS PIRATE
![Sample PNG](https://lh3.googleusercontent.com/proxy/d3W-QqrLQAMPTBm2pdZAvo1YU104pXJhr0dEF-C8Ri9HVSDGuLNGwUJwDSh2ivjFldA-ruDbkDmVH43v78cyYUIWPaEN72lf9KXUeDpAELXU8Kx_ovZN-A)

This is one of the most widely used tools out there right now. At the time of this blog’s posting the Bus Pirate version 4 official firmware development seems to have been abandoned. This has caused a lot of headaches for users struggling to get features to work on version 4 as well as they did on version 3. For example, some people have had difficulties with getting JTAG support to work on version 4.

As stated in the documentation link, the version 3 firmware has a strong community effort behind it. As long as there is a strong community backing this tool that community will be committed to fixing and maintaining the firmware of the Bus Pirate. Overall the Bus Pirate is a very robust tool. Finding someone to help you use it will not be hard.
# JTAGULATOR
![Sample PNG](https://media.parallax.com/wp-content/uploads/2020/07/13151959/32115.png)
Besides looking badass this tool is great for identifying what the different pinouts and chips do on the target device. When you open up the device it is not going to be obvious what pinouts and chips run which serial protocols. Testing each one with the JTAGulator will help you find your UART, JTAG, SPI, and other serial protocol interfaces.
# FACEDANCER21
![Sample PNG](https://hackerwarehouse.com/wp-content/uploads/2014/10/facedancer21-454A9092b-1024.jpg)

Not every IoT device is going to have a USB port, but this tool can be very useful when one is available. The Facedancer, besides having a cool name, essentially lets your computer become the USB drive plugged into a device. Within this emulation, you can communicate to the target device over the USB bus with Python. Devices often trust USB drives plugged into them so exploring the target device from this perspective can be very rewarding.
# OSCILLOSCOPE
![Sample PNG](https://cdn.sparkfun.com/assets/8/3/c/d/d/52f29d4dce395fe72c8b4569.jpg)
An oscilloscope allows you to watch how the voltage between two points in a circuit varies over time. For example, if you were to probe a sinusoidal voltage, your oscilloscope might display something similar to Figure 2. The most popular modern oscilloscope is the digital storage oscilloscope (DSO).

Note: This thing is not so cheap and it is not mandatory if you are not making your piece of electronics.
SOME IMPORTANT TOOLS

    - A set of screwdriver
    - Probers and Jumper Wires
    - A sharp Knife
    - Pliers and wire strippers
