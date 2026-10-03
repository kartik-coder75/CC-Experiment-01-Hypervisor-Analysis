# Performance Analysis of Type-1 and Type-2 Hypervisors

## Proxmox VE vs VMware Workstation

This project demonstrates the creation, configuration, and performance analysis of virtual machines using two different types of hypervisors:

* **Type-1 Hypervisor:** Proxmox VE
* **Type-2 Hypervisor:** VMware Workstation

Both virtual machines use Ubuntu as the guest operating system and are configured with similar hardware resources. CPU performance is measured using **Sysbench** and the obtained results are recorded for comparison.

---

# Objective

The objectives of this experiment are:

1. To understand Type-1 and Type-2 hypervisors.
2. To create a virtual machine using Proxmox VE.
3. To create a virtual machine using VMware Workstation.
4. To configure both virtual machines with similar hardware resources.
5. To install Ubuntu on both virtual machines.
6. To verify CPU, memory, and disk configurations.
7. To install Sysbench.
8. To perform CPU benchmarking.
9. To record execution time, events per second, and latency.
10. To compare the performance of both virtualization environments.

---

# Common Virtual Machine Configuration

Both virtual machines are configured with approximately the same resources.

| Resource               | Configuration |
| ---------------------- | ------------- |
| Guest Operating System | Ubuntu        |
| CPU                    | 2 vCPU        |
| RAM                    | 2 GB          |
| Disk                   | 20 GB         |
| Benchmark Tool         | Sysbench      |

The network configuration differs according to the virtualization platform:

* Proxmox VE → `vmbr0`
* VMware Workstation → `NAT`

---

# Type-1 Hypervisor – Proxmox VE

## 1. Accessing Proxmox VE

Proxmox VE provides a web-based management interface.

### Steps

1. Connect the system to the network where the Proxmox VE server is accessible.
2. Open a web browser.
3. Enter:

```text
https://<PROXMOX_SERVER_IP>:8006
```

Example:

```text
https://192.168.X.X:8006
```

4. Press **Enter**.

The Proxmox VE login page will be displayed.

---

## 2. Handling the Security Warning

A browser security warning may appear because Proxmox VE can use a self-signed SSL certificate.

1. Click **Advanced**.
2. Select **Proceed to <Server IP>**.
3. The Proxmox VE login page will open.

---

## 3. Logging in to Proxmox VE

Enter the assigned login credentials.

| Parameter | Value                |
| --------- | -------------------- |
| Username  | Provided credentials |
| Password  | Provided credentials |
| Realm     | As configured        |

Click **Login**.

After successful authentication, the Proxmox VE dashboard will be displayed.

---

## 4. Understanding the Proxmox Interface

The left-side navigation panel provides access to:

* Datacenter resources
* Proxmox server node
* Virtual machines
* Storage
* Network configuration
* CPU and memory utilization

---

## 5. Creating a Virtual Machine

The Proxmox VM creation process consists of:

```text
General → OS → System → Disks → CPU → Memory → Network → Confirm
```

Select the required Proxmox node from the left-side navigation panel.

For example:

```text
Datacenter → pve
```

Then click **Create VM** from the upper-right corner.

The **Create: Virtual Machine** wizard will open.

---

## 6. Configuring General Settings

Configure the VM name and other general settings.

| Parameter | Configuration          |
| --------- | ---------------------- |
| Node      | Selected Proxmox Node  |
| VM ID     | Automatically assigned |
| Name      | Unique VM name         |

Recommended naming convention:

```text
<Name>-Type1
```

Example:

```text
CC-Experiment1-Type1
```

Click **Next**.

---

## 7. Configuring the Operating System

In the OS section:

1. Select **Use CD/DVD Disc Image File (ISO)**.
2. Select the storage location containing the ISO.
3. Select the Ubuntu ISO image.

Example:

```text
Storage: local
ISO Image: ubuntu-22.04.iso
```

Configuration:

| Parameter          | Configuration |
| ------------------ | ------------- |
| Installation Media | ISO Image     |
| Storage            | local         |
| Operating System   | Ubuntu        |

Click **Next**.

---

## 8. Configuring System Settings

Use the default settings unless otherwise specified.

