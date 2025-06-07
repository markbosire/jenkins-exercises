# Parallel System Metrics Monitor (Jenkins Pipeline)

This folder contains a simple Jenkins Pipeline script that demonstrates **parallelization** of independent tasks within a pipeline. The example illustrates how to simultaneously collect **CPU**, **Memory**, and **Disk** usage metrics, then compiles them into a single report.

## 💡 Purpose

To show how `parallel` stages in Jenkins pipelines can help run independent tasks concurrently, improving efficiency and pipeline performance.

---

## 🧪 What It Does

1. **Pipeline agent:** Runs on any available agent.
2. **Main stage (`Monitor System Metrics`)**:

   * Runs the following tasks **in parallel**:

     * **CPU Usage**: Captures usage statistics using `top`.
     * **Memory Usage**: Fetches memory info using `free`.
     * **Disk Usage**: Reports disk usage from `/dev/sda2` using `df`.
3. **Post-success step**:

   * Aggregates the individual reports into a single `system_report.txt`.
   * Prints the final report to the console.
   * Cleans up the temporary metric files.

---

## 📄 Output Example (`system_report.txt`)

```text
=== CPU USAGE ===
CPU Usage: 5.6% user, 2.1% system, 91.7% idle

=== MEMORY USAGE ===
Memory Usage: Used: 2048MB / Total: 4096MB (Free: 2048MB)

=== DISK USAGE ===
Disk Usage on /dev/sda2: 58% used (29G of 50G)
```

---

## 🚀 How to Use

1. Paste the pipeline script into a **Jenkins Pipeline job**.
2. Run the pipeline.
3. Check the console output for the final system report.

---

## 🛠️ Requirements

* Jenkins with a working agent (Linux-based).
* Tools available on the agent:

  * `top`
  * `free`
  * `df`
  * `awk`, `grep`, `sh`

---

## 📚 Jenkins Concepts Illustrated

* `parallel {}`: Run independent steps simultaneously.
* `post { success {}}`: Define tasks to run after successful pipeline execution.
* `sh`: Execute shell commands on the agent.
* File handling and report aggregation.

---



