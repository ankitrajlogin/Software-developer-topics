# Memory Management



## 🧠 What is Memory Management?

Memory Management is the part of the Operating System that controls and handles the computer's memory.

It decides:
- Which process gets memory
- How much memory it gets
- Where it is stored in memory
- When to free memory
- How to protect memory

Think of it like a manager in a hotel:

Guests = Processes

Rooms = Memory blocks

Manager allocates rooms, keeps track of who stays where, avoids fights, and cleans rooms after checkout.



## 🧠 What is Memory? (In Operating System)

Memory refers to the storage space where the computer keeps data and instructions that the CPU needs right now.  
Think of memory like a workspace on your desk:
- The larger the desk, the more books (programs) you can keep open.
- The smaller the desk, the fewer items you can keep at once.

But all memory is not the same.

There are two major types:
- Primary Memory (Main Memory)
- RAM, Cache, Registers
- Fast, expensive
- Used for running programs

Secondary Memory (Storage)
- SSD, HDD, Pen drive
- Slower, cheaper
- Used for storing files permanently

📌 Key Idea:
Primary memory is like your brain’s active thinking area.
Secondary memory is like your notebook where you store things permanently.


## 🔍 2. What is RAM? (Random Access Memory)

RAM is the main working memory of the computer.

✔️ Characteristics of RAM:

Volatile → data is lost when power goes OFF

Fast → much faster than SSD/HDD

Temporary → used only when computer is ON

Used for running programs

✔️ Why do we need RAM?

Because CPU is very fast and cannot work directly with slow storage (SSD/HDD).
So when you open a program:

The program is copied from SSD → RAM

CPU executes it from RAM

When you close the program, RAM is cleared

📌 RAM is like a classroom table where you keep books while studying.
SSD/HDD is like the school bag where books are stored long-term.


--- 

## 🟢 Reality: What Actually Happens  
🔹 ROM → RAM happens ONLY one time when system boots.

Steps:

CPU reads BIOS/UEFI instructions from ROM

BIOS loads bootloader

Bootloader loads OS into RAM

After this, ROM is not used anymore

So data is never removed from RAM and reloaded from ROM again, because ROM doesn’t contain application data.