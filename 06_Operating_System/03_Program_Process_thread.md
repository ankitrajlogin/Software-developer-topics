# Program And Process - All About It 

## What is Program 
Definition

A program is a set of instructions written in a programming language that tells the computer how to perform a specific task.

It is a passive entity — meaning it does not perform any action by itself until it is executed by the operating system.

It is stored on disk (in secondary memory) as an executable file, waiting to be executed.

Example:

chrome.exe, notepad.exe, or a C program file hello.c.


## What is Process 
Definition

A process is a program in execution.
It is an active entity that performs a specific task using system resources such as CPU, memory, and input/output devices.
```
In simple terms — when a program starts running, it becomes a process.
```

### ⚙️ Characteristics of a Process
1. **Active Entity** – A process is a running instance of a program (unlike a program which is passive).
2. **Unique Process ID (PID)** – Each process is assigned a unique identification number by the OS.
3. **Resource Ownership** – Every process requires resources like CPU time, memory, files, and I/O devices.
4. **Execution Context** – Each process maintains information about its current state, program counter, stack, and registers.
5. **Independent Execution** – Each process runs independently and has its own address space.
6. **Concurrency** – Multiple processes can run concurrently (appearing to run simultaneously using CPU scheduling).


### 🧩 Components / Structure of a Process
A process is represented in the operating system by a Process Control Block (PCB).
Process Control Block contains important information about the process, including:
| **Component**                     | **Description**                                           |
| --------------------------------- | --------------------------------------------------------- |
| **Process ID (PID)**              | Unique identification number for the process              |
| **Process State**                 | Current state (ready, running, waiting, etc.)             |
| **Program Counter (PC)**          | Points to the next instruction to be executed             |
| **CPU Registers**                 | Holds the current working variables and intermediate data |
| **Memory Management Information** | Page tables, base and limit registers                     |
| **Accounting Information**        | CPU usage, time limits, process priority                  |
| **I/O Status Information**        | List of open files, I/O devices assigned                  |


## PROGRAM VS PROCESS 
### 🔍 Difference Between Program and Process
| **Aspect**       | **Program**                   | **Process**                   |
| ---------------- | ----------------------------- | ----------------------------- |
| **Nature**       | Passive (set of instructions) | Active (execution of program) |
| **Stored In**    | Secondary memory              | Main memory (RAM)             |
| **Execution**    | Not executing                 | Executing                     |
| **Memory Usage** | None until executed           | Uses CPU, memory, I/O         |
| **Example**      | `chrome.exe` file             | Running Chrome window         |

### 🧠 Summary
- A Program is a set of coded instructions stored on disk.
- It is inactive until it is executed.
- Once it starts running, it becomes a Process.
- Programs are essential building blocks of all software applications.


## 🧩 What is a Thread in Operating System
### Definition

A thread is the smallest unit of CPU execution within a process.
It is often called a lightweight process because it can run independently but shares resources with other threads of the same process.
```
In simple terms — a thread is like a sub-task inside a process that helps the process perform multiple actions simultaneously.
```

### ⚙️ Example to Understand

Let’s take an example of a web browser (like Chrome):
- One thread handles user input (keyboard, mouse).
- Another thread loads web pages.
- Another thread plays videos or audio.

👉 All these threads belong to the same process (browser) and share the same memory but run different tasks concurrently.

### 🧠 Characteristics of Threads
1. **Smallest Unit of Execution**    
A thread is the minimal unit that the CPU can schedule and execute.  

2. **Shared Resources**    
Threads of the same process share the same memory space, code, data, and files.

3. **Independent Execution Flow**    
Each thread has its own program counter (PC), stack, and set of registers to maintain execution flow.

4. **Faster Context Switching**    
Switching between threads of the same process is faster than switching between different processes because they share the same address space.

5. **Inter-thread Communication**    
Threads can easily communicate with each other through shared memory, unlike processes which require complex Inter-Process Communication (IPC).

