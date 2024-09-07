# USBEE
## USB-over-Network Universal Windows Driver

This project implements a Universal Windows Driver (UWD) that:

1. Receives encapsulated USB data sent over the network (from Ethernet or Wi-Fi adapter), forwards that data to a USB controller as if the data was coming from a USB device plugged into the machine.
2. Sends encapsulated USB data over the network (via Ethernet or Wi-Fi adapter) to a destination device whose IP address is defined in a custom Windows Registry entry.
3. Simulates a hardware-connected USB device so it appears as a USB input device from the OS's perspective.

## Tools and Frameworks

- **Windows Driver Kit (WDK)**: For driver development.
- **Network Components**: Windows Network Driver Interface Specification (NDIS) or Winsock for handling network communication.
- **USB Simulation**: Windows Kernel-Mode Driver Framework (KMDF) for simulating USB hardware devices.
- **Windows Filtering Platform (WFP)**: Optional, for inspecting and modifying network packets at a low level.

## Key Concepts

### 1. Network Data Handling

#### Winsock or NDIS for Network Handling
- Establish network connections and send/receive encapsulated USB data using Winsock or NDIS.
- **Winsock** can be used for simpler TCP/UDP socket programming.

#### Registry Configuration
- The IP address of the destination device is stored in the Windows Registry.
- Example code to read from the registry:

```cpp
NTSTATUS ReadRegistryIPAddress(UNICODE_STRING* destIP) {
    UNICODE_STRING regPath = RTL_CONSTANT_STRING(L"\\Registry\\Machine\\Software\\YourDriver\\Settings");
    // Open and read the IP address registry key here
}
```

### 2. USB Device Simulation (Virtual USB Device)
#### KMDF for USB simulation:

To simulate a USB device, you can use the Kernel Mode Driver Framework (KMDF). You will need to create a virtual USB device that behaves like an input device (e.g., keyboard or mouse).

Create a KMDF function driver that reports itself as a USB device. You’ll need to define descriptors (e.g., HID descriptors) that mimic those of a real device.
#### Example of creating a virtual HID device:

```cpp
Copy code
NTSTATUS
EvtDeviceAdd(
    _In_    WDFDRIVER       Driver,
    _Inout_ PWDFDEVICE_INIT DeviceInit
) {
    // Set device as HID or USB and prepare it for interaction
}
```
#### USB data forwarding:

Once the network data is received, unpack the USB encapsulated data and forward it to the virtual USB controller.

KMDF supports creating a driver that communicates with the USB stack. 

You'll simulate device responses such as URBs (USB Request Blocks) to match the behavior of real USB devices.

### 3. Sending and Receiving USB Encapsulated Data
#### Encapsulation/Decapsulation Logic:

The encapsulated USB data format needs to be clearly defined, e.g., packing URBs and other USB communication details into network packets.
You’ll also need to ensure proper routing of packets via network adapters to/from the virtual USB device.

#### Example of reading incoming network data:

```cpp
Copy code
NTSTATUS ReadNetworkData() {
    // Read encapsulated USB data from network socket or NDIS filter.
    // De-encapsulate and process it as USB data
}
```
#### Network to USB routing:

Once you have encapsulated USB data, send it over the network (using Winsock or NDIS) to the destination IP retrieved from the Windows Registry.
Example of network sending:

```cpp
Copy code
NTSTATUS SendNetworkData(const void* usbData, size_t length) {
    // Encapsulate the USB data and send it over the network to a destination.
    // Destination IP retrieved from the registry.
}
```
### Development Approach
#### Create a KMDF-based virtual USB device:

Start by creating a simple virtual USB device using KMDF that can be seen as a connected USB input device by the OS. Simulate basic functionality like keyboard input.

#### Implement network communication:

Implement socket communication using Winsock or NDIS to send and receive encapsulated USB data. Ensure that your driver can communicate with a remote device over Ethernet/Wi-Fi.
Handle data encapsulation and forwarding:

Develop the logic for encapsulating and de-encapsulating USB data, ensuring it can be routed correctly between the network and the virtual USB device.

#### Test and debug:

Thoroughly test the driver with different types of encapsulated USB data and ensure the virtual device behaves as expected from the OS’s perspective.

#### Challenges
- Real-time constraints: USB data is often real-time, so network latency might introduce challenges.
Error handling: Ensure robust error handling for network and USB communication issues.
- Security: Since USB and network data are being transmitted, you should consider encryption (e.g., TLS) to secure communications.

By following this approach, you’ll create a UWD that can simulate a USB device, handle network communication, and pass USB data between the virtual USB device and a remote networked USB source or destination.