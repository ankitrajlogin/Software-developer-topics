# 🧠 Operating System Notes — CPU Scheduling (Complete Notes)

---

## 🧩 What is CPU Scheduling?

**CPU Scheduling** is the process by which the **Operating System (OS)** decides which process should run on the CPU at any given time.

Since multiple processes may be in the ready queue, the OS must decide the order of execution to:
- Maximize CPU utilization
- Improve throughput
- Reduce waiting time and response time

---

## 🎯 Objectives of CPU Scheduling

- **Maximize CPU Utilization** → Keep the CPU as busy as possible  
- **Maximize Throughput** → Increase number of processes completed per unit time  
- **Minimize Turnaround Time** → Reduce total time from submission to completion  
- **Minimize Waiting Time** → Reduce idle waiting time in the ready queue  
- **Minimize Response Time** → Improve interactivity in time-sharing systems  
- **Ensure Fairness** → Avoid starvation and ensure equitable CPU sharing  

---

## ⚙️ Types of CPU Scheduling

| Type | Description |
|------|--------------|
| **Preemptive Scheduling** | CPU can be taken away from a process before it finishes (e.g., RR, SRTF) |
| **Non-Preemptive Scheduling** | Once a process gets CPU, it keeps it until completion or it voluntarily releases it (e.g., FCFS, SJF) |

---

## 🧠 Important Terms in CPU Scheduling

| Term | Definition | Formula / Example |
|------|-------------|------------------|
| **Arrival Time (AT)** | Time when process enters ready queue | e.g., P1 arrives at 0 ms |
| **Burst Time (BT)** | Total CPU time required for execution (without waiting time). <br> It is also known as Execution Time or CPU Burst. | e.g., P1 needs 5 ms CPU |
| **Completion Time (CT)** | The time at which a process finishes its execution and leaves the CPU. | e.g., P1 finishes execution at time = 10 ms, |
| **Turnaround Time (TAT)** | The total time taken by a process from its arrival to its completion.(waiting + execution) | `TAT = CT - AT`  |
| **Waiting Time (WT)** | The total time a process spends waiting in the ready queue before it gets CPU time. | `WT = TAT - BT` |
| **Response Time (RT)** | The time interval between the arrival of a process and the first time it gets the CPU. | `RT = First CPU Start - AT` |

---

## CPU Scheduiling Criteria 

| Criterion           | Description                                     | Desired |
| ------------------- | ----------------------------------------------- | ------- |
| **CPU Utilization** | Percentage of time CPU is busy.                 | High    |
| **Throughput**      | Number of processes completed per unit time.    | High    |
| **Turnaround Time** | Time from submission to completion.             | Low     |
| **Waiting Time**    | Time process spends waiting in the ready queue. | Low     |
| **Response Time**   | Time to start responding.                       | Low     |


### 🧮 Example: FCFS Scheduling (First Come , First Serve)

| Process | Arrival Time (AT) | Burst Time (BT) |
|----------|-------------------|----------------|
| P1 | 0 | 4 |
| P2 | 1 | 3 |
| P3 | 2 | 1 |

**Gantt Chart:**  
`0----4----7----8`  
`P1 → P2 → P3`

| Process | AT | BT | CT | TAT = CT-AT | WT = TAT-BT | RT = Start-AT |
|----------|----|----|----|--------------|--------------|---------------|
| P1 | 0 | 4 | 4 | 4 | 0 | 0 |
| P2 | 1 | 3 | 7 | 6 | 3 | 3 |
| P3 | 2 | 1 | 8 | 6 | 5 | 5 |

---

## 🧾 Types of CPU Scheduling Algorithms

---

### 1. **First Come First Serve (FCFS)**

**Type:** Non-Preemptive  
**Working:**  
Processes are executed in the order they arrive.

**Advantages:**
- Simple and easy to implement  

**Disadvantages:**
- Convoy effect (short jobs wait behind long jobs)
- Poor for interactive systems

---

### 2. **Shortest Job First (SJF)**

**Type:** Both Preemptive and Non-Preemptive  
**Working:**  
Selects the process with the **shortest burst time** next.

**Advantages:**
- Minimum average waiting time (optimal in theory)

**Disadvantages:**
- Requires prior knowledge of burst time
- May cause starvation of longer jobs

**Preemptive version:** *Shortest Remaining Time First (SRTF)*

---

### 3. **Shortest Remaining Time First (SRTF)**

**Type:** Preemptive  
**Working:**  
If a new process arrives with a smaller burst time than the remaining time of the current one, CPU is preempted.

**Advantages:**
- Improves response for shorter jobs

**Disadvantages:**
- Starvation possible for long processes
- Requires accurate burst time estimation

---

### 4. **Round Robin (RR)**

**Type:** Preemptive  
**Working:**  
Each process gets a fixed **time quantum (Q)** in cyclic order.
If a process doesn’t finish in its time slice, it’s sent back to the ready queue.

**Example:**
- Time Quantum = 4 ms
- Processes: P1(5), P2(8), P3(12)

**Gantt Chart:**  
`P1(4) → P2(4) → P3(4) → P1(1) → P2(4) → P3(8)`

**Advantages:**
- Fair allocation to all processes
- Suitable for time-sharing systems

**Disadvantages:**
- Too small quantum → too many context switches
- Too large quantum → behaves like FCFS

---

### 5. **Priority Scheduling**

**Type:** Preemptive or Non-Preemptive  
**Working:**  
Each process is assigned a **priority number**; CPU is given to the highest priority process.
(Smaller number = higher priority, usually)

