
# Silicon to Systems: A Full-Stack Networking Syllabus

**Objective:** Transition from Windows-centric development to a Linux-native flow, mastering everything from raw byte manipulation to distributed cloud architecture.

---

## Phase 1: Environment & The "Hello World" of Networking
**Goal:** Establish a Linux-native development flow and establish basic socket communication.

### The Setup
* **Environment:** Set up WSL2 (Ubuntu) or a dedicated Linux partition.
* **Tools:** VS Code with Remote Development extension, `gcc`, `gdb`, `rustup`, and `wireshark`.

### Lesson 1: The "Everything is a File" Philosophy
* **Concept:** In Linux, talking to hardware or remote clients uses a File Descriptor (fd). Understand standard fds: 0 (stdin), 1 (stdout), and 2 (stderr).
* **Task:** Write a C program using `write()` and `read()` syscalls instead of `printf()` or `scanf()`. Open a `.txt` file with `open()`, write a string, and read it back.

### Lesson 2: The Passive Socket (The "Ear")
* **Concept:** Lifecycle of a server-side socket: `socket()` -> `bind()` -> `listen()`.
* **Task:** Write a program executing these calls. Use `netstat -tuln` or `ss -tuln` to verify the process is listening on port 8080. Learn `struct sockaddr_in` and `htons()`.

### Lesson 3: The Handshake & The New Socket
* **Concept:** The "Listening Socket" acts as a receptionist; `accept()` creates a new "Worker Socket" for the actual conversation.
* **Task:** Implement `accept()`. When a client connects (via `nc`), print the client's IP and port, then close the connection.

### Lesson 4: The Echo Loop (Iterative Server)
* **Concept:** The "Read-Modify-Write" cycle.
* **Task:** Create a `while(1)` loop that `accept()`s, reads a string, writes it back, and closes the client fd.
* **Limitation:** Observe how one hanging client blocks the entire server.

### Lesson 5: Concurrency (Forking or Threading)
* **Concept:** Using `fork()` to clone the process so the parent can return to `accept()` while the child handles the client.
* **Task:** Implement `fork()`. Manage "zombie processes" using `waitpid`.


## Phase 1 Resources

### Linux Manual Pages (Syscalls)
* [read(2)](https://man7.org/linux/man-pages/man2/read.2.html) & [write(2)](https://man7.org/linux/man-pages/man2/write.2.html) - The foundation of I/O.
* [socket(2)](https://man7.org/linux/man-pages/man2/socket.2.html) - Creating the endpoint.
* [bind(2)](https://man7.org/linux/man-pages/man2/bind.2.html) - Assigning an address/port.
* [listen(2)](https://man7.org/linux/man-pages/man2/listen.2.html) - Preparing to accept connections.
* [accept(2)](https://man7.org/linux/man-pages/man2/accept.2.html) - Retrieving a connection from the queue.
* [fork(2)](https://man7.org/linux/man-pages/man2/fork.2.html) - Process creation for concurrency.

### Networking Fundamentals
* [Beej's Guide to Network Programming](https://beej.us/guide/bgnet/) - The "Gold Standard" for learning C sockets.
* [struct sockaddr_in Documentation](https://man7.org/linux/man-pages/man7/ip.7.html) - Detailed breakdown of IP socket addresses.
* [Byte Order (htons/ntohs)](https://man7.org/linux/man-pages/man3/byteorder.3.html) - Converting between Host and Network byte orders.

### Development Tools
* [VS Code Remote Development over WSL](https://code.visualstudio.com/docs/remote/wsl) - Official setup guide.
* [GDB Tutorial](https://www.tutorialspoint.com/gnu_debugger/index.htm) - Basics of debugging C programs in the terminal.

---
## Phase 2: Mastering the Byte (Data Marshalling)
**Goal:** Understand binary data transmission and the "Endian" problem.

* **Task:** Rewrite the Echo server in Rust using `std::net`. Create a custom binary protocol header: `[Type: 1 byte][Length: 2 bytes][Payload: N bytes]`.
* **Key Concepts:** Network Byte Order (Big Endian) vs. Host Byte Order, Struct padding, and manual serialization.

---

## Phase 3: The "L2/L3" Deep Dive (Packet Processing)
**Goal:** Move away from the OS-provided stack to look at raw frames.

* **Task:** Use Raw Sockets (`AF_PACKET`) to sniff traffic. Manually parse Ethernet frames, IP headers, and UDP headers.
* **Key Concepts:** OSI Model (L2 vs L3), MAC vs. IP addresses, and Checksums. Learn why ARP is necessary for frame delivery.

---

## Phase 4: Reliability & Flow Control (Building "Mini-TCP")
**Goal:** Understand TCP complexity by building a reliable layer over UDP.

* **Task:** Build a protocol on UDP that guarantees delivery. If a packet is lost, it must be re-sent.
* **Key Concepts:** Sequence numbers, Acknowledgments (ACKs), Timeouts, and Sliding Windows.
* **Significance:** Understand why RoCE/RDMA requires "Lossless Fabric" to avoid expensive software reliability.

---

## Phase 5: Horizontal Scaling & Load Balancing
**Goal:** Move to multi-node architecture using Raspberry Pis.

* **Task:** Deploy the server on 3 Raspberry Pis. Build a Load Balancer in Rust on a PC to sit in front of them.
* **Key Concepts:** Round Robin, Least Connections, and Heartbeat health checks.

---

## Phase 6: The Distributed State (Databases & Consistency)
**Goal:** Add a centralized "Brain" to the system.

* **Task:** Implement a simple Key-Value store (CRUD) stored across the Pis.
* **Key Concepts:** CAP Theorem, Synchronous vs. Asynchronous replication, and Connection Pooling.

---

## Phase 7: Advanced Cloud Networking (The "AWS" Layer)
**Goal:** Connect scratch-built knowledge to EC2 and Nitro architecture.

* **Task:** Research and implement a Geneve or VXLAN encapsulation wrapper for packets.
* **Key Concepts:** Tunneling, Overlay vs. Underlay networks, and MTU overhead.
* **Significance:** This mirrors how Geneve wraps customer packets to move through the physical Nitro fabric.