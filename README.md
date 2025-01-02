
# Background
A WebSocket is a communication protocol that provides full-duplex channels over a single TCP connection to establish a persistent connection between a client and server. Once a connection is established, both client and server can send messages to each other at any time, making it efficient for real-time applications.

Building a WebSocket server requires a few steps:
1. WebSocket Opening Handshake
    * (Start) First, a server must listen for incoming socket connections from a client using standard TCP socket (As a result of Java's incredible language support, knowledge of TCP sockets is not required, we can simply use the ServerSocket class)
    * (Client Handshake Request) A client sends request to establish a WebSocket connection to the server in the form of a standard HTTP request with headers. In order for the client to connect, the server must know how to interpret the request.
      ```
      // request from client (HTTP version MUST be >=1.1 and method must be GET)
      GET /chat HTTP/1.1
      Host: example.com:8000
      Upgrade: websocket
      Connection: Upgrade
      Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
      Sec-WebSocket-Version: 13
      ```
    * (Server Handshake Response) Once a server receives a request it will send back a response indicating that the protocol will change from HTTP to WebSocket.
      ```
      // response from server 
      HTTP/1.1 101 Switching Protocols
      Upgrade: websocket
      Connection: Upgrade
      Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
      ```
    * (Keep Track of Clients) The server must keep track of clients' sockets to avoid continuously handshaking with clients who have already completed the handshake since the same IP address can try to connect multiple times. If this happens, the server can deny the connection.
2. Exchanging Data Frames
    *
3. WebSocket Heartbeat
4. Close Connection


[//]: # (IGNORE &#40;SHR&#41;  * The response will include `Sec-WebSocket-Accept` header which is derived from client request `Sec-WebSocket-key` header.  )