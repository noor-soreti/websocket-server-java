# Overview
This is a basic WebSocket server written in Java. I decided to take on this task to deepen my understanding of how the WebSocket protocol works including the handshake, message framing and maintaining persistent connections.  

# Background
A WebSocket is a communication protocol that provides full-duplex channels over a single TCP connection to establish a persistent connection between a client and server. Once a connection is established, both client and server can send messages to each other at any time, making it efficient for real-time applications.

Implementing a basic WebSocket server requires a few steps. As you see below, I've outlined the steps we need to take in order to get this server working. 

1. WebSocket Opening Handshake
    * Listen for Connection: First, a server must listen for incoming socket connections from a client using standard TCP socket. As a result of Java's incredible language support, knowledge of TCP sockets is not required, we can simply use the ServerSocket class.
    * Client Handshake Request: A client sends request to establish a WebSocket connection to the server in the form of a standard HTTP request with headers. In order for the client to connect, the server must know how to interpret the request.
      ```
      // request from client (HTTP version MUST be >=1.1 and method must be GET)
      GET /chat HTTP/1.1
      Host: example.com:8000
      Upgrade: websocket
      Connection: Upgrade
      Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
      Sec-WebSocket-Version: 13
      ```
    * Server Handshake Response: Once a server receives a request it will send back a response that includes `Sec-WebSocket-Accept` header. This header is used to indicate that the server is willing to upgrade its connection from HTTP to WebSocket.
      ```
      // response from server 
      HTTP/1.1 101 Switching Protocols
      Upgrade: websocket
      Connection: Upgrade
      Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
      ```
    * Keep Track of Clients: The server must keep track of clients' sockets to avoid continuously handshaking with clients who have already completed the handshake since the same IP address can try to connect multiple times. If this happens, the server can deny the connection.
2. Exchanging Data Frames
    * After establishing a connection, we need to extract information from data frames (units that encapsulate packets of data for communication over a network). A WebSocket data frame includes the following components:
        * FIN
        * RSV1, RSV2, RSV
        * Opcode
        * Mask
        * Payload length
        * Masking key
        * Payload data
    * Decode Payload Length: to get the payload length, we need to follow these steps:
      1. Read bits 9-15 (inclusive) and interpret that as an unsigned integer. If it's 125 or less, then that's the length; you're done. If it's 126, go to step 2. If it's 127, go to step 3.
      2. Read the next 16 bits and interpret those as an unsigned integer. You're done.
      3. Read the next 64 bits and interpret those as an unsigned integer. (The most significant bit must be 0.) You're done.
    * Reading and Unmasking the Data: read the next 4 octets (32 bits) in the series to retrieve the masking key. Once payload length and masking key are decoded, you can read that number of bytes from the network socket.
    * Message Fragmentation: This step involves splitting messages into multiple frames, controlled by the FIN and opcode fields.
3. WebSocket Heartbeat: 
4. Close Connection


[//]: # (IGNORE &#40;SHR&#41;  * The response will include `Sec-WebSocket-Accept` header which is derived from client request `Sec-WebSocket-key` header.  )