---
title: FTP Enumeration
date: 2024-03-13 08:25:41 AM
categories: [RedTeam, Enumeration]
tags: [redteam]     # TAG names should always be lowercase
authors: [0]
img_path: /assets/img/services_enumerations/ftp/
---

## FTP 
File Transfer Protocol (FTP) is, as the name suggests , a protocol used to allow remote transfer of files over a network. It uses a client-server model to do this, and- as we'll come on to later- relays commands and data in a very efficient way.

### How does FTP work?
A typical FTP session operates using two channels:
- a command (sometimes called the control) channel
- a data channel.
![ftp archtiecture](ftp_archtiecture.png)


As their names imply, the command channel is used for transmitting commands as well as replies to those commands, while the data channel is used for transferring data.

FTP operates using a client-server protocol. The client initiates a connection with the server, the server validates whatever login credentials are provided and then opens the session.

While the session is open, the client may execute FTP commands on the server.

### Active vs Passive

The FTP server may support either Active or Passive connections, or both. 

- In an Active FTP connection, the client opens a port and listens. The server is required to actively connect to it. 
- In a Passive FTP connection, the server opens a port and listens (passively) and the client connects to it. 

This separation of command information and data into separate channels is a way of being able to send commands to the server without having to wait for the current data transfer to finish. If both channels were interlinked, you could only enter commands in between data transfers, which wouldn't be efficient for either large file transfers, or slow internet connections.

### Enumeration 
[Hydra](../hydra/)