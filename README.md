# Notes from Beej's Guide to Networking

> [!NOTE]
> All important RFC document links can be found in `RFC.md` within this repo.

This repository contains all the source code along with notes written 
down while studying `Beej's Guide to Networking`. A chapter wise 
breakdown is available below for navigating through the source code 
as all the files are kept in the root-directory for easy access.

## IP Addresses, `struct`s and Data Munging
`127.0.0.1` is also called as loop-back address. In IPv6 terms 
`0000:0000:0000:0000:0000:0000:0000:0001` which can be abbreviated to `::1` is 
the loop back address.

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
