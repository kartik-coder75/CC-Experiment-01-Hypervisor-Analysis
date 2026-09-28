# Performance Analysis of Virtual Machines and Containers

An experimental comparison of the performance, resource utilization, application behaviour, and scalability of a **VMware Ubuntu Virtual Machine** and a **Docker container** running identical workloads.

---

## Abstract

This project measures how a Virtual Machine (VMware Workstation, Ubuntu guest) and a Docker container behave under the same synthetic and application workloads. CPU, memory, disk I/O, network, a FastAPI web service, startup time, and scalability under increasing load are each tested using fixed resource allocations and repeated runs. Raw outputs, processed CSV data, statistical summaries, graphs, and all scripts are included so that every result can be reproduced.

> **Note:** No conclusion is assumed in advance. Findings must come only from the measured data in `results/`.

## Objectives

- Compare CPU, memory, disk, and network performance of a VM and a container under identical workloads.
- Evaluate a realistic application workload (FastAPI) in both environments.
- Compare startup and application-ready time.
- Study scalability as workload increases (1 / 2 / 4 / 8 threads, and increasing API concurrency).
- Apply repeated runs and statistical analysis (mean, median, min, max, standard deviation).
- Publish a reproducible, well-documented repository.

## Research Questions

1. How does CPU throughput differ between a VM and a container for the same workload?
2. Is there a measurable difference in memory operation speed and latency?
3. How do sequential/random read and write performance differ (MB/s, IOPS, latency)?
4. How does network throughput and retransmission behaviour compare?
5. How does a FastAPI service perform (requests/sec, latency, failed requests) in each environment?
6. How do startup and application-ready times compare?
7. How does each environment scale as load increases?

---

## Experimental Environment

```
┌───────────────────────────────────────────────┐
│           EXPERIMENTAL ENVIRONMENT            │
├───────────────────────────────────────────────┤
│ Host OS        → Windows                      │
│ Hypervisor     → VMware Workstation           │
│ Guest OS       → Ubuntu 24.04 LTS             │
│ Container      → Docker                       │
│ Programming    → Python                       │
│ CPU / Memory   → Sysbench                     │
│ Disk Benchmark → fio                          │
│ Network        → iperf3                       │
│ API            → FastAPI + Uvicorn            │
│ Load testing   → Apache Benchmark (ab), wrk   │
│ Analysis       → Pandas / Matplotlib / NumPy  │
└───────────────────────────────────────────────┘
```

### Hardware Configuration

Recorded automatically in the `docs/` folder:

| File | Contents |
|------|----------|
| `docs/cpu-info.txt` | `lscpu` output |
| `docs/memory-info.txt` | `free -h` output |
| `docs/storage-info.txt` | `lsblk` output |
| `docs/kernel-info.txt` | `uname -a` output |
| `docs/vm-configuration.txt` | Final VM settings used |

Host machine details (fill in your actual values):

| Item | Value |
|------|-------|
| Host CPU | _e.g. model / cores / threads_ |
| Host RAM | _e.g. 16 GB_ |
| Host storage | _e.g. NVMe SSD_ |
| Host OS | _e.g. Windows 11_ |

### Software Configuration

| Component | Version |
|-----------|---------|
| VMware Workstation | _record version_ |
| Ubuntu | 24.04 LTS |
| Docker | _output of `docker --version`_ |
| Sysbench / fio / iperf3 | _output of `--version` commands_ |
| Python | _output of `python3 --version`_ |

### Controlled Resource Allocation

Both environments use equal, fixed resources so the comparison is fair.

| Resource | VM | Container |
|----------|----|-----------|
| CPU | 4 vCPU | `--cpus=4` |
| Memory | 8 GB RAM | `--memory=8g` |
| Disk | 60 GB virtual disk | Host directory mounted with `-v` |
| OS / Image | Ubuntu 24.04 LTS | `ubuntu:24.04` based image |
| Network | NAT or Bridged (fixed) | Document the Docker network mode used |

> These are recommended example values. Record the values you actually used.

---

## Architecture

```
                 PERFORMANCE ANALYSIS
                          │
            ┌─────────────┴─────────────┐
            │                           │
     VIRTUAL MACHINE               CONTAINER
   (VMware + Ubuntu)                 (Docker)
            │                           │
            └─────────────┬─────────────┘
                          │
                   SAME WORKLOADS
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
         CPU            Memory      Disk / Network
                          │
                          ▼
                  APPLICATION TEST
                          │
                          ▼
                   FINAL ANALYSIS
```

