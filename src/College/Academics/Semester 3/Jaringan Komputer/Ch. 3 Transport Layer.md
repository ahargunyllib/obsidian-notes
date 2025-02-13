Tujuan:
1. Mengerti prinsip-prinsip di balik layanan-layanan transport layer:
   - multiplexing, demultiplexing
   - reliable data transfer
   - flow control
   - congestion control
2. Belajar tentang protokol-protokol transport layer di Internet:
   - UDP: connectionless transport
   - TCP: connection-oriented reliable transport
   - TCP congestion control

# Layanan-layanan Transport Layer
## Transport layer services
- Layanan pada transport layer menyediakan **logical communication** antara proses aplikasi yang berjalan pada host-host yang berbeda
- Tindakan protokol transport di **end systems**:
	- Sender: memecah **application messages** menjadi **segments**, pass ke network layer
	- Receiver: merakit ulang **segments** menjadi **messages**. pass ke application layer
	- Terdapat dua protokol transport untuk aplikasi internet: TCP dan UDP 
## Transport vs network layer services and protocols
- Transport layer: **communication between processes**, bergantung dan meningkatkan **network layer service**
- Network layer: **communication between hosts**
## Two principal Internet transport protocols
- **TCP**: Transmission Control Protocol
	- **Reliable** (dapat diandalkan) dan **in-order delivery** (pengantaran terurut)
	- **Congestion control** (kontrol kemacetan)
	- **Flow control** (kontrol aliran)
	- **Connection setup** (setup koneksi)
- **UDP**: User Datagram Protocol
	- **Unreliable** dan **unordered delivery**
	- **No-frills extension of "best effort" IP** (perpanjangan tanpa embel-embel dari IP "best effort")
- **Services** yang **tidak** tersedia:
	- **Delay guarantees**
	- **Bandwidth guarantees**
# Multiplexing and Demultiplexing
- Multiplexing as **sender**: **handle data from multiple sockets, add transport header (later used for demultiplexing)**
- Demultiplexing as **receiver**: **use header info to deliver received segments to correct socket**
## How demultiplexing works
- Host receives IP datagrams
	- Tiap **datagram** mempunyai **source and destination IP address**
	- Tiap **datagram** membawa **one transport-layer segment**
	- Tiap **segment** mempunyai **source and destination port number**
- Host menggunakan **IP addresses and port number** untuk mengarahkan **segment** ke **socket** yang sesuai
## Connectionless Demultiplexing
- Ketika membuat **socket**, harus menentukan **host-local port**
- Ketika membuat **datagram**, harus menentukan **destination IP address and port number**
- Ketika **receiving host** menerima **UDP segment**:
	- Cek **destination port number** di **segment**
	- Mengarahkan **UDP segment** ke **socket** dengan **port number** yang sesuai
	- **IP/UDP datagrams** dengan **destination port number** yang sama tetapi **source IP addresses** dan/atau **source port numbers** berbeda, akan diarahkan ke **socket yang sama** di **receiving host**
## Connection-oriented Demultiplexing
- **TCP socket** diidentifikasi oleh **4-tuple**: **(source | destination) (IP address | port number)**
- **Demultiplexing**: **receiver** menggunakan **4-tuple** untuk mengarahkan **segment** ke **socket** yang sesuai
- **Server** dapat mendukung banyak **TCP sockets simultaneously**:
	- Tiap **socket** diidentifikasi oleh **4-tuple** miliknya
	- Tiap **socket** dikaitkan dengan **connecting client** yang berbeda
## Summary
- Multiplexing and demultiplexing: based on segment and datagram header field values
- UDP: demultiplexing using only destination port number
- TCP: demultiplexing using 4-tuple
- Multiplexing and demultiplexing happen at all layers
# Connectionless transport: UDP
- UDP termasuk internet transport protokol yang "polos an" (**"no frills"**, **"bare bones"**)
- **"best effort" service**, **UDP segments** bisa jadi hilang atau diantarkan secara **out-of-order** ke aplikasi
- **Connectionless**:
	- Tanpa **handshaking** antara **UDP sender** dan **UDP receiver**
	- Tiap **UDP segment** ditangani **independently**
- **Why is there a UDP?**
	- **No connection establishment which can add RTT delay**
	- **Simple**: **no connection state at sender and receiver**
	- **Small header size**
	- **No congestion control**: **UDP can blast away as fast as desired and function in the face of congestion** (translate sendiri)
- Penggunaan **UDP**:
	- Aplikasi multimedia streaming (**loss tolerant and rate sensitive**)
	- DNS
	- SNMP
	- HTTP/3
- Jika **reliable transfer** dibutuhkan melalui **UDP** (seperti HTTP/3):
	- Tambah **reliability** pada layer application
	- Tambah **congestion control** pada layer application
## UDP: checksum
- Mendeteksi **error** (seperti **flipped bits**) dalam **transmitted segment**
- Sender:
	- Memperlakukan isi **UDP segment** (termasuk **UDP header fields** dan **IP addresses**) sebagai **sequence of 16-bit integers** 
	- Checksum: penjumlahan (jumlah komplemen satu) dari isi **segment**
	- Nilai Checksum diletakkan dalam **UDP checksum field**
- Receiver:
	- **Compute checksum** dari **received segment**
	- Cek apakah **computed checksum** sama dengan **checksum field value**:
		- Jika tidak sama maka terdapat **error**
		- Jika sama **mungkin** tidak ada **error**