| Parameter       | Configuration |
| --------------- | ------------- |
| Graphics Card   | Default       |
| Machine         | Default       |
| BIOS            | Default       |
| SCSI Controller | Default       |

Click **Next**.

---

## 9. Configuring Virtual Disk

Configure the virtual disk as follows:

| Parameter  | Configuration                |
| ---------- | ---------------------------- |
| Storage    | local-lvm / assigned storage |
| Disk Size  | 20 GB                        |
| Bus/Device | Default                      |

Click **Next**.

---

## 10. Configuring CPU

Configure:

| Parameter | Value |
| --------- | ----: |
| Sockets   |     1 |
| Cores     |     2 |

Therefore:

```text
Total vCPU = 1 × 2 = 2 vCPU
```

Click **Next**.

---

## 11. Configuring Memory

Configure:

| Parameter |    Value |
| --------- | -------: |
| Memory    | 2048 MiB |

This is approximately:

```text
2048 MiB = 2 GB RAM
```

Click **Next**.

---

## 12. Configuring Network

Configure the network interface as:

| Parameter | Configuration    |
| --------- | ---------------- |
| Bridge    | vmbr0            |
| Model     | Default / VirtIO |

The `vmbr0` bridge connects the VM to the configured network.

Click **Next**.

---

## 13. Confirming the VM Configuration

Before creating the VM, verify:

| Resource         | Configuration        |
| ---------------- | -------------------- |
| VM Name          | CC-Experiment1-Type1 |
| Operating System | Ubuntu               |
| CPU              | 2 vCPU               |
| Memory           | 2 GB                 |
| Disk             | 20 GB                |
| Network          | vmbr0                |

Click **Finish**.

The virtual machine will be created.

---

## 14. Starting the VM

1. Locate the newly created VM.
2. Select the VM.
3. Click **Start**.

The VM status should change from:

```text
Stopped
```

to:

```text
Running
```

---

## 15. Opening the VM Console

1. Select the virtual machine.
2. Select **Console**.
3. The VM display will open in the browser.

The Ubuntu installation interface will be displayed.

---

## 16. Installing Ubuntu

Complete the Ubuntu installation using the following steps:

1. Select the required language.
2. Select **Install Ubuntu**.
3. Configure the keyboard layout.
4. Select the required installation type.
5. Select the virtual disk.
6. Configure the timezone.
7. Create the Ubuntu user account.
8. Complete the installation.
9. Restart the VM.
10. Log in to Ubuntu.

---

## 17. Verifying the Virtual Machine

Open the Ubuntu terminal and execute:

```bash
hostnamectl
```

Verify:

* Hostname
* Operating system
* Kernel version
* Architecture

---

## 18. Checking CPU Configuration

Execute:

```bash
lscpu
```

Observe:

* Architecture
* CPU(s)
* CPU model
* Virtualization information

The VM should have approximately 2 virtual CPUs.

---

## 19. Checking Memory Configuration

Execute:

```bash
free -h
```

Observe:

* Total memory
* Used memory
* Free memory
* Available memory

The allocated memory should be approximately 2 GB.

---

## 20. Checking Disk Configuration

Execute:

```bash
df -h
```

Observe:

* Filesystem
* Total disk capacity
* Used disk space
* Available disk space

---

## 21. Monitoring System Resources

Execute:

```bash
top
```

Observe:

* CPU utilization
* Memory utilization
* Running processes
* Load average

Press:

```text
q
```

to exit.

---

## 22. Installing Sysbench

Update the package repository:

```bash
sudo apt update
```

Install Sysbench:

```bash
sudo apt install sysbench -y
```

Verify the installation:

```bash
sysbench --version
```

---

## 23. Running the CPU Benchmark

Execute:

```bash
sysbench cpu --cpu-max-prime=20000 run
```

Allow the benchmark to complete.

Record:

* Total execution time
* Total number of events
* Events per second
* Minimum latency
* Average latency
* Maximum latency

---

## 24. Recording Type-1 Results

Record the actual results obtained from the Sysbench output.