### 🧩 Structure of a Thread

Each thread has:
- Thread ID – Unique identifier for each thread.
- Program Counter (PC) – Keeps track of the next instruction to execute.
- Registers – Stores current working variables.
- Stack – Used to store local variables and function calls.

Threads share:
- Code section
- Data section
- Open files and system resources

### 🔄 Types of Threads
1. **User-Level Threads (ULT)**
- Managed by user libraries, not by the OS kernel.
- Faster to create and manage.
- The OS is not aware of user threads.
- If one thread blocks, all threads in that process may block.
- 🧠 Example: Java threads, POSIX threads (Pthreads).  


2. **Kernel-Level Threads (KLT)**
- Managed directly by the Operating System kernel.
- Each thread is known to the OS.
- Better for multiprocessor systems — multiple threads can run in parallel.
- Creation and management are slower (more overhead).
- 🧠 Example: Windows and Linux kernel threads.

3. **Hybrid Threads**
- Combines both user-level and kernel-level threads.
- User threads are mapped to kernel threads for execution.
- Achieves a balance between performance and control.
- 🧠 Example: Solaris, modern Linux systems.



### ⚠️ Disadvantages of Threads
- **Complex Debugging:**   	Shared memory can cause synchronization issues (race conditions).
- **Security Risks:**	Threads share memory — one faulty thread can corrupt the entire process.
- **Difficult Synchronization:**	Requires mechanisms like mutexes, semaphores to coordinate access to shared data.


### 🔍 Difference Between Process and Thread
| **Aspect**        | **Process**                       | **Thread**                                     |
| ----------------- | --------------------------------- | ---------------------------------------------- |
| **Definition**    | Independent program in execution  | Smallest unit of execution within a process    |
| **Memory**        | Each process has its own memory   | Threads share the same memory                  |
| **Communication** | Through IPC (slow)                | Via shared memory (fast)                       |
| **Creation Time** | Slow                              | Fast                                           |
| **Crash Impact**  | A crash affects only that process | A crash can bring down the whole process       |
| **Example**       | Chrome, Word                      | Tabs or background tasks within Chrome or Word |

### 🧠 Summary
- A Thread is a lightweight process and the smallest unit of execution.
- Threads share resources within a process but execute independently.
- Multithreading improves performance, responsiveness, and parallelism.
- Proper synchronization is crucial to prevent data inconsistency and race conditions.


## ⚙️ What is Multithreading in Operating System
### Definition

Multithreading is the ability of a CPU (or an operating system) to execute multiple threads of the same process simultaneously.  
It allows a single program (process) to perform multiple tasks at the same time by dividing its work into smaller independent threads.

```
In simple words — Multithreading means running multiple parts (threads) of a program concurrently within the same process.
```

### 💡 Why Multithreading?
Multithreading improves:
- Performance — tasks run concurrently.
- Responsiveness — UI remains active while background work continues.
- Resource utilization — better use of CPU cores.
- Scalability — allows parallel execution on multicore processors.


### 🧩 Types of Multithreading Models
Operating systems use different models to map user threads to kernel threads:

1. **Many-to-One Model**
- Many user threads mapped to a single kernel thread.
- Entire process blocks if one thread blocks.
- Not truly parallel (single kernel thread executes all).
- 🧠 Used in: Old thread libraries (like early Java green threads).

2. **One-to-One Model**
- Each user thread maps to one kernel thread.
- True concurrency achieved on multicore CPUs.
- More flexible but higher resource overhead.
- 🧠 Used in: Windows, Linux (with pthreads).

3. **Many-to-Many Model**
- Many user threads mapped to many kernel threads.
- Efficient and flexible — combines benefits of both previous models.
- Allows multiple threads to run truly in parallel.
- 🧠 Used in: Solaris, modern Linux systems.