## Summary
- **"no frills" protocol**:
	- segments may be lost or delivered out of order
	- best effort service: "send and hope for the best" (lol)
- UDP has its plusses:
	- No setup/handshaking needed (no RTT incurred)
	- Can function when network service is compromised
	- Helps with reliability (checksum)
- Build additional functionality on top of UDP in application layer (e.g., HTTP/3)
# Principles of reliable data transfer
> sumpah gua ga maksud pptnya, ini dari ChatGPT

Reliable data transfer means making sure data gets from one place to another without errors or loss. Imagine sending a package to a friend—you want to make sure it arrives safely. Here are the key principles in a simple way:
### 1. **Acknowledgment (ACKs)**
Your friend sends a "Thank you" message when they get the package. In data transfer, the receiver sends an acknowledgment to confirm they got the data correctly.
### 2. **Retransmission**
If your friend doesn’t send a "Thank you" message, you resend the package. In networking, if the sender doesn’t get an acknowledgment, it resends the data.
### 3. **Error Detection**
Imagine you wrap your package in clear plastic. If it’s damaged or tampered with, your friend will know. In data transfer, checks like CRC (Cyclic Redundancy Check) help detect errors in data.
### 4. **Flow Control**
If your friend can only open one package at a time, you don’t send ten at once. Similarly, flow control ensures the sender doesn’t overwhelm the receiver with too much data.
### 5. **Sequencing**
If you send multiple packages, you number them so your friend can open them in the correct order. For data transfer, sequence numbers ensure data is reassembled properly.
### 6. **Timeouts**
If you don’t hear back from your friend after some time, you assume the package is lost and resend it. Timeouts in networking work the same way to detect lost data.
### 7. **Congestion Control**
If the road to your friend is crowded, you might send fewer packages to avoid delays. In networking, congestion control prevents overloading the network.

These principles work together to ensure your "data package" reaches the other side intact and in the right order. 😊
# Connection-oriented transport: TCP
## Overview
- **Point-to-point**: **one sender one receiver**
- **Reliable, in-order byte steam**: **no "message boundaries"**
- **Full duplex data**: **bi-directional data flow in same connection**
- **Pipelining**: **TCP congestion and flow control set window size**
- **Connection-oriented**: **handshaking (exchange of control messages) initializes sender, receiver state before data exchange**
- **Flow controlled**: **sender will not overwhelm receiver**

> sub bab selanjutnya di generate ChatGPT
## Segment structure
Think of a TCP segment as an envelope that holds a piece of data you're sending. It has the following parts:

- **Header**: Like the envelope address, this contains important info (such as where the data is coming from and going to).
- **Data**: The actual content you're sending, like a letter inside the envelope.
- **Checksum**: Like a seal on the envelope that helps ensure the data hasn’t been tampered with.

This structure helps TCP know where the data should go, whether it's been received properly, and helps in error checking.
## Reliable data transfer
TCP is like sending a certified letter where you know the recipient got it safely.

- **Acknowledgments (ACKs)**: After receiving the data, the receiver sends a "receipt" to confirm. If the sender doesn’t get this receipt, it resends the data.
- **Retransmissions**: If the data is lost or corrupted (like a torn envelope), TCP makes sure it’s sent again until it gets through correctly.
- **Error Checking**: TCP uses checks like the checksum to make sure the data inside is correct, like checking that a letter isn’t smudged or damaged.
## Flow control
This is like making sure you don’t send too many letters at once to your friend if they can only handle one or two.

- **Receiver Window**: The receiver tells the sender how much data it can handle at once (like saying, "I can only read 5 letters, so hold off on sending more").
- **Sender Adjusts**: TCP adjusts the flow of data to avoid overwhelming the receiver. If the receiver can handle more, it says so, and the sender can speed up.
## Connection management
Before sending data, TCP sets up a "meeting" between the sender and receiver, just like you’d arrange a time to meet someone before talking.

- **Three-way handshake**:
    1. The sender says, "I want to send data" (SYN message).
    2. The receiver responds, "Okay, I’m ready" (SYN-ACK).
    3. The sender confirms, "Great, let's start!" (ACK).

This setup ensures both sides are ready for data transfer.

- **Connection Termination**: After the conversation (data transfer) is over, TCP safely closes the connection, making sure everything is wrapped up neatly (like saying goodbye after a meeting).
## Summary:
- **Segments**: Like envelopes with data and important info.
- **Reliable transfer**: Ensures data gets through correctly, with checks and resending if needed.
- **Flow control**: Prevents overwhelming the receiver.
- **Connection management**: Sets up and safely closes the connection between sender and receiver.
# Principles of congestion control
- Congestion secara informal: **"too many sources sending too much data too fast for *network* to handle"**
- Manifestasi:
	- **Long delay (queueing in router buffers)**
	- **Packet loss (buffer overflow at routers)**
> harusnya ada penyebab/biaya dari congestion skenario 1 2 3 sama approaches towards congestion control
# TCP congestion control
- Approach: senders can increase sending rate until packet loss (congestion) occurs, then decrease sending rate on loss event
- Additive increase: increase sending rate by 1 maximum segment size every RTT until loss detected
- Multiplicative decrease: cut sending rate in half at each loss event
> sisanya ntah lah
