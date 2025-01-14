## Lesson 2 : IP Addresses, `struct`s and Data Munging
`127.0.0.1` is also called as loop-back address. In IPv6 terms 
`0000:0000:0000:0000:0000:0000:0000:0001` which can be abbreviated to `::1` is 
the loop back address.

> [!NOTE]
> Endianess: Given a multibyte piece of data, how should it be represented 
> in memory. For example -> `0xa01183d1`    
> Should it be `::` a0 | 11 | 83 | d1   (big endian)    
> Or `::` d1 | 83 | 11 | a0  (little endian)    
> Basically, the most significant byte is the one that carries the most weight 
> place-value wise in the number. Humans write numbers in big-endian. Also, 
> when machines communicate over the internet, they agree to the internet 
standard which specifies that all network headers must be "big endian".

> [!WARNING]
> Endianness is about byte-ordering and not bit-ordering. Endianess only 
applies to multi-byte values meaning that if there is only byte there is no 
need to talk about Endianess.

> [!TIP]
> Do check whether your hardware is implementing big endian or little endian 
because when using a debugging tool to inspect memory, this can throw you off 
because as a programmer you usually do not care about endian-ness in your
program.

### .1 Subnets
Sometimes it is convenient to declare that we are host 12 on network 192.0.2.0
which basically means we are pointing to IP address 192.0.2.12. There used to 
be classes of subnets, where the first one, two or three bytes of the address
was the network part. 
- net.xxx.xxx.xxx (Class A network) 16 million hosts
- net.net.xxx.xxx (Class B network) 65,534 hosts
- net.net.net.xxx (Class C network) 256 hosts

The network portion of the IP address is described by something called _netmask_
which you bitwise-AND with the IP address to get the network number out of it.


### .2 Port Numbers
Besides the IP address, there is another address that is used by TCP 
(stream sockets) and, coincidentally, by UDP (datagram sockets). It is the PORT
number. It's a 16-bit number that's like the local address from connection. 

> Analogy `IP-address : PORT-number :: street-address : room-number`

If you need your computer to handle incoming mail AND web-services, both coming 
on the same IP-address can cause chaos hence we receive them on different ports.
- HTTP (80)
- HTTPS (443)
- SSH (20)
- TELNET (23)
- SMTP (25)
- DOOM game (666)

> [!NOTE]
> Ports under 1024 are often considered special and require OS priviledges to 
access.

### .3 Byte Orders (Serialization vs Deserialization)
Basically, numbers are converted to Network Byte Order when they go out into 
the wire and convert them back into Host Byte Order as they come off the wire.
1. `htons` -> Host to network "short"
2. `htonl` -> Host to network "long"
3. `htons` -> Network to host "short"
4. `ntohl` -> Network to host "long"


### .4 Structs
Datatypes used by the sockets interface
1. Socket descriptor
```c
int
```
2. `struct addrinfo`
It is used to prep the socket address structures for subsequent use. It's used
in hostname lookups, service name lookups.
```c
struct addrinfo {
  int             ai_flags;       // AI_PASSIVE, AI_CANONNAME, etc.
  int             ai_family;      // AF_INET, AF_INET6, AF_UNSPEC
  int             ai_socktype;    // SOCK_STREAM, SOCK_DGRAM  
  int             ai_protocol;    // use 0 for "any"
  size_t          ai_addrlen;     // size of ai_addr in bytes
  struct sockaddr *ai_addr;       // struct sockaddr_in or _in6
  char            *ai_canonname;  // full canonical hostname

  struct addrinfo *ai_next;       // LL (next node)
}
```
You can force it to use IPv4, IPv6 in the `ai_family` or leave it as `AF_UNSPEC`
too use whatever. Your code can be agnostic of the IP version.

`ai_next` points to the next element - there could be several results to choose
from.
`ai_`

