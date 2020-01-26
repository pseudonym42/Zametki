# Port vs Socket vs Websocket

---

A Socket is a data I/O mechanism, this means it is just a point of communication between two entities (those entities could be anything - two applications, two physically separate devices, User & Kernel space within an OS, etc)

A Port is used for network communication. It allows to differentiate one application from another e.g. machine on the same IP address could have different applications listening on different ports.

A socket (unlike port) is not directly related to the TCP/IP protocol family, it can be used with any protocol your system supports.
Ports on the other hand are only used by certain protocols of the TCP/IP protocol stack have, e.g. TCP (HTTP/HTTPS, SMTP) or UDP. Some other protocols do not use ports e.g. ICMP (used by TraceRoute)

Sockets are used for both local and network communication: within TCP/IP context (network communication) sockets are called websockets and normally used for communicating between two applications running on different computers. Outside TCP/IP context they normally called unix sockets and used for interprocess communication on a single computer.

---

If we consider usage of sockets within TCP/IP context (websockets), then they are obtained by the triple:

`{protocol, local-ip-address, local-process-port}`

For example:

`{TCP, 174.0.0.5, 12345}`

So, let’s say a local client attempts to connect to remote server via HTTPS. When client creates a connection a socket gets created on both client and the server, thus the connection (i.e. socket to socket) is identified as:

`{protocol, local-ip, local-process-port, remote-ip, remote-process-port}`

so in our case it will be:

`{TCP, 174.0.0.5, 12345, 174.1.1.15, 443}`

So with HTTP servers - server creates a new socket for each new connection, i.e. on the server side each new socket is a new client.

In IP networking, port numbers can theoretically range from 0 to 65535

