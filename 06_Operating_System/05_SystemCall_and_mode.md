# System Calls & Modes

## Table of Contents
- [What is a System Call?](#what-is-a-system-call)
- [Example (read file)](#example-read-file)
- [User Mode vs Kernel Mode](#user-mode-vs-kernel-mode)
- [Types of System Calls](#types-of-system-calls)
- [Summary Table](#summary-table)
- [In Short](#in-short)

---

## 🧠 What is a System Call?

A System Call is the way through which a user program (application) interacts with the Operating System (OS).

When a program wants to perform a task that requires access to hardware resources (like reading from a file, allocating memory, or sending data through a network), it cannot directly access the hardware — it must request the OS to do it on its behalf.  
That request is made using a system call.


**System Call — The Bridge Between Modes**

`A System Call is a request made by a program in User Mode to access services provided by the Operating System Kernel.`

### ⚙️ Working:
- User process generates a System Call.
- CPU switches from User Mode → Kernel Mode.
- The OS performs the required operation (like file read/write, device access).
- CPU switches back to User Mode after completion.

## ⚙️ Example

Suppose a program wants to read a file:

- Your program (user mode) calls the function read().
- read() internally triggers a system call.
- The OS kernel switches from user mode → kernel mode.
- The kernel executes the operation (read from disk).
- The result is sent back to the user program.

This mode switching ensures security and controlled access to system resources.

---


## 🧩 Types of System Calls

System calls are generally divided into five major categories:

| Type                        | Purpose                    | Example System Calls                     |
| --------------------------- | -------------------------- | ---------------------------------------- |
| **Process Control**         | These system calls deal with process creation, execution, and termination.         | `fork()`, `exec()`, `exit()`, `wait()`   |
| **File Management**         | These system calls handle file operations like reading, writing, and closing files.          | `open()`, `read()`, `write()`, `close()` |
| **Device Management**       | Used to request or release a device, or perform I/O operations on devices.            | `ioctl()`, `read()`, `write()`           |
| **Information Maintenance** | Used to get or set system data.                | `getpid()`, `time()`, `uname()`          |
| **Communication (IPC)**     | Used for communication between processes on the same or different systems. | `pipe()`, `socket()`, `send()`, `recv()` |





**In Short:**
```
System calls act as a bridge between user programs and the kernel, allowing controlled access to the system’s resources like files, devices, and processes.
```

## Kernel 
A Kernel is the core part of an Operating System (OS) — it acts as a bridge between the hardware and user-level applications.
It manages all system resources such as:
- CPU (Processor scheduling)
- Memory (RAM allocation)
- Input/Output devices (Keyboard, disk, network, etc.)
- System calls (communication between user programs and hardware)

Simply put:
```
🧩 The kernel is the heart of the operating system — without it, your computer cannot function.
```



# 🧠 User Mode vs Kernel Mode

## 🔍 Overview
In a computer system, the **CPU operates in two modes** to ensure system protection and stability:

- **User Mode** → Where application programs run.
- **Kernel Mode** → Where the operating system runs with full privileges.

These two modes provide a **security boundary** between user applications and the core operating system.


## ⚙️ What is User Mode?

**User Mode** is the **restricted mode** in which normal application software runs.  
Programs running in this mode **cannot directly access hardware** or system memory outside their own space.

**Purpose: To protect the system from user programs crashing or corrupting memory.**

### 🧩 Key Characteristics:
- Limited access to system resources.
- Cannot execute privileged instructions (like I/O operations or memory management).
- If a process crashes in user mode, it does **not affect the OS**.
- When a program needs to perform system-level tasks (like file I/O), it **makes a system call** to switch into kernel mode.

### 💡 Example:
When you run a C program like:
```c
printf("Hello World");
```
The `printf()` function internally calls the `write()` system call to display output.  
This system call requires kernel privileges, so the CPU switches from **User Mode → Kernel Mode**.

---

## ⚙️ What is Kernel Mode?

**Kernel Mode** (also known as **Supervisor Mode**) is where the **Operating System (OS) kernel** executes.  
In this mode, the CPU has **complete access** to all system resources.

### 🧩 Key Characteristics:
- Full access to system memory and hardware.
- Executes **privileged instructions** (I/O, memory management, process control).
- A crash in kernel mode can **crash the entire system**.
- Used for critical tasks like context switching, scheduling, and interrupt handling.

**Purpose: To execute critical tasks like process scheduling, memory management, and I/O control.**

## 🧠 Example:
When the open() system call is made, the kernel actually:
1. Checks file permissions,
2. Loads file metadata from disk,
3. Returns a file descriptor to the program.

## 🔄 Transition Between User Mode and Kernel Mode

Whenever a **system call** is made (e.g., reading a file or writing output), the CPU switches from **User Mode** to **Kernel Mode**.

### 🪜 Step-by-Step Flow (Example: `read()` system call)

1. **Program Request (User Mode)**  
   A user program requests to read data using `read()`.

2. **System Call Trigger**  
   The function triggers a **trap** (software interrupt) to the kernel.

3. **Mode Switch: User → Kernel**  
   CPU switches from **User Mode** to **Kernel Mode** using a **context switch**.  
   The **Program Counter (PC)** now points to a kernel routine.

4. **Kernel Execution**  
   The kernel executes the `read` operation, interacting with hardware (like a disk).

5. **Mode Switch: Kernel → User**  
   After completion, control returns to the user process.  
   The CPU switches back to **User Mode** and the result is returned.



## 🧭 Comparison Table — User Mode vs Kernel ModeSummary Table

| Feature                | User Mode                    | Kernel Mode                                   |
| ---------------------- | ---------------------------- | --------------------------------------------- |
| **Access Level**       | Limited access               | Full access                                   |
| **Who runs here?**     | Applications / user programs | Operating System kernel                       |
| **Access to hardware** | Not allowed                  | Allowed                                       |
| **System resources**   | Restricted                   | Can manage memory, CPU, devices               |
| **Execution speed**    | Slower (due to restrictions) | Faster (direct control)                       |
| **Security**           | High (less risky)            | Risky (bugs can crash system)                 |
| **Example**            | Notepad, Chrome, Python code | OS kernel, device drivers, interrupt handlers |

<br>




## 🧩 Example — C Program and Execution Flow

Let’s take a simple program:
```
#include <stdio.h>

int main() {
    printf("Hello, Ankit!\n");
    return 0;
}
```

### 🔍 Step-by-Step Execution Flow
1. **Compilation**  
The code is compiled into machine instructions.


2. **Execution (User Mode)**  
When executed, the CPU is in User Mode.


3. **Function Call (printf)**  
The printf() function is part of the C library, which internally uses the write() system call to print data to the console.

```
write(1, "Hello, Ankit!\n", 14);
```

4. **Trap / Mode Switch**  
The system call write() triggers a trap (software interrupt).
The CPU switches from User Mode to Kernel Mode.

5. **Kernel Execution (Kernel Mode)**  
The OS kernel performs the actual write operation to the standard output (stdout) device.

6. **Return to User Mode**  
After completing, the CPU switches back to User Mode and continues execution of the remaining program.


### Visulal Flow 

```
User Mode:
  printf("Hello, Ankit!");
      │
      ▼
   [write()] → System Call
      │
      ▼
Trap Generated → CPU Switches to Kernel Mode
      │
      ▼
Kernel Mode:
   OS executes write → sends data to screen
      │
      ▼
Returns control to User Mode

```

## 🧱 Mode Bit Explanation

Every CPU has a mode bit that indicates which mode the system is currently running in:

| Mode Bit | Mode        | Access Type    |
| -------- | ----------- | -------------- |
| 1        | User Mode   | Limited access |
| 0        | Kernel Mode | Full access    |


When a Trap occurs (due to a system call), the mode bit changes from 1 → 0 (User → Kernel).
When execution is complete, it changes back from 0 → 1 (Kernel → User).

#### 🧩 In Short

> **User Mode** is for application programs with limited privileges.  
> **Kernel Mode** is for the operating system with full control over hardware.  
> The CPU switches between these modes using **system calls** or **interrupts** to ensure secure and efficient execution.

---
<br>

# 🧠 What Are Traps and Interrupts?

Both traps and interrupts are mechanisms that transfer control from the currently running process to the operating system kernel.
They are used when something needs immediate attention — like input/output completion, errors, or explicit service requests from a program.




## ⚙️ 1. Interrupt

### 🧩 Definition

An interrupt is a signal sent to the CPU by hardware or software that temporarily stops the current execution of instructions, allowing the CPU to handle an important task (called an Interrupt Service Routine, or ISR) before resuming the original process.

### ⚡ Example

When you:

- Press a key on the keyboard,
- Move the mouse, or
- A disk I/O operation finishes —

The hardware device sends an interrupt signal to the CPU.

### 🧠 How It Works

- CPU receives an interrupt signal.
- CPU pauses the current program (saves context).
- CPU jumps to a specific location in memory (the ISR handler in kernel mode).
- Once ISR completes, CPU restores context and resumes the interrupted task.

### 🔸 Types of Interrupts

- Hardware Interrupts – Generated by external devices (keyboard, mouse, etc.)
- Software Interrupts – Generated by a program (e.g., system calls using INT instruction in x86 architecture).

---

## ⚙️ 2. Trap

### 🧩 Definition

A trap is a software-generated interrupt caused by:

- An error during program execution (e.g., divide by zero), or
- A system call (when a user program explicitly requests kernel service).

Traps are synchronous — they occur as a direct result of an instruction executed by the CPU.

### 🧠 How It Works

- The user program executes an instruction that triggers a trap (like int 0x80 in Linux or a divide by zero).
- The CPU switches from user mode → kernel mode.
- The control is transferred to the trap handler (a specific function in the OS kernel).
- The kernel executes the requested service or handles the exception.
- The system returns control to the user process (if recoverable).

### ⚡ Example of a Trap
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("Hello, OS!\n");    // User-level code
    write(1, "System Call!\n", 13); // Causes a trap (system call)
    return 0;
}
```

Here’s what happens:

- write() is a system call — it requests the OS to write data to stdout.
- The CPU triggers a trap instruction internally.
- Control switches from user mode → kernel mode.
- The OS executes the write() system call inside the kernel.
- After completion, control returns to the program (kernel → user mode).

---

### ⚔️ Trap vs Interrupt — Key Differences

| Feature   | Trap                                                   | Interrupt                              |
|-----------|--------------------------------------------------------|----------------------------------------|
| Source    | Generated by software (user program or CPU)            | Generated by hardware or external devices |
| Timing    | Synchronous (depends on current instruction)           | Asynchronous (can occur anytime)       |
| Example   | System call, divide by zero, invalid memory access     | Keyboard press, I/O completion, timer  |
| Handler   | Trap Handler (in Kernel)                               | Interrupt Service Routine (ISR)        |
| Mode Switch | Always causes a switch to kernel mode                 | Also causes a switch to kernel mode    |
| Purpose   | Error handling, request OS service                     | Handle external hardware events        |

---

### 🧩 Combined View — Execution Flow

Here’s what happens during function execution and system calls:

- A user program executes a system call (like read() or write()).
- CPU executes a trap instruction → switches to kernel mode.
- OS kernel handles the request.
- After completion, CPU returns to user mode.
- If during execution an external event (like I/O completion) occurs, an interrupt is raised → CPU pauses user program → executes ISR → resumes program.

### 💡 Summary
- Trap → Software-generated interrupt (e.g., system call or error).
- Interrupt → Hardware or software signal to CPU (asynchronous).
- Both cause context switch and mode switch from user → kernel.
- After handling, CPU returns to user mode and resumes normal execution.