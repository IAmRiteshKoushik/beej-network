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
### .2 Port Numbers
### .3 Byte Orders (Serialization vs Deserialization)
Basically, numbers are converted to Network Byte Order when they go out into 
the wire and convert them back into Host Byte Order as they come off the wire.
1. `htons` -> Host to network "short"
2. `htons` -> Host to network "long"
3. `htons` -> Network to host "short"
4. `ntohl` -> Network to host "long"


### .4 Structs
