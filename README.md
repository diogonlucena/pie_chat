# PieChat
*Original Authors: Luciano Gomes &  Diogo Lucena*<br/>
This is a Sockets project for CES-35 (Networks) subject from ITA - Aeronautical Technology Institute (www.ita.br)

## 0. Introduction
This work is meant as a study and introduction to the use of sockets and low level network management in python. The use of the TCP/IP communication protocol will be implemented and studied through a simple chat application with file transfering. The main goal is to be able to estabilish a connection and build on top of the protocol the messaging, uploading and downloading in a distributed manner. The work herein is based on three tutorials from [BogoToBogo](http://www.bogotobogo.com/), more especifically:
 1. [NETWORK PROGRAMMING - SERVER & CLIENT A : BASICS](http://www.bogotobogo.com/pyt python_network_programming_server_client.php)
 2. [NETWORK PROGRAMMING - SERVER & CLIENT B : FILE TRANSFER](http://www.bogotobogo.com/pyt python_network_programming_server_client_file_transfer.php)
 3. [NETWORK PROGRAMMING II - CHAT SERVER & CLIENT](http://www.bogotobogo.com/pyt python_network_programming_tcp_server_client_chat_server_chat_client_select.p)

## 1. Sockets in Python
Taken from [socket - Low-level networking interface](https://docs.python.org/3/library/socket.html):
>This module provides access to the BSD socket interface. It is available on all modern Unix systems, Windows, MacOS, and probably additional platforms. The Python interface is a straightforward transliteration of the Unix system call and library interface for sockets to Python’s object-oriented style: the socket() function returns a socket object whose methods implement the various socket system calls. Parameter types are somewhat higher-level than in the C interface: as with read() and write() operations on Python files, buffer allocation on receive operations is automatic, and buffer length is implicit on send operations.

The following is a summary of key functions that will be used and their explanation:
  1. **socket.socket()**: Create a new socket using the given address family, socket type and protocol number.
  2. **socket.bind(address)**: Bind the socket to address.
  3. **socket.listen(backlog)**: Listen for connections made to the socket. The backlog argument specifies the maxi  number of queued connections and should be at least 0; the maximum value is system-dependent (usually 5), the mini  value is forced to 0.
  4. **socket.accept()**: The return value is a pair (conn, address) where conn is a new socket object usable to send   receive data on the connection, and address is the address bound to the socket on the other end of the connection.
  At accept(), a new socket is created that is distinct from the named socket. This new socket is used solely   communication with this particular client.
  For TCP servers, the socket object used to receive connections is not the same socket used to perform subsequ  communication with the client. In particular, the accept() system call returns a new socket object that's actually u  for the connection. This allows a server to manage connections from a large number of clients simultaneously.
  5. **socket.send(bytes[, flags])**: Send data to the socket. The socket must be connected to a remote socket. Returns   number of bytes sent. Applications are responsible for checking that all data has been sent; if only some of the data   transmitted, the application needs to attempt delivery of the remaining data.
  6.**socket.close()**: Mark the socket closed. all future operations on the socket object will fail. The remote end w  receive no more data (after queued data is flushed). Sockets are automatically closed when they are garbage-collected,   it is recommended to close() them explicitly.