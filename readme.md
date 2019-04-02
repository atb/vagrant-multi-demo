# Vagrant Multi VM Demonstration

This project demonstrates how to run an Errai application using Wildfly and Vagrant.

## Requirements

  Install Vagrant and VirtualBox in you computer.

  You also need the application [errai-demonstration-gradle](https://github.com/atb/errai-demonstration-gradle).

## Steps

### 1

Create a local folder in your computer and copy the Vagrantfile into that folder

### 2

Build the war of the errai-demonstration-gradle and copy the file into the previous folder

### 3

Execute **vagrant up**

This will create two vagrant VMs (i.e, "web" and "db"). The first run will take some time because both machines will be provisioned with several software packages.

### 4

In the host you can open the errai application using the url: http://192.168.33.10:8080/errai-demonstration-gradle/

You can also open the H2 console using the url: http://192.168.33.11:8082

For the connection string use: jdbc:h2:tcp://192.168.33.11:9092/~/test

**Attention:** Although there are 2 connected VMs, one with the errai web application and other with H2 running in server mod,e the errai application is not using the H2 server because it is configured to use a memory H2 database. You need to change this configuration...
