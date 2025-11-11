# Operating System Notes

## 📑 Navigation
| Topic | Link |
|-------|------|
| **Current: Basic Concepts** | [Basic Concepts](01_basic.md) |
| **Next: Types of OS** | [Types of Operating Systems](02_types_of_operating_system.md) |


---

# Operating System - Complete Notes

## Overview
An Operating System (OS) is a system software that acts as a bridge between the user and the computer hardware.
It manages all hardware and software resources, provides services to application programs, and ensures that the system operates efficiently and securely.

🧩 In simple terms:
```
The OS is like a manager of the computer system — it controls all resources (CPU, Memory, Devices, Files) and gives each program what it needs.
```

# Functions of Operating System
## 🧠 1. Resource Management

The OS acts as a resource manager — efficiently controlling and allocating system resources such as CPU, memory, and input/output devices.

### a) CPU Management
The OS decides which process runs on the CPU, for how long, and in what order (scheduling).  
Performs context switching to share CPU time between multiple processes.

Ensures fair CPU time distribution among processes and prevents starvation.

👉 Example: While you stream music and browse the web, the OS rapidly switches CPU control between both, giving an illusion of simultaneous execution.

### b) Memory Management

Allocates and deallocates memory for running processes.

Keeps track of used and free memory areas.

Prevents one process from accessing another’s memory space.

Uses techniques like paging, segmentation, and virtual memory for efficiency.

👉 Example: Opening multiple applications — each gets its own memory segment without interfering with others.

## 2. Device Management

The OS manages communication between the CPU and all I/O devices (keyboard, printer, mouse, etc.).

Uses device drivers to translate OS commands into hardware instructions.

Handles I/O requests, queues them, and controls device access.

Ensures efficient and conflict-free use of devices.

👉 Example: When you print a file, the OS manages the print queue so multiple applications can share the printer safely.

## 3. User Interface (UI)

The OS provides a way for users to interact with the computer system.

### a) Graphical User Interface (GUI)

Uses windows, icons, buttons, and menus.

Easy for non-technical users.

Examples: Windows OS, macOS, Ubuntu (GNOME).

### b) Command Line Interface (CLI)

Users type text commands to perform tasks.

Preferred by developers and system administrators.

Examples: Linux Terminal, Windows CMD.

👉 Example: You can open a file using a GUI (double-click) or CLI (cat file.txt).

##  4. File System Management

The OS organizes and controls how data is stored, retrieved, and maintained on storage devices.

Manages files, folders, and permissions.

Handles operations like create, read, write, modify, delete.

Maintains metadata (file size, creation date, ownership).

Provides abstraction — users see files, not raw disk blocks.

👉 Example: The OS lets you save a file as report.docx rather than manually writing binary data to a disk.

## 🔒 5. Security and Access Control

The OS ensures protection and privacy of system resources and user data.

Implements user authentication (passwords, biometrics, etc.).

Enforces access permissions for files and resources.

Provides firewalls, encryption, and process isolation to prevent unauthorized access.

Detects and recovers from errors and malware threats.

👉 Example: You can’t open another user’s personal folder without proper permissions — enforced by the OS.

##  6. Application Support

The OS provides a platform for running applications and acts as a bridge between apps and hardware.

Offers APIs (Application Programming Interfaces) and system calls for software development.

Handles program execution, resource allocation, and error handling.

Manages background services required by applications.

👉 Example: A web browser uses OS services to access the network, memory, and file system — without directly communicating with hardware.

## 7. Networking

Modern operating systems include built-in networking capabilities for communication between systems.

Supports network protocols (like TCP/IP) for internet connectivity.

Manages network interfaces, data transfer, and socket communication.

Provides services like file sharing, remote login, and resource access.

👉 Example: When you browse a website, the OS manages all underlying data transfer between your system and the web server.

## 8. Error Detection and Response

The OS constantly monitors the system for hardware and software errors.

Detects CPU, memory, or I/O device malfunctions.

Handles program errors and alerts users or logs them.

Ensures the system remains stable even when one process fails.

👉 Example: If an application crashes, the OS isolates it without affecting others.

## ⚡ 9. Performance and Efficiency

The OS ensures that all components work efficiently together.

Uses caching, buffering, and scheduling to improve performance.

Optimizes resource usage to avoid waste.

Ensures responsiveness and system stability.

## Summary

### ✅ In Summary

| Function | Purpose / Role of OS |
|----------|----------------------|
| Resource Management | Manages CPU, memory, and I/O efficiently. |
| Device Management | Controls and coordinates all hardware devices. |
| User Interface | Provides GUI and CLI for easy interaction. |
| File System Management | Organizes and controls data storage and access. |
| Security & Access Control | Protects system and user data from unauthorized access. |
| Application Support | Provides APIs and system calls for running applications. |
| Networking | Enables communication and data exchange between systems. |
| Error Detection | Identifies and handles hardware/software errors. |
| Performance Optimization | Ensures efficient and stable system operation. |


# 🧱 3. Components of Operating System
The OS has several main components that work together:
| Component               | Description                                                                                                          |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **1. Kernel**           | The core part of the OS that directly interacts with hardware. It manages CPU, memory, and devices.                  |
| **2. Shell**            | The command interpreter that takes input from the user and passes it to the OS. Example: Bash shell, Command Prompt. |
| **3. File System**      | Defines how files are named, stored, and organized on disks.                                                         |
| **4. Device Drivers**   | Programs that allow the OS to communicate with hardware (printer, disk, etc.).                                       |
| **5. System Utilities** | Tools that help manage, analyze, and optimize the system (e.g., Task Manager, Disk Cleanup).                         |
| **6. System Calls**     | Interface that allows user programs to request services from the kernel (e.g., read(), write(), open()).             |

## 🧩 4. Architecture of Operating System
```
+---------------------------------------------------+
|               User Applications                   |
| (Games, Browsers, Media Players, Editors, etc.)   |
+---------------------------------------------------+
|                System Programs                    |
| (Compilers, Utilities, Libraries)                 |
+---------------------------------------------------+
|             Operating System (OS)                 |
|   ├── User Interface (CLI / GUI)                  |
|   ├── System Calls                                |
|   ├── Kernel                                      |
|   ├── File System                                 |
|   ├── Device Drivers                              |
+---------------------------------------------------+
|                    Hardware                       |
|   (CPU, Memory, Disk, I/O Devices)                |
+---------------------------------------------------+
```


> ### Kernel – The Core of the OS
>The Kernel is the core part of an operating system that has full control over the system.  
>It interacts directly with the hardware and provides low-level services such as process control, memory management, device management, and file system access.

## Final Summary Line
🧭 Final Summary Line

💡 We use an Operating System to make the computer system functional, secure, efficient, and user-friendly — managing hardware, running applications, and enabling communication between users and machines.