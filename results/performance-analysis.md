# Performance Analysis

## 1. Overview and Environment Setup

This document presents the empirical benchmark results comparing the performance of a **Type-1 Bare-Metal Hypervisor (Proxmox VE)** and a **Type-2 Hosted Hypervisor (VMware Workstation)**. Both virtual machines were provisioned with identical hardware resources and ran Ubuntu as the guest operating system. CPU performance and latency were measured using **Sysbench**.

### Virtual Machine Resource Specification

| Resource | Type-1 (Proxmox VE) | Type-2 (VMware Workstation) |
| :--- | :--- | :--- |
| **Guest OS** | Ubuntu 22.04 LTS | Ubuntu 22.04 LTS |
| **vCPU** | 2 vCPU (1 Socket, 2 Cores) | 2 vCPU (1 Processor, 2 Cores) |
| **RAM** | 2048 MiB (~2 GB) | 2048 MB (~2 GB) |
| **Disk Size** | 20 GB | 20 GB |
| **Network Interface** | Bridge (`vmbr0`) | NAT |
| **Benchmark Tool** | Sysbench (`--cpu-max-prime=20000`) | Sysbench (`--cpu-max-prime=20000`) |

---

## 2. Benchmark Results & Data Comparison

The benchmark was executed using the command:
```bash
sysbench cpu --cpu-max-prime=20000 run

```
---
## 3. Performance Difference Summary
<table border="1">
  <tr>
    <th>Parameter</th>
    <th>Proxmox VE</th>
    <th>VMware Workstation</th>
    <th>Difference</th>
  </tr>
  <tr>
    <td><b>Execution Time</b></td>
    <td>9.9968 s</td>
    <td>9.9959 s</td>
    <td>0.0009 s</td>
  </tr>
  <tr>
    <td><b>Total Events</b></td>
    <td>15877</td>
    <td>6898</td>
    <td>8979</td>
  </tr>
  <tr>
    <td><b>Events per Second</b></td>
    <td>1587.47</td>
    <td>689.57</td>
    <td>897.90</td>
  </tr>
  <tr>
    <td><b>Minimum Latency</b></td>
    <td>0.59 ms</td>
    <td>1.25 ms</td>
    <td>0.66 ms </td>
  </tr>
  <tr>
    <td><b>Average Latency</b></td>
    <td>0.63 ms</td>
    <td>1.45 ms</td>
    <td>0.82 ms</td>
  </tr>
  <tr>
    <td><b>Maximum Latency</b></td>
    <td>1.34 ms</td>
    <td>4.23 ms</td>
    <td>2.89 ms</td>
  </tr>
</table>

## 3. Hypervisor Performance Visual Analysis
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





