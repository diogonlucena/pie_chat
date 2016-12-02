# PieChat
*Original Authors: Luciano Gomes &  Diogo Lucena*<br/>
This is a Sockets project for CES-35 (Networks) subject from ITA - Aeronautical Technology Institute (www.ita.br)

## 0. Introduction
This work is meant as a study and introduction to the use of sockets and low level network management in python. The use of the TCP/IP communication protocol will be implemented and studied through a simple chat application with threaded messaging. The main goal is to be able to estabilish a connection and build on top of the protocol the messaging, uploading and downloading small messaging packets in a distributed manner. The work herein is based on three tutorials from [BogoToBogo](http://www.bogotobogo.com/), more especifically:
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
  5. **socket.recv(bufsize[, flags])**: Receive data from the socket. The return value is a bytes object representing the data received. The maximum amount of data to be received at once is specified by bufsize. See the Unix manual page recv(2) for the meaning of the optional argument flags; it defaults to zero.
  6. **socket.send(bytes[, flags])**: Send data to the socket. The socket must be connected to a remote socket. Returns   number of bytes sent. Applications are responsible for checking that all data has been sent; if only some of the data   transmitted, the application needs to attempt delivery of the remaining data.
  7.**socket.close()**: Mark the socket closed. all future operations on the socket object will fail. The remote end w  receive no more data (after queued data is flushed). Sockets are automatically closed when they are garbage-collected,   it is recommended to close() them explicitly.

## 2. Program Structure
As with most chats, there the structure is of a main server that centralizes and broadcasts messages to the clients. The clients can connect to the server through a port that stays open for that purpose. In this sense, the server is like a middle man among clients.

### 2.1. Socket Handling
The server handles the sockets in a non-blocking mode using select.select():
```python
ready_to_read, ready_to_write, in_error = \
               select.select(
                  potential_readers,
                  potential_writers,
                  potential_errs,
                  timeout)
```
select() is a blocking call, but we set it's timeout to 0 so it never actually blocks. The select() function monitors all the client sockets and the server socket for readable activity. If any of the client socket is readable then it means that one of the chat client has send a message.

### 2.2. Server code
In the configurations, we set the HOST to 0.0.0.0 to allow connection on any interface. The following is the server config code:
```python
HOST = '0.0.0.0' 
SOCKET_LIST = []
RECV_BUFFER = 4096 
PORT = 9009
```
Whenever any message is receive, it is broadcasted to all other clients:
```python
# broadcast chat messages to all connected clients
def broadcast (server_socket, sock, message):
    for socket in SOCKET_LIST:
        # send the message only to peer
        if socket != server_socket and socket != sock :
            try :
                socket.send(message)
            except :
                # broken socket connection
                socket.close()
                # broken socket, remove it
                if socket in SOCKET_LIST:
                    SOCKET_LIST.remove(socket)
```
### 2.3. Client code
The client code does either listen to incoming messages from the server or check user input. If the user types in a message then send it to the server.
```python
for sock in ready_to_read:             
    if sock == s:
        # incoming message from remote server, s
        data = sock.recv(4096)
        if not data :
            print('\nDisconnected from chat server')
            sys.exit()
        else :
            #print data
            sys.stdout.write(data.decode('ascii'))
            sys.stdout.write('[Me] '); sys.stdout.flush()     
    
    else :
        # user entered a message
        msg = sys.stdin.readline()
        s.send(("["+username.upper()+"] >> "+msg).encode('ascii'))
        sys.stdout.write('[Me] '); sys.stdout.flush() 
```
The select() method is used to handle both messages from the stdin and the server.

## 3. Conclusion
It was possible to better understand how to implement and manage messaging through sockets with python. As possible improvements, there are several ideas:
 1. Username and password setting
 2. Mentiniong users in chat
 3. Groups
 4. Sending commands through the chat
A distributed messaging and chat service with multiple server nodes would also result in more effort in terms of distributed computing.
