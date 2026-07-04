# System_Design_Overvieew


1 - [CAP Theorem](#cap-theorem-section)

2 - [OSI Model](#osi-model)

3 - [TCP/IP Model](#tcp/ip-model)


<a name="cap-theorem-section"><a/>
# 1 - CAP Theorem

<img width="2048" height="1933" alt="image" src="https://github.com/user-attachments/assets/a1d80d2e-48a5-43e6-9e76-96b8b373ee94" />

The CAP theorem is a fundamental rule in system design. It states that a distributed data store (a system made of multiple machines working together) can only simultaneously provide two out of three guarantees: Consistency, Availability, and Partition Tolerance.

Here is what those three letters actually mean in plain English:

- Consistency (C): All nodes display identical data, guaranteeing that reads always reflect the most recent write.
Every read receives the most recent write or an error. Imagine updating your profile picture; if the system is consistent, anyone checking your profile a millisecond later will see the new picture, not the old one.

- Availability (A): Every request receives a response, without guarantee that it contains the most recent write. Every non-failing node returns a non-error response, without the guarantee that it contains the most recent write. The system is always up and responsive, even if it occasionally serves stale data.

- Partition Tolerance (P): The system continues to operate despite network partitions.
The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

<img width="800" height="487" alt="image" src="https://github.com/user-attachments/assets/76ab1927-d556-41fd-8e26-8842661de877" />

<a name="osi-model"><a/>
# 2 - OSI Model

Think of the OSI (Open Systems Interconnection) model as a **conceptual universal translator** for networking. It breaks down how data travels from an app on your device (like a web browser) all the way across the world through cables, and up into another app.

The model splits this massive job into **7 distinct layers**. When you send data, it travels from Layer 7 down to Layer 1. When you receive data, it moves from Layer 1 up to Layer 7.

<img width="1363" height="2048" alt="image" src="https://github.com/user-attachments/assets/957d8a22-16ad-468a-a460-ee837c7ccf85" />


### The 7 Layers Explained (Top to Bottom)

To remember the order from 7 down to 1, network engineers use the phrase: **A**ll **P**eople **S**eem **To** **N**eed **D**ata **P**rocessing.

#### 7. Application Layer

This is the layer interacting directly with your software application. Your browser doesn't live here, but the network protocols it uses do.

* **What it does:** Provides network services directly to end-user applications.
* **Protocols / Tech:** HTTP, HTTPS, FTP, SMTP (email), DNS.

#### 6. Presentation Layer

This layer acts as the translator. It ensures that the data sent by Layer 7 is formatted in a readable way for the receiver.

* **What it does:** Data formatting, syntax translation, compression, and encryption/decryption (like SSL/TLS).
* **Protocols / Tech:** JPEG, MP3, ASCII, SSL/TLS.

#### 5. Session Layer

This is the "mechanic" that opens, manages, and closes the communication channel between two devices.

* **What it does:** Sets up, coordinates, and terminates conversations (sessions) between applications.
* **Protocols / Tech:** NetBIOS, RPC, SOCKS.

#### 4. Transport Layer

This layer is responsible for end-to-end communication and breaking large files into smaller chunks called **segments**.

* **What it does:** Flow control (not sending faster than a receiver can handle) and error control (checking if data arrived corrupted).
* **Protocols / Tech:** TCP (reliable, slow), UDP (unreliable, fast).

#### 3. Network Layer

This layer is all about finding the path to move data between different networks (routing). Data chunks here are called **packets**.

* **What it does:** Handles logical addressing (IP addresses) and routes packets across various interconnected networks.
* **Protocols / Tech:** IP (IPv4, IPv6), Routers, ICMP (ping).

#### 2. Data Link Layer

While the Network layer moves data *between* networks, the Data Link layer moves data between devices on the *same* local network. Data here is called **frames**.

* **What it does:** Handles physical addressing (MAC addresses) and fixes errors from the physical layer.
* **Protocols / Tech:** Ethernet, Wi-Fi (802.11), Network Switches.

#### 1. Physical Layer

The actual hardware. This layer deals with the raw electrical, optical, or radio signals traveling across physical media. Data here is just raw **bits** (0s and 1s).

* **What it does:** Transmits raw bitstreams over a physical medium.
* **Protocols / Tech:** Cables (Cat6, Fiber optic), Hubs, Modems.

---

### How Data Flows: The Post Office Analogy

Imagine writing a letter (Data) to a friend:

1. **Layer 7-5:** You write the letter (Application), translate it to English (Presentation), and log into your desk to start the task (Session).
2. **Layer 4 (Transport):** Your letter is too long for one envelope, so you split it into 3 pages, label them "1 of 3", "2 of 3", and "3 of 3", and include instructions on how to put them back together.
3. **Layer 3 (Network):** You place each page into an envelope and write your friend's global mailing address (IP Address) on them.
4. **Layer 2 (Data Link):** The post office looks at the zip code and puts the envelopes onto a specific local delivery truck (MAC Address / Switch).
5. **Layer 1 (Physical):** The truck physically drives down the asphalt road (Cables/Wires) to deliver the letters.


<a name="tcp/ip-model"><a/>
# 3 - TCP/IP Model

While the 7-layer **OSI model** is the gold standard for teaching networking concepts, the **TCP/IP model** is what the internet actually runs on.

The easiest way to think about it is that the OSI model is a **theoretical** framework designed by a committee, whereas the TCP/IP model is a **practical** framework designed by engineers to get computers talking immediately.

---

## The Core Difference: Layer Merging

The TCP/IP model combines several of the OSI layers because, in practice, software developers usually handle them all together. It condenses the 7 layers down into **4 layers**.

| OSI Layer | TCP/IP Layer | What lives there? |
| --- | --- | --- |
| **7. Application**<br>

<br>**6. Presentation**<br>

<br>**5. Session** | **4. Application** | Everything the user interacts with (HTTP, FTP, SMTP, DNS, encryption/TLS, and session management). |
| **4. Transport** | **3. Transport** | Controls host-to-host data flow (TCP, UDP). |
| **3. Network** | **2. Internet** | Routes packets across different networks globally (IP addresses, ICMP). |
| **2. Data Link**<br>

<br>**1. Physical** | **1. Network Access** *(or Link Layer)* | Handles physical hardware, local wiring, and local device jumping (Ethernet, Wi-Fi, MAC addresses). |

---

## Structural & Philosophical Differences

### 1. Theory vs. Practice

* **OSI (Open Systems Interconnection):** Created by the International Organization for Standardization (ISO) in 1984. It was designed *before* the protocols were actually written. Because it was built by a committee, it is highly structured and strict, but sometimes clunky.
* **TCP/IP (Transmission Control Protocol / Internet Protocol):** Developed by the US Department of Defense (DARPA) in the 1970s. It was built around existing protocols that were already working in the real world.

### 2. Loose vs. Rigid Boundaries

* **In OSI,** the boundaries are strict. An application developer *cannot* touch Layer 5 or 6 directly; they must pass data down through the rigid chain.
* **In TCP/IP,** the Application layer is a catch-all. If you are writing a web application, your code handles the data (OSI Layer 7), handles the JSON serialization (OSI Layer 6), and tracks the login session (OSI Layer 5) all inside the same program.

### 3. Connection Philosophy

* **OSI model** supports both connectionless and connection-oriented communication at the Network layer.
* **TCP/IP model** assumes a connectionless Network layer (IP packets are just thrown into the internet, and routers try their best to deliver them). It shifts the responsibility of a reliable connection entirely up to the Transport layer (TCP).

## Which one should you use?

* Use the **OSI model** when you are **troubleshooting or interviewing**. (e.g., *"We have a Layer 3 issue"* means check the router or IP configurations; *"We have a Layer 1 issue"* means check if the cable is plugged in).
* Use the **TCP/IP model** when you are **building software or configuring production servers**, as it maps directly to how the actual protocols and operating system kernels interact.
