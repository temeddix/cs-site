---
title: The Structure of a Packet
date: 2025-04-03 19:58:00 +0900
categories: [Network]
tags: []
---

When someone is sending very large data, say 100GB, it doesn't get sent in one big chunk. Instead, it is split into a network transmission unit called a "packet." A typical packet size is around 1,500 bytes (1.5 KB) for Ethernet networks, although this can vary depending on the network protocol and configuration.

A packet consists of a payload and a header, sometimes with a trailer. The trailer is the last part of a packet and is often used for error detection, such as a cyclic redundancy check (CRC), to ensure the data integrity of the packet during transmission.

## Packet

| Component | Description                                                                            | Approximate Size |
| --------- | -------------------------------------------------------------------------------------- | ---------------- |
| Header    | Contains metadata, such as source and destination addresses, and protocol information. | ~8-60 bytes      |
| Payload   | The actual data being transmitted.                                                     | ~1,400 bytes     |
| Trailer   | Used for error detection and data integrity checks (e.g., CRC).                        | ~4 bytes         |

## Header

### Using TCP Protocol

Because **port**s can have values between 0-65535, they occupy 2 bytes in the packet.

**Sequence number** and **acknowledgment number** are 4 bytes each and are critical for ensuring reliable data transmission. They help in tracking the order of packets and confirming successful delivery, which is related to the reliability of TCP. **Sequence Number** is used to indicate the position of the first byte of data in the current packet relative to the start of the data stream (the entire sequence of data being transmitted, such as a 100GB file). It helps the receiver assemble the data in the correct order. By sending the **Acknowledgment Number**, the receiver confirms that it has successfully received all bytes up to (but not including) this number.

**Flags**, which are 1-2 bytes, are used for managing the connection state, such as initiating or terminating a connection. Each flag is represented by a single bit in the TCP header, and it can be turned on (set to 1) or off (set to 0) depending on the state of the connection. The most important flags include `SYN`, `ACK`, and `FIN`.

These flags are critical for managing the lifecycle of a TCP connection, ensuring reliable communication between devices.

The checksum, which is 2 bytes, is used for error detection to ensure data integrity during transmission.

| Field                 | Description                                                           | Approximate Size |
| --------------------- | --------------------------------------------------------------------- | ---------------- |
| Source Port           | Identifies the sending application.                                   | 16 bits          |
| Destination Port      | Identifies the receiving application.                                 | 16 bits          |
| Sequence Number       | Ensures data is received in the correct order.                        | 32 bits          |
| Acknowledgment Number | Confirms receipt of data.                                             | 32 bits          |
| Data Offset           | Specifies the size of the TCP header in 4-byte units.                 | 4 bits           |
| Reserved              | Reserved for future use (must be set to 0).                           | 4 bits           |
| Flags                 | Control flags like `SYN`, `ACK`, and `FIN` for connection management. | 8 bits           |
| Window                | Specifies the size of the receive window for flow control.            | 16 bits          |
| Checksum              | Used for error detection.                                             | 16 bits          |
| Urgent Pointer        | Points to urgent data if the `URG` flag is set.                       | 16 bits          |
| TCP Options           | Optional fields for additional functionality (e.g., timestamps).      | Variable         |

> While a 4-byte sequence number can only represent values up to 4GB (2^32), TCP uses sequence number wrapping to handle larger data streams. When the sequence number reaches its maximum value, it wraps around to 0 and continues. This is managed by the protocol to ensure reliable data transmission, even for files larger than 4GB.

### Using UDP Protocol

Unlike TCP, UDP is stateless and does not guarantee reliable delivery, order, or error correction. It is faster and has lower overhead, making it suitable for time-sensitive applications where some amount of data loss is acceptable.

The UDP header includes a length field because UDP is a stateless protocol and does not inherently know the size of the data being transmitted. The **length** field specifies the total size of the UDP header and payload, ensuring the receiver can process the packet correctly. This is different from TCP, which does not need a length field in its header because it uses **sequence numbers** to track the position of data in the stream and ensures that packets are received in the correct order. Since TCP guarantees ordered delivery and tracks the data stream using sequence numbers and acknowledgment numbers, the length of the data in each packet can be inferred, making a separate length field unnecessary.

| Field            | Description                                      | Approximate Size |
| ---------------- | ------------------------------------------------ | ---------------- |
| Source Port      | Identifies the sending application.              | 16 bits          |
| Destination Port | Identifies the receiving application.            | 16 bits          |
| Length           | Specifies the length of the UDP header and data. | 16 bits          |
| Checksum         | Used for error detection (optional in IPv4).     | 16 bits          |
