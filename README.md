# MAAS Bare Metal Provisioning with Ubuntu 24.04 LTS

This repository demonstrates the use of MAAS (Metal as a Service) for bare metal provisioning on virtual machines. The goal of this project is to show how MAAS can be set up and used to automate the provisioning of physical machines or VMs, making them ready for deployment with minimal manual intervention.

The project is built on **Ubuntu 24.04 LTS** and uses the **MAAS** framework to automate the setup and management of system provisioning.

## Objective
To provision virtual machines (VMs) via MAAS, demonstrating the use of automation for hardware management, OS installation, and system configuration.

## Technologies
- **Ubuntu 24.04 LTS** - The OS used for both the MAAS server and the provisioned nodes
- **MAAS** (Metal as a Service) - The tool used for automating hardware provisioning
- **VirtualBox** or **VMware** - Virtualization tools to create the VMs for testing
- **Cloud-init** - Automates the initial setup of the machines
- **PXE Booting** - To kick-start the provisioning process via network boot

MAAS (Metal as a Service): A tool to manage and automate the deployment of operating systems on physical servers, allowing you to treat them like cloud instances. It simplifies provisioning, management, and monitoring of bare-metal hardware through a web-based UI, offering a seamless way to manage servers at scale.

## Prerequisites
1. **Ubuntu 24.04 LTS** installed on a physical or virtual machine.
2. **MAAS** installed and configured.
3. A virtualization tool (e.g., **VirtualBox** or **VMware**).
4. Basic networking setup to allow PXE booting.

## Setting up the MAAS Server
1. **Install MAAS**:  
   ```bash
   sudo apt update  
   sudo apt install maas
   
2. **Install PostgreSQL**:  
   ```bash
   sudo apt install postgresql postgresql-contrib -y
3. **Create PostgreSQL user**:  
   ```bash
   CREATE USER maas WITH PASSWORD 'maaspass';
   CREATE DATABASE maasdb WITH OWNER maas;
   \q
4. **Then initialize MAAS with:**:  
   ```bash
   sudo maas init region+rack --database-uri "postgres://maas:maaspass@localhost/maasdb"
5. **Check if MAAS was initialized properly**:  
   ```bash
   sudo maas status

You should now see services like:
regiond: running
rackd: running

6. **Create an admin user**:  
   ```bash
   sudo maas createadmin
   Username: testadmin
   Password: <choose a password>
   Email: <optional>

How to access MAAS service? <br>
The MAAS snap does not support snap set for region.http_addr because it doesn’t have a configure hook. <br>
That means you cannot change the binding via snap configuration. <br>
In snap-based MAAS, the web UI (region controller) is hard-coded to bind to localhost (127.0.0.1) for security. <br>
This is why your browser cannot access http://192.168.X.X:5240/MAAS from another machine. <br>


Use SSH port forwarding (quickest, safe) <br>
If you only need to access MAAS from your PC:
```bash
ssh -L 5240:127.0.0.1:5240 user@192.168.X.X
```
Then open in your local browser and access MAAS GUI: <br>
http://127.0.0.1:5240/MAAS
