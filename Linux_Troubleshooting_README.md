# 🐧 Linux Troubleshooting

> **A practical quick-reference guide for diagnosing common Linux issues
> in DevOps and production environments.**

Linux powers AWS EC2 instances, Docker hosts, Kubernetes worker nodes,
CI/CD runners, application servers, and databases. This guide focuses on
three common production problems: **CPU spikes, memory leaks, and disk
exhaustion**.

## 🎯 What You'll Learn

  -----------------------------------------------------------------------
  Topic                               What you'll learn
  ----------------------------------- -----------------------------------
  🔥 CPU spikes                       Identify processes consuming
                                      excessive CPU

  🧠 Memory leaks                     Investigate increasing RAM
                                      consumption

  💾 Disk exhaustion                  Find large files and directories
                                      filling storage
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 1. 🔥 CPU Spikes

### What is the problem?

Imagine an EC2 instance normally uses 20% CPU, but suddenly reaches
100%.

**Possible impact** - Applications become slow. - Request latency
increases. - Processes may become unresponsive. - Workloads may perform
poorly.

**Common causes** - Infinite loops in application code. - Too many
concurrent requests. - Heavy background jobs. - Inefficient database
queries. - Resource-intensive processes.

### Troubleshooting commands

``` bash
# Monitor CPU and running processes
top

# Interactive process monitoring
htop

# Display CPU architecture and information
lscpu

# Find processes consuming the most CPU
ps aux --sort=-%cpu | head
```

Example output:

``` text
PID    USER    %CPU    COMMAND
1234   root    95.2    python app.py
1456   root    20.5    java
```

**Key takeaway:** Identify the process consuming CPU before deciding
whether to restart it or investigate further.

------------------------------------------------------------------------

## 2. 🧠 Memory Leaks

### What is the problem?

A memory leak occurs when an application retains memory it no longer
needs, preventing that memory from being reclaimed.

Example:

``` text
Application starts  →  200 MB
After some time     →  300 MB
Later               →  500 MB
Eventually          →  1.5 GB
```

**Possible impact** - System performance degrades. - Other applications
may not get enough memory. - Linux may invoke the OOM Killer. -
Kubernetes containers may be terminated with `OOMKilled`.

### Troubleshooting commands

``` bash
# Check RAM and swap usage
free -h

# Monitor process memory
top

# Find processes consuming the most RAM
ps aux --sort=-%mem | head
```

### Kubernetes investigation

``` bash
# Check pod resource consumption
kubectl top pods

# Inspect pod status, events and container details
kubectl describe pod <pod-name>
```

**Key takeaway:** Increasing memory usage is a symptom, not proof of a
memory leak. Investigate application behavior and container limits.

------------------------------------------------------------------------

## 3. 💾 Disk Exhaustion

### What is the problem?

Imagine an EC2 instance has a 20 GB disk. Your application continuously
generates logs, and eventually the disk reaches 100% utilization.

**Possible impact** - Applications cannot write logs. - Temporary file
creation fails. - Database operations may fail. - Services may fail to
start.

**Common causes** - Unrotated application logs. - Large temporary
files. - Old backups. - Unused Docker images and containers. - Excessive
application data.

### Troubleshooting commands

``` bash
# Check filesystem disk usage
df -h

# Check directory sizes
du -sh /var/log/*

# Find files larger than 100 MB
sudo find /var/log -type f -size +100M
```

> **Important:** Never blindly delete files. Identify what is consuming
> storage and follow appropriate cleanup and log-retention policies.

------------------------------------------------------------------------

## ⚡ Quick Revision Cheat Sheet

  Problem             Command                 Purpose
  ------------------- ----------------------- ----------------------------
  CPU usage           `top`                   Monitor processes
  CPU ranking         `ps aux --sort=-%cpu`   Find high-CPU processes
  RAM usage           `free -h`               Check memory utilization
  Memory ranking      `ps aux --sort=-%mem`   Find high-memory processes
  Disk usage          `df -h`                 Check filesystem capacity
  Directory size      `du -sh`                Find large directories
  Running processes   `ps aux`                List processes
  System logs         `journalctl`            Inspect system logs

------------------------------------------------------------------------

## ☸️ Connection with DevOps & Kubernetes

Kubernetes runs workloads on worker nodes, and understanding Linux helps
diagnose problems underneath the cluster.

  Linux problem       Possible Kubernetes impact
  ------------------- -----------------------------------------------
  High CPU            CPU contention, throttling and slow workloads
  High memory usage   `OOMKilled` containers and pod instability
  Disk exhaustion     Disk pressure and pod eviction

**Remember:** Kubernetes manages workloads, but Linux knowledge helps
you investigate what is happening underneath the cluster.

------------------------------------------------------------------------

## 🧠 Final Takeaway

``` text
CPU problem     → Identify the process
Memory problem  → Investigate RAM consumption
Disk problem    → Find what is consuming storage
```

**Learn the commands → Understand the output → Identify the root cause →
Fix the issue.**