### Advantages of Multithreading
| **Advantage**                | **Description**                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------------ |
| **Increased Responsiveness** | Keeps the program responsive even during heavy operations.                           |
| **Resource Sharing**         | Threads share memory and resources efficiently.                                      |
| **Faster Execution**         | Tasks are divided and executed concurrently, improving throughput.                   |
| **Economy**                  | Creating threads is faster and requires fewer resources than creating new processes. |
| **Better CPU Utilization**   | Idle CPU time is reduced by allowing multiple threads to run.                        |


### Disadvantages of Multithreading
| **Disadvantage**               | **Description**                                                         |
| ------------------------------ | ----------------------------------------------------------------------- |
| **Complex Debugging**          | Hard to track bugs due to concurrent execution.                         |
| **Synchronization Issues**     | Race conditions can occur if threads access shared data simultaneously. |
| **Security Risks**             | A faulty thread can affect other threads of the same process.           |
| **Context Switching Overhead** | Frequent thread switching can reduce performance.                       |


### 🧠 Key Concepts

1. **Thread**
- The smallest unit of CPU execution.
- Shares memory and resources of its parent process.

2. **Multithreaded Process**
- A process that contains two or more threads running simultaneously.

3. **Concurrency vs Parallelism**
- Concurrency: Multiple threads make progress at the same time (not necessarily simultaneously).
- Parallelism: Threads run literally at the same time on different CPU cores.


## ⚙️ Concurrency vs Parallelism

Both concurrency and parallelism are terms used when we talk about executing multiple tasks, but they have different meanings and happen in different ways.

### 🧠 1. Concurrency
**Definition:**
Concurrency means dealing with multiple tasks at once, but not necessarily executing them at the exact same time.  
The CPU switches rapidly between tasks (using time slicing), giving the illusion that tasks are running simultaneously.  
```
🔹 Key idea: Multiple tasks make progress together, but only one runs at a given instant (on a single CPU core).
```

#### Example:
- Suppose your laptop has one CPU core.
- You are listening to music, downloading a file, and typing a document.
- The CPU switches quickly between these tasks, running a small part of each task in turn.
- To you, it seems like all are happening together — but in reality, only one task executes at a time.

#### Analogy:

Imagine a single chef cooking three dishes.  
He chops vegetables for one dish, then stirs the second, then seasons the third — doing small parts of each one quickly.  
He’s handling all three dishes concurrently.

### ⚡ 2. Parallelism
**Definition:** Parallelism means executing multiple tasks literally at the same time using multiple CPU cores or processors.
```
🔹 Key idea: Tasks are actually running simultaneously on different CPU cores.
```

#### Example:
- Suppose your computer has 4 CPU cores.
- One thread runs on Core 1, another on Core 2, another on Core 3, and so on.
- All four threads execute at the same exact time, each doing part of the work.

#### Analogy:
Now imagine four chefs, each working on a different dish at the same time — that’s parallelism.


### Main Differences Table 
| **Aspect**          | **Concurrency**                                                  | **Parallelism**                                       |
| ------------------- | ---------------------------------------------------------------- | ----------------------------------------------------- |
| **Meaning**         | Managing multiple tasks at once (not simultaneously)             | Running multiple tasks exactly at the same time       |
| **Execution**       | Tasks start, pause, and resume using time slicing                | Tasks execute simultaneously on multiple cores        |
| **CPU Requirement** | Can work on a single-core CPU                                    | Requires multi-core or multi-processor system         |
| **Goal**            | To handle multiple tasks efficiently and maintain responsiveness | To increase performance and speed by dividing work    |
| **Example**         | Handling multiple requests in a single-threaded server           | Running multiple threads on different CPU cores       |
| **Analogy**         | One chef cooking several dishes alternately                      | Several chefs cooking different dishes simultaneously |

```
🧠 In Short:
Concurrency = Dealing with many things at once.
Parallelism = Doing many things at once.

Hence, 
Multithreading provides concurrency,
and on multi-core systems, it can also achieve parallelism.
```


