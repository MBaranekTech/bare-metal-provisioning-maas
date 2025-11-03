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

```bash
Check if is binded to 0.0.0.0:5240  0.0.0.0:* 

sudo maas config 
Mode: region+rack
Settings:
maas_url=http://192.168.88.208:5240/MAAS
database_host=localhost
database_port=None
database_name=maasdb
database_user=maas
database_pass=(hidden)
```
7. **Install libvirt, qemu, virt-manager, and some supporting packages on Linux Client side**:  
```bash
sudo apt install -y libvirt-daemon-system libvirt-clients qemu-kvm virt-manager bridge-utils
 
Explanation:
libvirt-daemon-system: System-wide libvirt service.
libvirt-clients: Command-line tools (virsh, etc.).
qemu-kvm: Hypervisor for virtualization.
virt-manager: GUI for managing VMs.
bridge-utils: For network bridging if needed.
```
8.**Start and enable libvirt**:
```bash
Enable the libvirt daemon to start at boot:
sudo systemctl enable --now libvirtd

Check status:
sudo systemctl status libvirtd
```
9.**Add your user to the libvirt group**:
```bash
This allows you to manage VMs without sudo:

sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
```
Check if KVM is available:
```
virsh list --all
```
10.**Create VM**:
```bash
virt-install \
  --name maas-test1 \
  --ram 4096 --vcpus 2 \
  --disk size=10,bus=virtio \
  --os-variant ubuntu24.04 \
  --network bridge=br0,model=virtio \
  --boot hd,menu=on \
  --pxe \
  --graphics vnc,listen=0.0.0.0
  
This allows PXE for commissioning/deployment, but after OS installation, it will boot from disk first. 
```