## Project Structure

```
vm-vs-container-performance/
│
├── README.md
├── .gitignore
│
├── docs/
│   ├── architecture.png
│   ├── cpu-info.txt
│   ├── memory-info.txt
│   ├── storage-info.txt
│   ├── kernel-info.txt
│   ├── vm-configuration.txt
│   └── methodology.md
│
├── vm/
│   ├── setup.sh
│   └── benchmark.sh
│
├── docker/
│   ├── Dockerfile
│   └── benchmark.sh
│
├── api/
│   ├── main.py
│   ├── requirements.txt
│   └── Dockerfile
│
├── workloads/
│   ├── cpu/
│   ├── memory/
│   ├── disk/
│   └── network/
│
├── scripts/
│   ├── run_cpu.sh
│   ├── run_memory.sh
│   ├── run_disk.sh
│   ├── run_network.sh
│   ├── collect_metrics.py
│   ├── analyze_results.py
│   └── generate_plots.py
│
├── results/
│   ├── raw/
│   ├── processed/
│   └── figures/
│
└── analysis/
    └── analysis.ipynb
```

| Location | Purpose |
|----------|---------|
| `docs/` | Environment records and methodology |
| `docker/Dockerfile` | General benchmark image |
| `api/` | FastAPI source, requirements, Dockerfile |
| `scripts/` | Benchmark automation and analysis code |
| `results/raw/` | Unmodified benchmark output |
| `results/processed/` | Cleaned CSV data used for analysis |
| `results/figures/` | Generated graphs |
| `analysis/` | Jupyter notebook |

---

## Methodology

- The **same workload and parameters** are executed in the VM and the container.
- Each experiment is **repeated (10 runs)** to reduce the effect of temporary fluctuations.
- VM and container resource limits are **fixed and documented**.
- **Raw output is stored unmodified** in `results/raw/`; processed values go in `results/processed/`.
- Statistics: mean, median, min, max, and standard deviation.
- Difference formulas (applied consistently):

```python
# Execution time (lower is better)
difference = ((vm_time - container_time) / vm_time) * 100

# Throughput (higher is better)
difference = ((container_throughput - vm_throughput) / vm_throughput) * 100
```

> A percentage difference is not called "overhead" unless the calculation specifically represents overhead.

All commands below assume you are in the project root:

```bash
cd ~/vm-vs-container-performance
```

---

## Reproduction Instructions

### 1. Prepare the environment

Create an Ubuntu 24.04 VM in VMware Workstation (4 vCPU, 8 GB RAM, 60 GB disk, fixed network mode), then inside the VM:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y sysbench fio iperf3 htop iotop sysstat python3 python3-pip python3-venv git apache2-utils wrk
```

Verify:

```bash
sysbench --version
fio --version
iperf3 --version
python3 --version
git --version
nproc
free -h
```

### 2. Clone the repository and record the configuration

```bash
git clone <GITHUB-REPOSITORY-URL> ~/vm-vs-container-performance
cd ~/vm-vs-container-performance

mkdir -p docs results/raw results/processed results/figures scripts workloads
lscpu       > docs/cpu-info.txt
free -h     > docs/memory-info.txt
lsblk       > docs/storage-info.txt
uname -a    > docs/kernel-info.txt
docker --version
docker info
```

Write the final VM settings (vCPU, RAM, disk, OS, network mode) into `docs/vm-configuration.txt`.

### 3. Install Docker

```bash
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
# log out and back in, then verify
docker run --rm hello-world
```

### 4. Build the benchmark image

`docker/Dockerfile`:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update && \
    apt-get install -y \
    sysbench \
    fio \
    iperf3 \
    python3 \
    python3-pip \
    procps \
    sysstat && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /benchmark
```

Build and verify:

```bash
docker build -t vm-container-benchmark -f docker/Dockerfile .
docker images
docker run --rm vm-container-benchmark sysbench --version
```

### 5. Python environment for the API and analysis

