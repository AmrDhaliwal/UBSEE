# USBEE ROADMAP

## To Do
##### Build UDE (Emulated USB Device) Client driver
- Can receive encapsulated USB data from a streambuffer, unencapsulate the data, and forward that USB data to the USB host controller.
- Must be able to retrieve raw USB data from the USB host controller, encapsulate that data, and write it to a streambuffer.
- Gracefully disconnect from the USB host controller upon detection of lost remote device/error (such as x *amount of time passed since last byte received.*).
##### Build Network driver
- Can read data from a streambuffer, and send data from that buffer to a destination IP defined by a custom Registry entry.
- Can receive data from a network adapter/specified source IP (defined by the same custom Registry entry mentioned before), and write that data to a streambuffer.

##### Build combo driver to encompass the UDE Client and Network drivers.
- Enable the UDE Client and Network drivers to write/read to/from the same input output streambuffers so as to connect the data flow.

##### Build a prototype of the remote USB link using a Raspberry Pi.
- Linux app running on Pi will execute at a low enough level to capture raw USB data from a specified USB port/socket (/dev/...).
- That raw USB data must then be sent over LAN/WLAN to a destination IP specified in a config file.
- The app must be able to receive encapsulated USB data from the destination IP and forward it as raw USB messages to the connected USB device specified in the config file as mentioned before.

## To Learn
- How to make a UDE Driver
- How to make a Network Driver
- How to make a combo driver
- How to pass data from one driver to another
    - Can combo drivers be used to group drivers and communicate between them
- Can a UDE Client driver (emulated USB device) belong to a combo driver
- Will forwarding USB data from the remote USB device to the emulated USB host be enough to let the PC detect the device/driver type of the remote USB device?
    - Are there any caveats or initial conditions we need to account for to ensure that the initial handshake between the remote USB device and the host machine (and the subsequent device detection) is successful?

## To Solve
- Will raw USB data require any encapsulation to be sent over network or will that be unneccessary?
- Will raw USB communication on Linux require knowledge and creation of Linux OS drivers? Or can this be done with an application (like C or Python) as a proof-of-concept?

## Assumptions
- A TCP connection between the remote USBEE device (remote USB adapter) and the USBEE PC driver is mandatory as USB data must be processed sequentially.
- Some detection mechanism for loss of connection is required so as to gracefully handle these scenarios at both the USBEE driver level and the USBEE device level. This way, both the PC USB controller and the remote USB device connected to the remote USBEE device will not be left waiting for data from the other.
- Delayed USB messages (due to network traffic congestion, low data rates, or poor wireless signal) may need to be ignored depending on the requirements of the emulated USB device driver type or the remote USB device.
