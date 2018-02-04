# sensornet

SensorNet - An IoT project that connects ESP8266 based sensors through a NodeJS server which forwards sensor data to a database. The web client received notifications when sensor status or data is updated.

## Submodules

This project currently consists of 3 submodules - 

* Sensors - [esp8266-dht-udp](<https://github.com/jxmot/esp8266-dht-udp>)
* A NodeJS server - [node-dht-udp](<https://github.com/jxmot/node-dht-udp>)
* Client - [client-dht-udp](<https://github.com/jxmot/client-dht-udp>)

Each module repository contains a README. Please note that this project is a *work in progress* and is subject to changes and additions.

**NOTE:** The repo links above point to the original submodule repositories. They *may* be different from what is pointed to within this repository. I will update this repository with the submodules changes when they hit milestones.

## Overview

I had been tinkering with using an ESP8266 and a temperature/humidity sensor to record environmental data within my home. I hadn't really moved forward with it until near the end of December 2017\. And what really prompted me to move forward with the project was the brutally cold temperatures we experienced in Chicago. I was concerned about water pipes freezing, or my furnace breaking down. So the completion of this project (_sensors, server, database, and web client_) became **very** important. Fortunately no pipes froze, and my furnace continued working! I was luckier than some of my neighbors!

### Components & Technologies :

*   **Sensor Devices :**
    *   ESP-01S
    *   DHT22
    *   WiFi/UDPThe sensor devices communicate a JSON packet via UDP over WiFi to the server. Status messages are broadcast and data messages are targeted at the server. The status message broadcast will allow future devices on the network to monitor and manage the status of the sensor devices.

*   **Server :**
    *   Node.js
    *   UDP
    *   Socket.io
    *   MySQL DatabaseThe Node.js application listens for UDP packets from the sensors. Before writing the contents to the database a _time stamp_ is added to the packet. The time stamp is stored as an _epoch_ value and used as an index. After a new row (_status or data_) has been written to the database the contents are broadcast to all connected web clients via Socket.io.

*   **Web Clients :**
    *   HTML/CSS
    *   JavaScript/JQuery
    *   Socket.io
    *   C3.js - A D3.js based chart library
    *   Google Gauges - an example of my work can be found [here](http://bit.ly/gh-client-dht-udp "A repository on GitHub, opens a new tab.")This page is a web client. I developed two different clients where one uses _Google Gauges_ and [this one](<http://webexperiment.info/portfolio/sensornet/>) which uses [C3.js](http://c3js.org/ "C3.js website"). However both clients are _configurable_ and utilize Socket.io for communication with the server. However this client displays more information such as status messages, and the time & date as passed along in the messages.

### Features :

*   **Data Management** - The total number of rows in the status and data tables is managed. This was one my requirements to keep the tables from growing to an unmanageable size. This feature is implemented as a configurable timer that removes rows based on _time_.
*   **Configuration** - The sensor devices, server, and clients are configurable in a number of aspects. For example, the sensor devices can have their reporting interval configured and the server is configurable in regards to its network connection and database type.
*   **Server Run-Time Event Logging** - The server uses a log file module to record internal events to a text file where each event is time stamped. Configuration settings can be used to manage the log file name & extenstion, maximum file size, and other aspects.
*   **Sensor Device Error Detection & Automatic Recovery** - The sensor devices can detect start up and run time errors and report them via UDP multi-cast. And if possible the devices can attempt to recover from some errors. All status messages are broadcasted and saved in the database by the server, and forwarded to the clients.
*   **Immediate Client Updates** - Web clients receive updates via Socket.io. The status and data updates are broadcast to all clients, and are received nearly immediately. Since the updates are transmitted when the database is updated there was an issue where clients would have to wait for the next update of sensor before it could be displayed. The work-around was to modify the server to _remember_ the last update for the sensors and then transmit their status & data when a client connects.
*   **Low Cost of Implementation** - I was able to keep the cost of the sensor devices under $20. You might be able to get the cost even lower if the parts are sourced overseas.