3. Socket Address Info
```c
struct sockaddr {
  unsigned short sa_family      // address family, AF_xxxx
  char           sa_data[14];   // 14 bytes of protocol address
}
```
sa_data contains the destination address and the port number for the socket. To
deal with `struct sockaddr`, we have a parallel structure called 
`struct sockaddr_in` to be used with IPv4.
- A pointer to a `struct sockaddr_in` can be cast to a pointer to a 
`struct sockadrr`  and vice-versa. So even though `connect()` wants a 
`struct sockaddr*`, you can still use a `struct sockaddr_in` and cast it in the 
end.

```c
// IPv4 only
struct sockaddr_in {
  short int           sin_family; // Address family, AF_INET
  unsigned short int  sin_port;   // Port number
  struct in_addr      sin_addr;   // Internet address
  unsigned char       sin_zero[8] // Same size as struct sockaddr
};

// Internet address
struct in_addr {
  uint32_t s_addr;    // a 32-bit int (4 bytes)
};
```
For handling IPv6, we have similar structures
```c
// IPv6 
struct sockaddr_in6 {
  u_int16_t sin6_family;      // Address family, AF_INET6
  u_int16_t sin6_port;        // Port number, Network Byte Order
  u_int32_t sin6_flowinfo;    // Ipv6 flow information
  struct in6_addr sin6_addr;  // IPv6 address 
  u_int32_t sin6_scope_id;    // Scope ID
  
};

struct in6_addr {
  unsigned char s6_addr[16]; // IPv6 address
}
```

### .4 IP Addresses, Part Deux
There are a bunch of functions that allow you to manipulate IP addresses. No 
need to figure them out by hand and stuff them in a `long` with the `<<` 
operator.

First, let's say you have a `struct sockaddr_in ina`, and you have an IP address
"10.12.11.57" that you want to store into it. Use `inet_pton` which converts
IP-address into `struct in_addr` or `struct in6_addr` based on `AF_INET` or 
`AF_INET6`. 

> [!NOTE]
> pton :: Presentation to network / print to network

IPv6 has a port number just list IPv4.

```c
struct sockaddr_storage {
  sa_family_t ss_family;  // address family

  // all this is padding, implementation specific, ignore it:
  char __ss_pad1[_SS_PAD1SIZE];
  int64_t __ss_align;
  char __ss_pad2[_SS_PAD2SIZE];
}
```
Here,  you can see the address family in the `ss_family` field-check this to 
see if it's `AF_INET` or `AF_INET6` (for IPv4 or IPv6). Then you can cast it to
`struct sockaddr_in` or  `struct sockaddr_in6`.

```c
struct sockaddr_in sa;
struct sockaddr_in6 sa6;

inet_pton(AF_INET, "10.12.110.57", &(sa.sin_addr)); // IPv4
inet_pton(AF_INET6, "2001:db8:63b3:1::3490", &(sa6.sin6_addr)); // IPv6

// inet_pton returns -1 on error, or 0 if the address is messed up. Handle 
// the error gracefully
```

Now, suppose if you have to convert them back to numbers-and-dots notation. The
function to use `inet_ntop` (ntop = network to presentation format).
```c
char ip4[INET_ADDRSTRLEN]
struct sockaddr_in sa;

inet_ntop(AF_INET, &(sa.sin_addr), ip4, INET_ADDRSTRLEN);
printf("The IPv4 address is: %s\n", ip4);

// IPv6:
char ip6[INET6_ADDRSTRLEN];
struct sockaddr_in6 sa6;
inet_ntop(AF_INET6, &(sa6.sin6_addr), ip6, INET6_ADDRSTRLEN);
printf("The address is: %s\n", ip6);
```

## NAT (Network Address Translation)
Many orgs have firewalls that hides the network from the rest of the world for 
their own protection. And often times, the firewall translates "internal" IP
addresses to "external" IP addresses using a process called Network Address 
Translation (NAT)

Two computers cannot share the same IP address. Often times computers are on a 
private network with 24 million IP addresses allocated to it. `10.x.x.x` is
one of the few reserved networks that are only to be used either on fully 
disconnected networks or on networks that are behind firewalls. The details 
of private network numbers are available in RFC 1918. Common ones :
1. `10.x.x.x`, x in (0-255) 
2. `192.168.x.x`, x in (0-255)
3. `172.y.x.x`, y in (16-31) and x in (0-255)
