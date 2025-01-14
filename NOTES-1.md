# Lesson 1 : What is a Socket ?

When a Unix program tries to do any kind of IO, they try to do so by reading 
or writing to a file descriptor. A file descriptor is simply an integer 
associated with an open file. But that file can be 
1. A network connection
2. A FIFO
3. A Pipe
4. A terminal
5. A real on-the-disk file
6. Anything else ...

Therefore communicating with anything would require the use of file-descriptors.

- `socket()` system routine is how you get access to the file descriptors for 
network communication. It returns a socket descriptor and you communicate 
through it using `send()` and `recv()` socket calls.
- As it is a file-descriptor, you can use normal `read()` and `write()` calls 
to communicate through a socket but `send()` and `recv()` offer much more
fine-grained control over data transmission.

> Sockets are of many kinds -> Internet Sockets, Unix Sockets, X.25 Sockets etc.

### Types of Internet Sockets
1. Stream sockets `SOCK_STREAM`
2. Datagram sockets `SOCK_DGRAM` (connectionless sockets)

Stream sockets are used almost everywhere. From `telnet`, `ssh` to `HTTP` in 
the browsers. They use the "TCP" protocol to make sure that your data 
comes sequentially and error-free. It is used in conjunction with IP which is 
responsible for Internet Routing and not for data integrity.

Datagram Sockets may or may not arrive. They rely on `UDP` instead of `TCP`.
You do not have to maintain an open-connection like stream-sockets. They are 
usually seen in :
1. TFTP (trivial file transfer protocol)
2. DHCPCD (dynamic host configuration protocol client daemon)
3. Multiplayer games
4. Streaming audio and video

Due to their unreliability but usefulness, we have protocols implemented on 
top of `UDP`. For example: tftp protocol says that for each packet that gets 
sent, the recipient has to send back a packet that says "ACK". If the sender 
of the original packet does not get a reply within 5 seconds then the packet 
is re-transmitted until an "ACK" is received. This is the acknowledgement 
procedure that is very useful when implementing reliable `SOCK_DGRM` apps.

### OSI (Open-Systems Interconnection) Model and Encapsulation
- Application Layer
- Presentation Layer
- Session Layer
- Transport Layer
- Network Layer
- Data Link Layer
- Physical Layer

For a more Unix-friendly view :
- Application Layer (`telnet`, `ftp`)
- Host-to-Host Transport Layer (`TCP`, `UDP`)
- Internet Layer (`IP and routing`)
- Network Access Layer (`Ethernet, wi-fi`)

When a packet is born, the packet is wrapped in a header (and sometimes a footer)
by the first protocol(TFTP) and then the whole thing is encapsulated by another 
protocol (say UDP) and then by the next (IP) and then final protocol (physical
layer). When another computer receives the packet, the hardware strips away the 
physical / ethernet header, the kernel strips away the IP, UDP headers and 
the TFTP receiver strips away the TFTP header. 

Finally you have the data. 