Ubuntu 24.04 blocks system-wide `pip install`, so use a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install fastapi uvicorn pandas matplotlib numpy jupyter
```

---

## Baseline

A baseline CPU run establishes a reference point using the same parameters as later tests.

```bash
mkdir -p results/raw/baseline
sysbench cpu --cpu-max-prime=20000 --threads=4 --time=30 run > results/raw/baseline/cpu.txt
```

---

## CPU Experiment

| Setting | Value |
|---------|-------|
| Tool | Sysbench |
| Workload | Prime number calculation (`--cpu-max-prime=20000`) |
| Threads | 1 / 2 / 4 / 8 |
| Duration | 30 seconds |
| Metrics | Events/sec, execution time |
| Repetitions | 10 |

**VM (10 runs):**

```bash
mkdir -p results/raw/cpu/vm
for i in {1..10}
do
  sysbench cpu --cpu-max-prime=20000 --threads=4 --time=30 run \
    > results/raw/cpu/vm/run$i.txt
done
```

**Container (10 runs):**

```bash
mkdir -p results/raw/cpu/container
for i in {1..10}
do
  docker run --rm --cpus=4 --memory=8g \
    vm-container-benchmark \
    sysbench cpu --cpu-max-prime=20000 --threads=4 --time=30 run \
    > results/raw/cpu/container/run$i.txt
done
```

**CPU scalability script** (`scripts/run_cpu.sh`):

```bash
#!/bin/bash
OUTPUT_DIR="results/raw/cpu"
mkdir -p "$OUTPUT_DIR"

for threads in 1 2 4 8
do
  echo "Running CPU test with $threads threads"
  sysbench cpu \
    --cpu-max-prime=20000 \
    --threads=$threads \
    --time=30 \
    run > "$OUTPUT_DIR/cpu_${threads}_threads.txt"
done

echo "CPU benchmark completed."
```

```bash
chmod +x scripts/run_cpu.sh
./scripts/run_cpu.sh        # run from the project root
```

---

## Memory Experiment

| Setting | Value |
|---------|-------|
| Tool | Sysbench memory |
| Block size | 1 MB |
| Total size | 10 GB |
| Threads | 4 |
| Metrics | Operations/sec, throughput (MiB/s), latency |

**VM:**

```bash
mkdir -p results/raw/memory/vm
for i in {1..10}
do
  sysbench memory --memory-block-size=1M --memory-total-size=10G --threads=4 run \
    > results/raw/memory/vm/run$i.txt
done
```

**Container:**

```bash
mkdir -p results/raw/memory/container
for i in {1..10}
do
  docker run --rm --cpus=4 --memory=8g \
    vm-container-benchmark \
    sysbench memory --memory-block-size=1M --memory-total-size=10G --threads=4 run \
    > results/raw/memory/container/run$i.txt
done
```

### Resource Monitoring

Run in a second terminal while benchmarks execute, and record the results alongside the benchmark output:

```bash
htop            # or: vmstat 1
docker stats    # for containers
```

---

## Disk I/O Experiment

| Setting | Value |
|---------|-------|
| Tool | fio |
| Operations | Read / Write |
| Patterns | Sequential (1M blocks) / Random (4k blocks) |
| Queue depth | 16 |
| Runtime | 30 seconds, `--direct=1` |
| Metrics | MB/s, IOPS, latency |

**VM:**

```bash
mkdir -p ~/fio-test results/raw/disk/vm

fio --name=seq-write  --filename=$HOME/fio-test/testfile --size=2G --bs=1M --rw=write     --direct=1 --iodepth=16 --runtime=30 --time_based > results/raw/disk/vm/seq-write.txt
fio --name=seq-read   --filename=$HOME/fio-test/testfile --size=2G --bs=1M --rw=read      --direct=1 --iodepth=16 --runtime=30 --time_based > results/raw/disk/vm/seq-read.txt
fio --name=rand-read  --filename=$HOME/fio-test/testfile --size=2G --bs=4k --rw=randread  --direct=1 --iodepth=16 --runtime=30 --time_based > results/raw/disk/vm/rand-read.txt
fio --name=rand-write --filename=$HOME/fio-test/testfile --size=2G --bs=4k --rw=randwrite --direct=1 --iodepth=16 --runtime=30 --time_based > results/raw/disk/vm/rand-write.txt
```

**Container** (host directory mounted with `-v`; storage must be equivalent to the VM test):

```bash
mkdir -p ~/fio-test results/raw/disk/container

docker run --rm -v ~/fio-test:/fio-test vm-container-benchmark \
  fio --name=seq-write --filename=/fio-test/testfile --size=2G --bs=1M \
      --rw=write --direct=1 --iodepth=16 --runtime=30 --time_based \
  > results/raw/disk/container/seq-write.txt

