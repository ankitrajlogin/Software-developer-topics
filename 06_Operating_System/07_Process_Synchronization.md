# Process Synchronization

Process Synchronization is a mechanism that ensures that multiple processes (or threads) can execute concurrently without interfering with each other and without leading to inconsistent data.  
When several processes access and manipulate shared data, synchronization ensures that only one process modifies the shared resource at a time.

**Key Idea:**      
`Synchronization prevents race conditions and ensures data consistency.`

---

## ⚙️ Why Synchronization is Needed?

When two or more processes execute concurrently and share data, inconsistencies can occur if proper control is not applied.

### Example: Race Condition

```c
// Shared variable
int counter = 0;

// Process P1
counter++;

// Process P2
counter++;
```

**Problem:**

If both P1 and P2 execute simultaneously, the final value of counter may **not be 2** due to **race conditions**.

- Each process may read the same old value before the other updates it.
- Without synchronization, we cannot guarantee data consistency.

**What should happen:**
- P1 reads counter (0), increments to 1, writes back.
- P2 reads counter (1), increments to 2, writes back.
- Final result: counter = 2

**What actually happens (without synchronization):**
- P1 reads counter (0)
- P2 reads counter (0)  ← reads the same old value
- P1 increments and writes counter = 1
- P2 increments and writes counter = 1  ← overwrites P1's work
- Final result: counter = 1  ❌ (incorrect!)

This is a **race condition** — the final result depends on the timing/order of execution, which is unpredictable.

---

## Important Concepts

### 1. Independent Process:  
Does **not share** data, resources, or state with any other process. Its execution is **not affected** by other processes.             
        Example: Text editor, Calculator, Media player   

### 2. Cooperative Process:  
**Shares data** or resources with other processes and hence **depends** on them. Its execution can **affect or be affected** by others. 
Example: Web browser with multiple tabs sharing cache, Producer–Consumer system 


### 3. Deadlock:
A Deadlock is a situation in an Operating System where two or more processes are waiting indefinitely for resources that are held by each other.   

As a result, none of the processes can proceed further, and the system gets stuck. 


### 4. Race Condition
A race condition occurs when multiple processes access and modify shared data concurrently, and the final result depends on the sequence or timing of their execution.

Example: 
If one process reads and modifies a shared variable while another does the same, the outcome becomes unpredictable.

`👉 Goal of synchronization: Prevent race conditions by ensuring that only one process at a time can access critical resources.`


### 5. Critical Section
A Critical Section is a part of a program where shared resources (like variables, data structures) are accessed and modified.  
To prevent race conditions, only one process should be allowed to execute in its critical section at any given time.

`👉 Critical Section Problem: Ensure mutual exclusion, progress, and bounded waiting among processes accessing critical sections.`

**Objective of Critical Section Problem:**
1. Mutual Exclusion — Only one process executes in its critical section at a time.
2. Progress — If no process is in its critical section, only those processes that want to enter should participate in deciding who enters next.
3. Bounded Waiting — A process must not wait indefinitely to enter its critical section.

### 6. Starvation
Starvation (also known as Indefinite Blocking) occurs when a process waits indefinitely to gain access to a resource or CPU, even though the resource becomes available multiple times.

This happens because some processes continuously get preference over others due to the scheduling policy or resource allocation method used.

**✅ Solutions to Starvation**
| Solution                   | Description                                                                                                  |
| -------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **1. Aging**               | Gradually **increase the priority** of waiting processes over time, so they eventually get CPU time.         |
| **2. Fair Scheduling**     | Use scheduling algorithms like **Round Robin**, **Multilevel Feedback Queue**, or **Fair Share Scheduling**. |
| **3. Resource Management** | Ensure that resource allocation follows **fairness policies** and avoids bias.                               |




---

## 🧩 Classical Synchronization Problems

Let's discuss some famous OS synchronization problems, their challenges, and standard solutions.

### 🖨️ 1. Printer Spooler Problem

#### 🔸 Problem Description:

A printer spooler is a buffer (queue) where print jobs are stored before being printed.

- Multiple users/processes can submit print jobs simultaneously.
- The spooler can only handle one job at a time, and jobs must be printed in order (FIFO).

#### ⚠️ Problem:

If multiple processes access the spooler directory concurrently:

- Two processes might get the same slot number.
- One process might overwrite another's print job.
- This leads to race conditions and data inconsistency.

#### ✅ Solution using Synchronization:

Use mutual exclusion (via semaphores or mutex locks) to ensure only one process can modify the spooler at a time.

```c
semaphore mutex = 1;  // Binary semaphore for mutual exclusion

Process() {
    wait(mutex);      // Enter critical section
    addJobToSpooler();
    signal(mutex);    // Exit critical section
}
```

**Result:** Each process safely adds its print job to the spooler without overwriting others.

---

### 🍞 2. Producer–Consumer Problem (Bounded Buffer Problem)

#### 🔸 Problem Description:

- The Producer creates data items and stores them in a shared buffer.
- The Consumer takes data items from that buffer.
- The buffer has limited capacity.

#### ⚠️ Issues:

- Producer must wait if the buffer is full.
- Consumer must wait if the buffer is empty.
- Access to the buffer must be mutually exclusive.