**Example:**
| Process | Burst Time | Priority |
|----------|-------------|----------|
| P1 | 10 | 3 |
| P2 | 1 | 1 |
| P3 | 2 | 2 |

**Execution Order:** P2 → P3 → P1

**Disadvantages:**
- Starvation of low-priority processes  
**Solution:** *Aging* — increase priority of waiting processes over time.

---

### 6. **Multilevel Queue Scheduling (MLQ)**

**Type:** Non-Preemptive  
**Concept:**  
The ready queue is divided into **multiple separate queues**, each with its own scheduling algorithm and fixed priority.

Processes are permanently assigned to one queue based on a specific property such as:
- Process type (system, interactive, batch, etc.)
- Priority
- Memory size
- Process privilege level (foreground/background)

**Example Setup:**

| Queue No. | Process Type       | Scheduling Algorithm | Priority |
| --------- | ------------------ | -------------------- | -------- |
| Q1        | System / Real-time | Round Robin (RR)     | Highest  |
| Q2        | Interactive / User | Round Robin (RR)     | Medium   |
| Q3        | Batch / Background | FCFS                 | Lowest   |


**Working:**
- CPU executes jobs from **Q1** first.
- Only if Q1 is empty, **Q2** processes get CPU.

**Advantages:**
- Simple and logical process separation.

**Disadvantages:**
- Rigid (no movement between queues)
- Starvation possible for low-priority queues

---

### 7. **Multilevel Feedback Queue Scheduling (MLFQ)**

**Type:** Preemptive  
**Concept:**  
A process **can move between queues** based on its CPU usage and behavior.

**Idea:**
- Short jobs stay in high-priority queues.  
- Long jobs move down to lower queues.  
- Waiting processes can move up (aging).

**Example Setup:**

| Queue | Scheduling | Time Quantum | Priority |
|--------|-------------|---------------|-----------|
| Q1 | Round Robin | 4 ms | Highest |
| Q2 | Round Robin | 8 ms | Medium |
| Q3 | FCFS | — | Lowest |

**Working:**
1. New process enters **Q1**.  
2. If it uses up 4 ms → moved to **Q2**.  
3. If it uses up 8 ms → moved to **Q3**.  
4. Aging ensures no starvation.

**Advantages:**
- Dynamic and fair  
- Prevents starvation  
- Adaptable (used in modern OS like Linux/Windows)

**Disadvantages:**
- Complex to implement and tune
- Overhead of managing multiple queues

---

### 8. **Highest Response Ratio Next (HRRN)**

**Type:** Non-Preemptive  
**Formula:**
\[
\text{Response Ratio} = \frac{\text{Waiting Time} + \text{Burst Time}}{\text{Burst Time}}
\]

**Working:**
Selects the process with the **highest response ratio** next.

**Advantages:**
- Reduces starvation (balances short and long jobs)

---

## ⚡ Comparison of Scheduling Algorithms

| Algorithm | Type | Preemptive | Starvation | Suitable For |
|------------|------|-------------|-------------|---------------|
| **FCFS** | Non-Preemptive | ❌ | ✅ | Batch systems |
| **SJF** | Non-Preemptive | ❌ | ✅ | Batch systems |
| **SRTF** | Preemptive | ✅ | ✅ | Short jobs |
| **Priority** | Both | ✅ / ❌ | ✅ | Mixed workloads |
| **RR** | Preemptive | ✅ | ❌ | Time-sharing systems |
| **MLQ** | Non-Preemptive | ❌ | ✅ | Multi-type processes |
| **MLFQ** | Preemptive | ✅ | ❌ | Modern OS |
| **HRRN** | Non-Preemptive | ❌ | ❌ | Balanced workloads |

---

## 🧩 Key Components in Scheduling

| Component | Description |
|------------|--------------|
| **Scheduler** | Decides which process runs next |
| **Dispatcher** | Gives CPU control to chosen process |
| **Context Switch** | Saving/restoring state between process switches |
| **Throughput** | No. of processes completed per unit time |
| **Turnaround Time** | Time between process submission and completion |
| **Waiting Time** | Time spent waiting in the ready queue |
| **Response Time** | Time until first response is produced |

---

## 🧠 Real-World Examples

| OS | Scheduling Type |
|----|------------------|
| **Windows** | Multilevel Feedback Queue |
| **Linux** | Completely Fair Scheduler (CFS) — MLFQ-based |
| **macOS / iOS** | Priority + Feedback Queue |
| **Embedded Systems** | Priority or FCFS |

---

## 🧾 Summary

| Algorithm | Key Feature | Best Use |
|------------|--------------|-----------|
| **FCFS** | Simple, FIFO order | Batch systems |
| **SJF/SRTF** | Optimal waiting time | Short predictable jobs |
| **Priority** | Priority-based | Real-time systems |
| **Round Robin** | Fair CPU time | Interactive systems |
| **MLQ** | Fixed queues | Multi-level workloads |
| **MLFQ** | Dynamic priority | Modern OS |
| **HRRN** | Balances long & short jobs | General systems |

---

## 🧮 Quick Formulas Recap

| Metric | Formula |
|---------|----------|
| Turnaround Time (TAT) | `CT - AT` |
| Waiting Time (WT) | `TAT - BT` |
| Response Time (RT) | `Start Time - AT` |

---

## 🏁 Final Takeaway

- **CPU Scheduling** improves performance and fairness.  
- **Preemptive algorithms** offer responsiveness, while **non-preemptive** ones are simpler.  
- **MLFQ** is the most flexible and widely used scheduling algorithm today.

---