| Parameter              | Observation   |
| ---------------------- | ------------- |
| Hypervisor             | Proxmox VE    |
| Hypervisor Type        | Type-1        |
| Guest Operating System | Ubuntu        |
| CPU Allocation         | 2 vCPU        |
| Memory Allocation      | 2 GB          |
| Disk Allocation        | 20 GB         |
| Total Execution Time   | 9.9968        |
| Total Events           | 15877         |
| Events per Second      | 1587.47       |
| Minimum Latency        | 0.59          |
| Average Latency        | 0.63          |
| Maximum Latency        | 1.34          |

---

## 25. Monitoring Resources in Proxmox

Return to the Proxmox VE web interface.

Navigate to the VM summary.

Observe:

* CPU usage
* Memory usage
* Network traffic
* Disk usage

Record the observations for comparison with the Type-2 hypervisor.

---

## 26. Shutting Down the Type-1 VM

After completing the benchmark, shut down the VM properly.

Inside Ubuntu:

```bash
sudo poweroff
```

Alternatively, use the shutdown option available in the Proxmox VE interface.

Verify that the VM status changes to:

```text
Stopped
```

---

# Type-2 Hypervisor – VMware Workstation

## 1. Launching VMware Workstation

Open **VMware Workstation** from the installed applications.

The VMware Workstation home interface will be displayed.

Select:

```text
Create a New Virtual Machine
```

The New Virtual Machine Wizard will open.

---

## 2. Selecting Virtual Machine Configuration

The wizard provides:

* Typical (recommended)
* Custom (advanced)

Select:

```text
Typical (recommended)
```

Click **Next**.

---

## 3. Selecting Ubuntu ISO

Select:

```text
Installer disc image file (iso)
```

Click **Browse**.

Navigate to the location of the Ubuntu ISO.

Select:

```text
ubuntu-22.04.iso
```

Click **Next**.

---

## 4. Selecting Guest Operating System

If VMware automatically detects Ubuntu, verify the detected configuration.

Otherwise select:

| Parameter              | Configuration |
| ---------------------- | ------------- |
| Guest Operating System | Linux         |
| Version                | Ubuntu 64-bit |

Click **Next**.

---

## 5. Naming the Virtual Machine

Enter the VM name:

```text
CC-Experiment1-Type2
```

Select the location where the VM files should be stored.

Click **Next**.

---

## 6. Configuring Virtual Disk

Configure:

| Parameter         | Configuration                                 |
| ----------------- | --------------------------------------------- |
| Maximum Disk Size | 20 GB                                         |
| Disk Storage      | Store virtual disk as a single file / default |

Click **Next**.

The VM configuration summary will appear.

Select:

```text
Customize Hardware
```

---

## 7. Configuring Memory

Select **Memory** from the hardware settings.

Set:

```text
2048 MB
```

This is approximately:

```text
2 GB RAM
```

---

## 8. Configuring Processor

Select **Processors**.

Configure:

| Parameter                     | Value |
| ----------------------------- | ----: |
| Number of Processors          |     1 |
| Number of Cores per Processor |     2 |

Therefore:

```text
Total Virtual CPUs = 1 × 2 = 2 vCPU
```

---

## 9. Verifying Hard Disk

Select **Hard Disk**.

Verify:

```text
20 GB
```

---

## 10. Configuring Network Adapter

Select **Network Adapter**.

For this experiment, select:

```text
NAT
```

NAT allows the VM to access the network through the host system.

---

## 11. Verifying VMware Hardware Configuration

Verify:

| Resource               | Configuration |
| ---------------------- | ------------- |
| Memory                 | 2 GB          |
| CPU                    | 2 vCPU        |
| Hard Disk              | 20 GB         |
| Network                | NAT           |
| Guest Operating System | Ubuntu        |

Click **Close**.

---

## 12. Completing VM Creation

Review the complete configuration.

Click:

```text
Finish
```

The VM will appear in the VMware Workstation library.

---

## 13. Starting the VM

Select the created VM.

Click:

```text
Power on this virtual machine
```

The Ubuntu installation process will begin.

---

## 14. Installing Ubuntu

The Ubuntu installation interface will appear.

### Language

Select the required language.

Click:

```text
Install Ubuntu
```

### Keyboard Layout

Select the appropriate keyboard layout.

Click **Continue**.

### Installation Type

Select:

```text
Normal Installation
```

Click **Continue**.

### Installation Disk

Select:

```text
Erase disk and install Ubuntu
```

This operation affects only the virtual disk created for the VMware VM.