docker run --rm -v ~/fio-test:/fio-test vm-container-benchmark \
  fio --name=seq-read --filename=/fio-test/testfile --size=2G --bs=1M \
      --rw=read --direct=1 --iodepth=16 --runtime=30 --time_based \
  > results/raw/disk/container/seq-read.txt

docker run --rm -v ~/fio-test:/fio-test vm-container-benchmark \
  fio --name=rand-read --filename=/fio-test/testfile --size=2G --bs=4k \
      --rw=randread --direct=1 --iodepth=16 --runtime=30 --time_based \
  > results/raw/disk/container/rand-read.txt

docker run --rm -v ~/fio-test:/fio-test vm-container-benchmark \
  fio --name=rand-write --filename=/fio-test/testfile --size=2G --bs=4k \
      --rw=randwrite --direct=1 --iodepth=16 --runtime=30 --time_based \
  > results/raw/disk/container/rand-write.txt
```

> Use exactly the same fio parameters in both environments. Document the storage path or volume used, since storage placement can change results.

---

## Network Experiment

| Setting | Value |
|---------|-------|
| Tool | iperf3 |
| Metrics | Throughput, retransmissions |
| Test | Client ↔ Server, 30 s, 1 and 4 parallel streams |

```bash
mkdir -p results/raw/network

# Server
iperf3 -s

# Find server IP
ip addr

# Client
iperf3 -c <SERVER-IP> -t 30       > results/raw/network/iperf3.txt
iperf3 -c <SERVER-IP> -t 30 -P 4  > results/raw/network/iperf3_parallel4.txt
```

> Use the same client/server arrangement for VM and container tests, and document the network mode (NAT, bridged, or Docker host/bridge networking).

---

## Application Experiment (FastAPI)

**`api/main.py`:**

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/health")
def health():
    return {"status": "healthy"}

@app.get("/compute")
def compute():
    total = 0
    for i in range(1_000_000):
        total += i * i
    return {"result": total}

@app.get("/memory")
def memory():
    data = [i for i in range(1_000_000)]
    return {"elements": len(data)}
```

**`api/requirements.txt`:**

```
fastapi
uvicorn
```

**`api/Dockerfile`:**

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY main.py .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Run in the VM:**

```bash
cd api
uvicorn main:app --host 0.0.0.0 --port 8000
# in a second terminal
curl http://localhost:8000/health      # {"status":"healthy"}
```

**Run in Docker:**

```bash
docker build -t performance-api -f api/Dockerfile api
docker run --rm --cpus=4 --memory=8g -p 8000:8000 performance-api
curl http://127.0.0.1:8000/health
```

**Benchmark the API** (identical parameters for VM and container):

```bash
mkdir -p results/raw/api

ab -n 10000 -c 100 http://127.0.0.1:8000/health  > results/raw/api/ab_health.txt
ab -n 1000  -c 10  http://127.0.0.1:8000/compute > results/raw/api/ab_compute.txt
wrk -t4 -c100 -d30s http://127.0.0.1:8000/health > results/raw/api/wrk_health.txt
```

Key metrics: requests per second, time per request, failed requests, connection times.

---

## Startup-Time Experiment

```bash
# Simple measurement
time docker run --rm performance-api

# Controlled measurement
time docker run --rm -d --name startup-test -p 8000:8000 performance-api
docker stop startup-test
```

Record for both environments (repeat several times and average):

| Measurement | VM | Container |
|-------------|----|-----------|
| Environment startup | _ | _ |
| Application ready | _ | _ |

---

## Scalability Experiment

```
Workload 1 → Workload 2 → Workload 4 → Workload 8
```

**CPU scalability:**

```bash
for threads in 1 2 4 8
do
  sysbench cpu --cpu-max-prime=20000 --threads=$threads --time=30 run
done
```

**API scalability:**

```bash
wrk -t1 -c10  -d30s http://127.0.0.1:8000/health
wrk -t2 -c50  -d30s http://127.0.0.1:8000/health
wrk -t4 -c100 -d30s http://127.0.0.1:8000/health
wrk -t4 -c200 -d30s http://127.0.0.1:8000/health
```

Record throughput, latency, CPU usage, and memory usage at each level.

---

## Results

### Processed data format

Processed values are stored in `results/processed/`, for example `cpu_results.csv`:

```csv
environment,threads,execution_time,events_per_second
VM,1,<measured>,<measured>
Container,1,<measured>,<measured>
```

> Replace `<measured>` with your own results. Do not copy example numbers. Never alter raw files to make results look cleaner.

### Figures

Generated graphs are saved in `results/figures/`:

| Figure | File |
|--------|------|
| Average CPU performance | `results/figures/cpu_performance.png` |
| CPU scalability | `results/figures/cpu_scalability.png` |
| Memory | `results/figures/memory_performance.png` |
| Disk I/O | `results/figures/disk_performance.png` |
| Network | `results/figures/network_performance.png` |
| Application | `results/figures/api_performance.png` |
| Startup | `results/figures/startup_time.png` |

_After generating the graphs, embed them, e.g.:_ `![CPU Scalability](results/figures/cpu_scalability.png)`

## Statistical Analysis

```bash
python3 scripts/analyze_results.py
python3 scripts/generate_plots.py
```

**`scripts/analyze_results.py`:**

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("results/processed/cpu_results.csv")
print(df)

summary = df.groupby("environment")["events_per_second"].agg(
    ["mean", "median", "min", "max", "std"]
)
print("\nCPU Performance Summary:")
print(summary)

summary["mean"].plot(kind="bar", title="Average CPU Performance")
plt.ylabel("Events per Second")
plt.xlabel("Environment")
plt.tight_layout()
plt.savefig("results/figures/cpu_performance.png", dpi=300)
plt.show()
```

**`scripts/generate_plots.py`:**

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("results/processed/cpu_results.csv")

for environment in df["environment"].unique():
    data = df[df["environment"] == environment]
    plt.plot(data["threads"], data["events_per_second"],
             marker="o", label=environment)

plt.xlabel("Number of Threads")
plt.ylabel("Events per Second")
plt.title("CPU Performance vs Number of Threads")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.savefig("results/figures/cpu_scalability.png", dpi=300)
plt.show()
```

Equivalent graphs should be created for memory, disk, network, application, startup, and scalability. Every graph needs a title, axis labels, units, and a legend.

- **Mean** – average performance across repeated runs.
- **Median** – reduces the influence of outliers.
- **Standard deviation** – shows run-to-run variation.

## VM vs Container Comparison

Populate only with measured values (include units) and calculate the difference with the formulas in the Methodology section.

| Metric | VM | Container | Difference |
|--------|----|-----------|------------|
| CPU Performance (events/sec) | | | |
| Memory Throughput (MiB/s) | | | |
| Sequential Read (MB/s) | | | |
| Sequential Write (MB/s) | | | |
| Random Read (IOPS) | | | |
| Random Write (IOPS) | | | |
| Network Throughput (Mbps) | | | |
| Startup Time (s) | | | |
| API Requests/sec | | | |
| API Latency (ms) | | | |

## Discussion

_Interpret the measured results here: where the VM and container differ, where they are similar, and possible reasons (virtualization layer, shared kernel, storage driver, network mode, etc.). Support every claim with the collected data._

## Limitations

- Results depend on the specific host hardware, VMware version, and Docker configuration.
- The container runs on a shared kernel, while the VM runs a full guest OS on a hypervisor.
- Storage and network configuration (NAT/bridged, bind mount/volume) can significantly change results.
- Background host activity may introduce noise despite repeated runs.
- Synthetic benchmarks do not represent all real-world workloads.

## Conclusion

_Write the final conclusion based only on the measured data, repeated runs, statistical analysis, and documented configuration. Do not assume beforehand that either environment will perform better._

## Future Work

- Kubernetes replica and autoscaling experiments.
- Additional workloads (databases, web servers, machine-learning inference).
- Testing other hypervisors and container runtimes.
- Testing different Docker storage drivers and network modes.

---

## Publishing / Git Workflow

Run all Git commands from the project root. The whole project is a single Git repository.

```bash
cd ~/vm-vs-container-performance
git init
git add .
git status
git commit -m "Initial project setup"
git branch -M main
git remote add origin <GITHUB-REPOSITORY-URL>
git push -u origin main
```

Later updates:

```bash
git status
git add .
git commit -m "Add CPU benchmark results"
git push
```

**`.gitignore`:**

```
__pycache__/
*.pyc
.venv/
venv/
.env
.ipynb_checkpoints/
*.log
.vscode/
.idea/
*.vmdk
*.vmem
*.vmss
*.nvram
```

> Never commit credentials, API keys, VM disk files, or large temporary files.

## Important Experimental Practice

Do not assume in advance that VMs or containers will always perform better. The purpose of this project is to measure actual behaviour under controlled conditions. The final conclusion must rest on the collected measurements, repeated runs, statistical analysis, and documented configuration.

