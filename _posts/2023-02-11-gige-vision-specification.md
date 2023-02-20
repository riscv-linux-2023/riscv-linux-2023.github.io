---
layout: post
title:  "GigE Vision Specification"
date:   2023-02-11 17:43:59 +0800
categories: camera 
---

## GVSP GVCP

[GigE Vision Video Streaming and Device Control Over Ethernet Standard version 2.2](https://www.automate.org/a3-content/vision-standards)

### Acronyms

Acronyms | Detail
---|---
AOI | Area of Interest
ARB | Arbitrary (as defined in IEEE 1588)
BOOTP | Bootstrap Protocol
CCP | Control Channel Privilege (a bootstrap register)
CFA | Color Filter Array
CID | Chunk Identifier
DHCP | Dynamic Host Configuration Protocol
dLAG | Dynamic Link Aggregation Group
DNS | Domain Name System
DNS-SD | Domain Name System - Service Discovery
EOC | End Of Codestream
EOI | End Of Image
FMO | Flexible Macroblock Ordering
GEV | GigE Vision
GIF | Graphic Interchange Format
IANA | Internet Assigned Numbers Authority
ID | Identifier
IDR | Instantaneous Decoding Refresh
IEC | International Electrotechnical Commission
IEEE | Institute of Electrical and Electronics Engineers
IETF | Internet Engineering Task Force
IP | Internet Protocol
IPv4 | Internet Protocol version 4
IPv6 | Internet Protocol version 6
ISO | International Organization for Standardization
ITU | International Telecommunication Union
GigE | Gigabit Ethernet
GMT | Greenwich Mean Time
GPS | Global Positioning System
GVCP | GigE Vision Control Protocol
GVSP | GigE Vision Streaming Protocol
JFIF | JPEG File Interchange Format
JPEG | Joint Photographic Experts Group
LAG | Link Aggregation Group
LFSR | Linear Feedback Shift Register
LLA | Link-Local Address
lsb | Least Significant Bit
LSB | Least Significant Byte
MAC | Media Access Control
mDNS | Multicast Domain Name System
ML | Multiple Links
MPEG | Moving Picture Experts Group
msb | Most Significant Bit
MSB | Most Significant Byte
MTU | Maximum Transmission Unit
NAL | Network Abstraction Layer
NAT | Network Address Translation
NIC | Network Interface Card
OSI | Open Systems Interconnection
OUI | Organizationally Unique Identifier
PC | Personal Computer
PFNC | GenICam Pixel Format Naming Convention
PTP | Precision Time Protocol
RFC | Request For Comments
ROI | Region of Interest
RTP | Real-time Transport Protocol
SDP | Session Description Protocol
SL | Single Link
sLAG | Static Link Aggregation Group
SOC | Start Of Codestream
SOD | Start Of Data
SOI | Start Of Image
SOT | Start Of Tile
SPI | Stateful Packet Inspection
TAI | International Atomic Time
TCP | Transmission Control Protocol
UCS | Universal Character Set
UDP | User Datagram Protocol
UTC | Coordinated Universal Time
UTF | UCS Transformation Format
UTP | Unshielded Twisted Pair
VCL | Video Coding Layer

### System Overview

![overview](/assets/images/gige/overview.png)

1. Transmitter: A transmitter is a device capable of streaming data. It includes one or more GVSP transmitters. For instance, this can be a *camera*.
2. Receiver: A receiver is a device capable of receiving data. It includes one or more GVSP receivers. For instance, this can be a *GigE Vision to HDMI converter*.
3. Transceiver: A transceiver is a device capable of receiving and transmitting data. It includes one or more GVSP receivers and one or more GVSP transmitters. For instance, this can be a *GigE Vision image processor*.
4. Peripheral: A peripheral is a device that cannot transmit or receive data. However, it can execute tasks controlled using the GigE Vision control protocol. For instance, this can be a *GigE Vision strobe light controller*.

### Device Discovery

Device Discovery covers the sequence of events required for a controllable device to get a connection through its network interface and obtain a valid IP address using standard IP protocols.

Device Discovery is separated in 3 sequential steps:

1. Link negotiation
2. IP configuration
3. Device enumeration

Physical Link Configuration

1. Single Link configuration (SL configuration)
2. Multiple Links configuration (ML configuration)
3. Static Link Aggregation Group configuration (static LAG configuration, sLAG)
4. Dynamic Link Aggregation Group configuration (dynamic LAG configuration, dLAG)

![discovery](/assets/images/gige/discovery.png)

### GVCP

> GVCP is an application layer protocol relying on the UDP transport layer protocol. It basically allows an application to configure a device (typically a camera) and to instantiate stream channels (GVSP transmitters or receivers, when applicable) on the device, and the device to notify an application when specific events occur.

GVCP 是一种基于 UDP 传输层协议的应用层协议。它基本上允许应用程序配置设备（通常是相机），并在设备上实例化流通道（适用时的 GVSP 发送器或接收器），以及设备在特定事件发生时通知应用程序。

> The current version of the specification uses UDP IPv4 as the transport layer protocol. Because UDP is unreliable, GVCP defines mechanisms to guarantee the reliability of packet transmission and to ensure minimal flow control.

当前的规范版本使用 UDP IPv4 作为传输层协议。由于 UDP 是不可靠的，GVCP 定义了一系列机制来保证数据包传输的可靠性，并确保流量控制最小化。

> When an application sends a GVCP packet, it uses the device GVCP port (3956) as the destination and its dynamically allocated UDP port number as the source.

当一个应用程序发送一个 GVCP 数据包时，它使用设备 GVCP 端口（3956）作为目的地，并使用其动态分配的 UDP 端口号作为源。

![gvcp](/assets/images/gige/gvcp.png)

![acknowledge](/assets/images/gige/acknowledge.png)

![timeout_req](/assets/images/gige/timeout_req.png)

![timeout_ack](/assets/images/gige/timeout_ack.png)

The Channel Concept

1. Control channel (there is always 1 control channel for the primary application)
2. Stream channel (from 0 to 512 stream channels)
3. Message channel (0 or 1 message channel)

![basic_channels_example](/assets/images/gige/basic_channels_example.png)

![advanced_channels_example](/assets/images/gige/advanced_channels_example.png)

![gvcp_commands_characteristics](/assets/images/gige/gvcp_commands_characteristics.png)

### GVSP

GVSP is an application layer protocol relying on the UDP transport layer protocol. It allows a GVSP receiver to receive image data, image information or other information from a GVSP transmitter.

GVSP packets always travel from a GVSP transmitter to a receiver.

![data_resend_flowchart](/assets/images/gige/data_resend_flowchart.png)

![data_block_standard_transmission_mode](/assets/images/gige/data_block_standard_transmission_mode.png)

### Bootstrap Registers

> A number of bootstrap registers are defined by this specification to allow configuration of a device. These registers are common to GigE Vision devices and are located at fixed addresses specified by this text. But a device can also allocate non-bootstrap registers in the device-specific memory space starting at address 0xA000. These manufacturer-specific registers are not defined by this specification and are typically advertised through the XML device description file.

本规范定义了一些引导寄存器，以允许配置设备。这些寄存器是 GigE Vision 设备的公共寄存器，位于本文指定的固定地址处。但设备也可以在从地址 0xA000 开始的设备特定内存空间中分配非引导寄存器。这些制造商特定的寄存器不由本规范定义，通常通过 XML 设备描述文件进行广播。

## SDK

![sdk](/assets/images/gige/sdk.png)

写一个 GigE Vision SDK 需要对网络协议、图像处理以及相机控制等方面有深入的了解。下面是一般的步骤：

1. 了解 GigE Vision 规范：请仔细阅读 GigE Vision 规范，了解它的核心功能、协议、命令等。
2. 选择开发语言：可以选择使用 C 或 C++ 语言编写 SDK。
3. 编写基础代码：编写基础代码，实现网络通信、图像传输、相机控制等基本功能。
4. 编写相机控制代码：编写相机控制代码，实现对相机参数的设置、读取、控制等功能。
5. 编写图像处理代码：编写图像处理代码，实现对图像的处理和分析。
6. 测试和调试代码：测试和调试代码，确保它正常工作并且符合 GigE Vision 规范。
7. 发布 SDK：当 SDK 符合需求并且通过测试后，可以将它发布供其他开发者使用。

以上是一般的开发过程。请注意，开发一个高质量的 SDK 可能需要花费很长的时间，并且需要对相关领域有深入的了解。

可以参考现有的 GigE Vision SDK，了解它们是如何实现的，并从中获得灵感和启发。

另外，为了保证 SDK 的质量和可用性，建议遵循一些最佳实践：

1. 清晰的代码结构：使用易于理解的代码结构，使代码易于维护和扩展。
2. 可读性：使用易于理解的代码风格，以便他人能够理解和使用代码。
3. 注释：为代码添加足够的注释，以帮助他人理解代码的目的和实现方式。
4. 单元测试：为代码编写单元测试，以帮助检测和修复代码中的错误。
5. 文档：为 SDK 编写详细的文档，以帮助其他开发人员使用代码。

以上是一些最佳实践，有助于提高 SDK 的质量和可用性。如果遵循这些建议，SDK 就可以更容易地被其他开发人员使用，并且可以长期维护和扩展。

在开发 GigE Vision SDK 时，还需要注意以下几点：

1. 兼容性：保证 SDK 与所有支持 GigE Vision 标准的硬件和软件兼容。
2. 可靠性：保证 SDK 在各种情况下都能正常工作，并且在出现错误时可以及时停止工作。
3. 可扩展性：保证 SDK 可以方便地扩展到支持更多功能和硬件。
4. 性能：尽量提高 SDK 的性能，以便在大量数据处理的情况下仍然能够保证高效率。
5. 可维护性：使用可维护的代码，并且定期对代码进行更新，以保证其在未来仍然可用。
6. 文档：为 SDK 提供详细的文档，以方便用户使用和了解如何使用。文档应该包含安装说明、API 参考、示例代码以及常见问题的解决方案。
7. 技术支持：提供充足的技术支持，以帮助用户解决使用 SDK 时遇到的问题。
8. 测试：为 SDK 进行充分的测试，以确保其能够在各种环境下正常工作。
9. 反馈：积极收集用户的反馈，并且根据反馈进行改进。
10. 发布：发布 SDK，并且定期更新 SDK，以保证其始终保持最新。

开发 GigE Vision SDK 是一项复杂的任务，但如果认真对待每一个细节，就能够开发出一个成功的 SDK。总之，通过坚持不懈的努力，将能够创造出一个受用户欢迎的 GigE Vision SDK。

这些注意事项都很重要，可以帮助开发出一个高质量、可靠和可扩展的 GigE Vision SDK。如果遵循这些建议， SDK 就能够更容易地适应市场的需求，并且能够为用户提供更好的体验。

下面是一些常见的 GigE Vision SDK：

1. Allied Vision Technologies (AVT) Vimba SDK：AVT 是一家领先的图像传感器制造商，它提供了免费的 Vimba SDK，该 SDK 支持 GigE Vision 和 USB3 Vision 标准。
2. Basler Camera SDK：Basler 是一家生产高品质图像传感器的公司，它提供了 Basler Camera SDK，该 SDK 支持 GigE Vision 和 USB3 Vision 标准。
3. MatrixVision mvIMPACT SDK：MatrixVision 是一家图像处理解决方案提供商，它提供了 mvIMPACT SDK，该 SDK 支持 GigE Vision 和 USB3 Vision 标准。
4. FLIR Systems Spinnaker SDK：FLIR 是一家生产热成像摄像机的公司，它提供了 Spinnaker SDK，该 SDK 支持 GigE Vision 和 USB3 Vision 标准。

这些 GigE Vision SDK 均提供了详细的文档和技术支持，可以帮助快速实现图像采集和处理。此外，如果需要定制解决方案，也可以考虑咨询这些公司的技术团队。

![demo](/assets/images/gige/demo.png)