Click **Install Now**.

Confirm the disk changes if prompted.

### Time Zone

Select the appropriate geographical location and timezone.

Click **Continue**.

### User Account

Configure the Ubuntu account.

| Parameter     | Example         |
| ------------- | --------------- |
| Name          | Your Name       |
| Computer Name | cc-type2-vm     |
| Username      | Your Username   |
| Password      | Secure Password |

Click **Continue**.

Ubuntu installation will begin.

---

## 15. Restarting the VM

After installation is complete:

1. Select **Restart Now**.
2. Wait for the VM to restart.
3. Log in using the Ubuntu username and password.

---

## 16. Verifying the Virtual Machine

Open the Ubuntu terminal.

Execute:

```bash
hostnamectl
```

Verify:

* Hostname
* Operating system
* Kernel version
* Architecture

---

## 17. Checking CPU Configuration

Execute:

```bash
lscpu
```

Observe:

* Architecture
* CPU(s)
* CPU model
* Number of cores
* Virtualization type

Verify that the VM has approximately:

```text
2 Virtual CPUs
```

---

## 18. Checking Memory Configuration

Execute:

```bash
free -h
```

Observe:

* Total memory
* Used memory
* Free memory
* Available memory

Verify that the allocated memory is approximately:

```text
2 GB
```

---

## 19. Checking Disk Configuration

Execute:

```bash
df -h
```

Observe:

* Filesystem
* Total disk capacity
* Used disk space
* Available disk space

Verify the virtual disk configuration.

---

## 20. Monitoring System Resources

Execute:

```bash
top
```

Observe:

* CPU utilization
* Memory utilization
* Running processes
* Load average

Press:

```text
q
```

to exit.

---

## 21. Installing Sysbench

Update the package repository:

```bash
sudo apt update
```

Install Sysbench:

```bash
sudo apt install sysbench -y
```

Verify:

```bash
sysbench --version
```

---

## 22. Running the CPU Benchmark

Execute:

```bash
sysbench cpu --cpu-max-prime=20000 run
```

Allow the benchmark to complete.

Record:

* Total execution time
* Total number of events
* Events per second
* Minimum latency
* Average latency
* Maximum latency

---

## 23. Recording Type-2 Results

Record the actual results obtained from Sysbench.

| Parameter              | Observation        |
| ---------------------- | ------------------ |
| Hypervisor             | VMware Workstation |
| Hypervisor Type        | Type-2             |
| Guest Operating System | Ubuntu             |
| CPU Allocation         | 2 vCPU             |
| Memory Allocation      | 2 GB               |
| Disk Allocation        | 20 GB              |
| Total Execution Time   | 9.9959             |
| Total Events           | 6898               |
| Events per Second      | 689.57             |
| Minimum Latency        | 1.25               |
| Average Latency        | 1.45               |
| Maximum Latency        | 4.23               |

---

## 24. Monitoring Resources in VMware Workstation

Return to VMware Workstation.

Select the running virtual machine.

The configured hardware can be viewed through:

```text
VM → Settings
```

Verify:

* Processors
* Memory
* Hard Disk
* Network Adapter

Additional resource information can be monitored inside Ubuntu using:

```bash
top
```

or:

```bash
free -h
```

---

## 25. Shutting Down the Type-2 VM

After completing the performance analysis, shut down the VM properly.

Inside Ubuntu:

```bash
sudo poweroff
```

Alternatively, use:

```text
VM → Power → Shut Down Guest
```

Wait until the virtual machine shuts down completely.

---

# Performance Comparison

After completing both experiments, the measured results can be recorded together.

| Performance Metric   | Proxmox VE    | VMware Workstation |
| -------------------- | ------------- | ------------------ |
| Hypervisor Type      | Type-1        | Type-2             |
| Guest OS             | Ubuntu        | Ubuntu             |
| CPU                  | 2 vCPU        | 2 vCPU             |
| Memory               | 2 GB          | 2 GB               |
| Disk                 | 20 GB         | 20 GB              |
| Network              | vmbr0         | NAT                |
| Total Execution Time | 9.9968        | 9.9959             |
| Total Events         | 15877         | 6898               |
| Events per Second    | 1587.47       | 689.57             |
| Minimum Latency      | 0.59          | 1.25               |
| Average Latency      | 0.63          | 1.45               |
| Maximum Latency      | 1.34          | 4.23               |