#### ✅ Solution using Semaphores:

```c
semaphore mutex = 1;    // For mutual exclusion
semaphore empty = n;    // Count of empty slots
semaphore full = 0;     // Count of full slots

Producer() {
    while (true) {
        produce_item();
        wait(empty);     // Wait if buffer is full
        wait(mutex);     // Enter critical section
        insert_item();   // Add item to buffer
        signal(mutex);   // Leave critical section
        signal(full);    // One more full slot
    }
}

Consumer() {
    while (true) {
        wait(full);      // Wait if buffer empty
        wait(mutex);     // Enter critical section
        remove_item();   // Consume item
        signal(mutex);   // Leave critical section
        signal(empty);   // One more empty slot
        consume_item();
    }
}
```

**Result:** Ensures proper coordination — no buffer overflow or underflow.

---

### 📖 3. Reader–Writer Problem

#### 🔸 Problem Description:

- Multiple readers can read data simultaneously.
- Only one writer can modify data at a time.
- While a writer is writing, no reader or other writer should access the data.

#### ⚠️ Goal:

Maintain data consistency between readers and writers.

#### ✅ Solution using Semaphores:

```c
semaphore mutex = 1;      // Protect readCount
semaphore wrt = 1;        // Control writer access
int readCount = 0;

Reader() {
    wait(mutex);
    readCount++;
    if (readCount == 1)
        wait(wrt);        // First reader locks writer
    signal(mutex);

    // Reading section
    read_data();

    wait(mutex);
    readCount--;
    if (readCount == 0)
        signal(wrt);      // Last reader unlocks writer
    signal(mutex);
}

Writer() {
    wait(wrt);
    write_data();
    signal(wrt);
}
```

**Result:** Multiple readers can read together, but writing is exclusive.

---

### 🍽️ 4. Dining Philosophers Problem

#### 🔸 Problem Description:

- Five philosophers sit around a table.
- Each philosopher alternates between thinking and eating.
- There are five forks (shared resources), and a philosopher needs two forks to eat.

#### ⚠️ Issues: 

If all philosophers pick up their left fork first, deadlock occurs (each holds one fork and waits forever for the other). 

#### ✅ Solution using Semaphores:

```c
semaphore fork[5] = {1, 1, 1, 1, 1};

Philosopher(i) {
    while (true) {
        think();
        wait(fork[i]);               // Pick left fork
        wait(fork[(i+1)%5]);         // Pick right fork
        eat();
        signal(fork[i]);             // Put down left fork
        signal(fork[(i+1)%5]);       // Put down right fork
    }
}
```

#### 💡 Deadlock Prevention:

To avoid deadlock, one philosopher picks up forks in reverse order:

```c
if (i == 4) {  // last philosopher
    wait(fork[(i+1)%5]);
    wait(fork[i]);
} else {
    wait(fork[i]);
    wait(fork[(i+1)%5]);
}
```

**Result:** Ensures mutual exclusion and avoids deadlock.

---

### 💈 5. Sleeping Barber Problem

#### 🔸 Problem Description:

- A barber shop has one barber, one barber chair, and n waiting chairs.
- If there are no customers, the barber sleeps.
- When a customer arrives:
  - If the barber is free, he gets a haircut.
  - If chairs are available, he waits.
  - If no chair is free, the customer leaves.

#### ⚠️ Issues:

Need synchronization between barber and customers to prevent missed signals or over-occupation.

#### ✅ Solution using Semaphores:

```c
semaphore customers = 0;
semaphore barber = 0;
semaphore mutex = 1;
int waiting = 0;

Barber() {
    while (true) {
        wait(customers);   // Sleep if no customer
        wait(mutex);
        waiting--;
        signal(barber);    // Barber ready
        signal(mutex);
        cut_hair();
    }
}

Customer() {
    wait(mutex);
    if (waiting < n) {
        waiting++;
        signal(customers); // Notify barber
        signal(mutex);
        wait(barber);      // Wait for barber
        get_haircut();
    } else {
        signal(mutex);     // Leave if full
        leave_shop();
    }
}
```

**Result:** Proper coordination — barber sleeps when idle, customers are served in order.

---

## 🧮 Summary Table of Classical Problems

| Problem | Shared Resource | Challenge | Solution Tool |
|---------|-----------------|-----------|------------------|
| Printer Spooler | Print queue | Avoid overwriting jobs | Mutex/Semaphore |
| Producer–Consumer | Buffer | Avoid overflow/underflow | Counting Semaphores |
| Reader–Writer | Shared file/database | Allow multiple readers, one writer | Semaphores |
| Dining Philosophers | Forks | Avoid deadlock/starvation | Semaphores + Ordering |
| Sleeping Barber | Barber chair/waiting chairs | Synchronize barber and customers | Semaphores |

---

## 🧠 Conclusion

- Process Synchronization ensures safe, consistent, and predictable access to shared resources.
- It avoids race conditions, deadlocks, and starvation.
- Semaphores, mutex locks, and monitors are essential synchronization primitives.
- Classical problems like Producer–Consumer, Dining Philosophers, and Reader–Writer demonstrate the practical importance of synchronization in OS design.