---

# Understanding the Performance Metrics

## Total Execution Time

Total time required by Sysbench to complete the CPU benchmark.

A lower execution time means the benchmark completed in less time.

## Total Events

The total number of benchmark operations performed during the test.

## Events Per Second

The number of benchmark operations completed per second.

A higher events-per-second value indicates higher benchmark throughput.

## Latency

Latency represents the time required to complete an individual operation.

The benchmark provides:

* Minimum latency
* Average latency
* Maximum latency

---

# Commands Used

## System Information

```bash
hostnamectl
```

## CPU Information

```bash
lscpu
```

## Memory Information

```bash
free -h
```

## Disk Information

```bash
df -h
```

## Resource Monitoring

```bash
top
```

Press `q` to exit.

## Update Packages

```bash
sudo apt update
```

## Install Sysbench

```bash
sudo apt install sysbench -y
```

## Check Sysbench Version

```bash
sysbench --version
```

## Run CPU Benchmark

```bash
sysbench cpu --cpu-max-prime=20000 run
```

## Shutdown VM

```bash
sudo poweroff
```

---

# Final Comparison

The experiment creates two Ubuntu virtual machines using different hypervisor types.

### Type-1

```text
Physical Server
      ↓
Proxmox VE
      ↓
Ubuntu VM
      ↓
2 vCPU + 2 GB RAM + 20 GB Disk
      ↓
Sysbench CPU Benchmark
```

### Type-2

```text
Host Operating System
      ↓
VMware Workstation
      ↓
Ubuntu VM
      ↓
2 vCPU + 2 GB RAM + 20 GB Disk
      ↓
Sysbench CPU Benchmark
```

The same Sysbench CPU benchmark is executed in both environments. The obtained execution time, events per second, and latency values are used for the performance comparison.

The final observations should be based on the actual benchmark results obtained from the two systems.

---

# Conclusion

This experiment demonstrates the practical implementation and performance analysis of Type-1 and Type-2 hypervisors.

Proxmox VE is used as the Type-1 hypervisor, while VMware Workstation is used as the Type-2 hypervisor. Ubuntu is installed as the guest operating system in both environments.

Both virtual machines are configured with approximately:

* 2 vCPU
* 2 GB RAM
* 20 GB disk

System configuration is verified using Linux commands such as `hostnamectl`, `lscpu`, `free -h`, and `df -h`.


Sysbench is then installed and used to perform CPU benchmarking. The benchmark results provide measurements such as total execution time, total events, events per second, and latency.

These measured values can then be used to analyze the performance of the two virtualization environments under similar virtual machine configurations.

## Hypervisor Performance Visual Analysis
<img width="1977" height="1180" alt="01_execution_time" src="https://github.com/user-attachments/assets/36739b49-bfe4-42a5-b201-1c7b4ede4851" />

<img width="1977" height="1180" alt="02_total_events" src="https://github.com/user-attachments/assets/e394d3c1-eb33-45b9-ab39-8111679839e2" />

<img width="1977" height="1180" alt="03_events_per_second" src="https://github.com/user-attachments/assets/b447f53f-e784-442f-896b-cc59d2b2307e" />

<img width="1977" height="1180" alt="04_minimum_latency" src="https://github.com/user-attachments/assets/d0b1deca-e278-41dd-852a-247b3f877e59" />

<img width="1977" height="1180" alt="05_average_latency" src="https://github.com/user-attachments/assets/5d641fe6-6b5f-4129-8771-e9ca807a4c36" />

<img width="1977" height="1180" alt="06_maximum_latency" src="https://github.com/user-attachments/assets/97cd6c5f-9668-42a0-b693-1d8a60cec47f" />

<img width="1977" height="1178" alt="07_relative_difference" src="https://github.com/user-attachments/assets/ef410933-1f3f-4788-9b4b-a7bd1e2e4c36" />

---
#### Final Conclusion: 
Overall, the performance analysis shows that Type-1 (Proxmox VE) achieved higher event-processing efficiency and lower latency than Type-2 (VMware Workstation) across the tested workloads. These results highlight the performance advantage of bare-metal virtualization for this benchmark.


