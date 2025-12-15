# ⭐ Normalization in DBMS

---

## Table of Contents
- [1. Normalization](#1-normalization)
- [2. Functional Dependency (FD)](#2-functional-dependency-fd)
- [3. Anomalies](#3-anomalies)
- [4. Types of Normal Forms](#4-types-of-normal-forms)
  - [Unnormalized Table (UNF)](#unnormalized-table-unf)
  - [1NF — First Normal Form](#1nf--first-normal-form)
  - [2NF — Second Normal Form](#2nf--second-normal-form)
  - [3NF — Third Normal Form](#3nf--third-normal-form)
  - [BCNF — Boyce-Codd Normal Form](#bcnf--boyce-codd-normal-form)
  - [4NF — Fourth Normal Form](#4nf--fourth-normal-form)
- [Final Normalized Tables (4NF)](#final-normalized-tables-4nf)
- [Short Summary](#short-summary)

---

## ⭐ 1. Normalization

### Definition (Simple Explanation)

**Normalization** is a process in DBMS used to organize data in a database to:
- Remove redundancy (duplicate data).
- Avoid anomalies.
- Ensure consistency.

### Why Normalization?

1. To reduce repeated data.  
2. To make the database clean and efficient.  
3. To reduce chances of update errors.  
4. To save storage.  
5. To improve data integrity.

---

## ⭐ 2. Functional Dependency (FD)

### Definition

A **functional dependency** is a relationship where one attribute uniquely determines another attribute.

### Notation

```
A → B
```
**Means:** A determines B (or value of B depends on value of A).

### Example

In a **STUDENT** table:
```
Roll_No → Name
Roll_No → Course
```
**Means:** If you know `Roll_No`, you can find `Name` and `Course`.

---

### Types of Functional Dependency

| **Type**       | **Explanation**                              | **Example**                                      |
|----------------|----------------------------------------------|-------------------------------------------------|
| **Full FD**    | B depends fully on A, not on part of it.     | (Roll_no, Subject) → Marks                      |
| **Partial FD** | B depends on part of candidate key.          | (Roll_no, Subject) → Student_Name (depends only on Roll_no) |
| **Transitive FD** | A → B and B → C, then A → C.              | Roll_no → Dept_no → Dept_name                   |

---

## ⭐ 3. Anomalies

When a table is not normalized, it causes errors called **anomalies**.

### Types of Anomalies

1. **Insert Anomaly**  
   - You cannot insert data because some other data is missing.  
   **Example:** Cannot insert a new course unless a student takes that course.

2. **Update Anomaly**  
   - Same data stored in many places → updating one place creates inconsistencies.  
   **Example:** Teacher phone updated in one row but not in others.

3. **Delete Anomaly**  
   - Deleting one row causes unwanted loss of data.  
   **Example:** Deleting the last student in a course → course data also gets lost.


```
Due to these anomalies, DB size increases and DB performance become very slow.
To rectify these anomalies and the effect of these of DB, we use Database optimisation technique called
```

## ⭐ 4. Types of Normal Forms

The main normal forms are:
1. **1NF**  
2. **2NF**  
3. **3NF**  
4. **BCNF**  
5. **4NF**

---

### 🌟 Unnormalized Table (UNF)

Assume we have this table:

**STUDENT_COURSE Table**

| **StudentID** | **StudentName** | **Course** | **Instructor** | **PhoneNumbers** |
|---------------|-----------------|------------|----------------|------------------|
| 101           | Rahul           | DBMS       | Sharma         | 9876, 8888      |
| 101           | Rahul           | OS         | Verma          | 9876, 8888      |
| 102           | Aman            | DBMS       | Sharma         | 9999            |

### Problems:
1. **Multivalued attribute:** `PhoneNumbers`.  
2. **Repeated data:** `StudentName`, `PhoneNumbers` repeated for the same student.  
3. **Anomalies likely:** Update, insert, delete issues.

---

### ✅ 1NF — First Normal Form

#### Rule of 1NF:
1. All values must be **atomic** (single value, not lists).  
2. No repeating groups.

#### Fix:
Split multivalued phone numbers into separate rows.

**1NF Table**

| **StudentID** | **StudentName** | **Course** | **Instructor** | **PhoneNumber** |
|---------------|-----------------|------------|----------------|-----------------|
| 101           | Rahul           | DBMS       | Sharma         | 9876           |
| 101           | Rahul           | DBMS       | Sharma         | 8888           |
| 101           | Rahul           | OS         | Verma          | 9876           |
| 101           | Rahul           | OS         | Verma          | 8888           |
| 102           | Aman            | DBMS       | Sharma         | 9999           |

💡 **Now the table is in 1NF.**

---

### ✅ 2NF — Second Normal Form

#### Rule of 2NF:
1. Must be in **1NF**.  
2. No **Partial Functional Dependency** (Non-key attribute must depend on the whole primary key).

**In 2NF, no attribute (column) should be partially dependent on the primary key.**

#### Composite Primary Key in our table:
```
(StudentID, Course, PhoneNumber)
```

#### Check Partial FDs:
- `StudentName` depends only on `StudentID`, not on `Course` or `PhoneNumber` → Partial FD.  
- `Instructor` depends only on `Course`, not on `StudentID` → Partial FD.  

❌ **Violates 2NF.**

#### Fix:
Split table into 3 tables:

**⭐ Table 1: STUDENT**

| **StudentID** | **StudentName** |
|---------------|-----------------|
| 101           | Rahul           |
| 102           | Aman            |

**⭐ Table 2: COURSE_INSTRUCTOR**

| **Course** | **Instructor** |
|------------|----------------|
| DBMS       | Sharma         |
| OS         | Verma          |

**⭐ Table 3: STUDENT_COURSE_PHONE**

| **StudentID** | **Course** | **PhoneNumber** |
|---------------|------------|-----------------|
| 101           | DBMS       | 9876           |
| 101           | DBMS       | 8888           |
| 101           | OS         | 9876           |
| 101           | OS         | 8888           |
| 102           | DBMS       | 9999           |

💡 **Now the schema is in 2NF.**

---

### ✅ 3NF — Third Normal Form

#### Rule of 3NF:
1. Must be in **2NF**.  
2. No **Transitive Dependencies** (A → B and B → C should not exist).

#### Check transitive dependencies:
- `Course → Instructor`.  
- `StudentID → StudentName`.

These are OK because:
- In the **STUDENT** table: No transitive dependency.  
- In **COURSE_INSTRUCTOR**: `Instructor` depends ONLY on `Course`.  
- In **STUDENT_COURSE_PHONE**: No extra attributes.

💡 **Now schema is in 3NF.**

---

### ✅ BCNF — Boyce-Codd Normal Form

#### Rule of BCNF:
1. Must be in 3NF
2. For every functional dependency A → B:  
👉 A must be a **superkey**.

- **A superkey is any column or group of columns that can uniquely identify a row in a table.**

#### Check FD in each table:

1. **Table 1: STUDENT**  
   FD: `StudentID → StudentName`  
   ✔ `StudentID` is primary key → OK.

2. **Table 2: COURSE_INSTRUCTOR**  
   FD: `Course → Instructor`  
   ✔ `Course` is primary key → OK.

3. **Table 3: STUDENT_COURSE_PHONE**  
   Key = `(StudentID, Course, PhoneNumber)`  
   No FD violates BCNF.

#### **Another Example** : 

✅ Simple Example (Most Common BCNF Example)        

❌ **Table NOT in BCNF**
| Course | Teacher | Room |
| ------ | ------- | ---- |
| DBMS   | Raman   | 201  |
| OS     | Ravi    | 202  |
| DBMS   | Raman   | 201  |


#### Functional Dependencies     
**Course** → Room (every course has a fixed room)       
**Room** → Teacher (every room has a fixed teacher)

Candidate key = Course      
(because course uniquely identifies the row)

**But look carefully:**     
**Room** → Teacher violates BCNF        
because Room is NOT a superkey      
(room alone cannot uniquely identify the row)

Thus, table is NOT in BCNF.

⭐ **How to Fix (Convert to BCNF)**     
#### Decompose into two tables:      
**Table 1: Course → Room**
| Course | Room |
| ------ | ---- |
| DBMS   | 201  |
| OS     | 202  |

**Table 2: Room → Teacher**
| Room | Teacher |
| ---- | ------- |
| 201  | Raman   |
| 202  | Ravi    |

Now:

In Table 1 → Course is the key  
In Table 2 → Room is the key

👉 **All determinants are superkeys → BCNF achieved.**

---

### ✅ 4NF — Fourth Normal Form

#### Rule of 4NF:
- It is already in BCNF
- It does not contain any multi-valued dependencies (MVDs)

<br>
Removes Multi-valued Dependencies (MVD) .         

```
A →→ B (independent multi-valued attributes)
```

**Multi-Valued Dependency (MVD)** : Occurs when one attribute in a table determines multiple independent values of another attribute, independently of other attributes.

#### Check table 3 (STUDENT_COURSE_PHONE):
| **StudentID** | **Course** | **Phone** |
|---------------|------------|-----------|
| 101           | DBMS       |  9876     |
| 101           | DBMS       |  8888     |
| 101           | OS         |  9876     |
| 101           | OS         |  8888     |
| 102           | DBMS       |  9999     |


Here:
- A student has multiple phones.  
- A student takes multiple courses.  

👉 These two are **independent multi-valued facts**:
- `Student →→ Phone`.  
- `Student →→ Course`.

This violates 4NF.

#### Fix:
Split into two tables:

**⭐ STUDENT_PHONE**

| **StudentID** | **PhoneNumber** |
|---------------|-----------------|
| 101           | 9876           |
| 101           | 8888           |
| 102           | 9999           |

**⭐ STUDENT_COURSE**

| **StudentID** | **Course** |
|---------------|------------|
| 101           | DBMS       |
| 101           | OS         |
| 102           | DBMS       |

💡 **Now the database is in 4NF.**

---

## 🎉 FINAL NORMALIZED TABLES (4NF)

1. **STUDENT**

| **StudentID** | **StudentName** |
|---------------|-----------------|
| 101           | Rahul           |
| 102           | Aman            |

2. **COURSE_INSTRUCTOR**

| **Course** | **Instructor** |
|------------|----------------|
| DBMS       | Sharma         |
| OS         | Verma          |

3. **STUDENT_PHONE**

| **StudentID** | **Phone** |
|---------------|-----------|
| 101           | 9876      |
| 101           | 8888      |
| 102           | 9999      |

4. **STUDENT_COURSE**

| **StudentID** | **Course** |
|---------------|------------|
| 101           | DBMS       |
| 101           | OS         |
| 102           | DBMS       |

---

## ⭐ Short Summary (Very Easy)

| **NF**   | **What is removed?**           | **Fix**                         | **Meaning (Layman Explanation)**                                                                                      |
| -------- | ------------------------------ | ------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **1NF**  | Multi-valued, repeating groups | Make values atomic              | Every cell must have only **one value**, not lists, not repeated items                                                |
| **2NF**  | Partial dependency             | Split based on full key         | A non-key column must **depend on the entire primary key**, not just part of it                                       |
| **3NF**  | Transitive dependency          | Split tables further            | A non-key column must depend **only on the primary key**, not on another non-key column                               |
| **BCNF** | Non-superkey dependency        | Ensure LHS of FD is key         | Every determinant must be a **candidate key**; no column should control another unless it is a key                    |
| **4NF**  | Multi-valued dependency        | Separate independent attributes | A table should not contain **two independent multi-valued facts** about the same key                                  |
| **5NF**  | Join dependency                | Break into smallest tables      | A table should not have hidden dependencies that require **multiple tables to be recombined** to get full information |


---


## Quick Meaning
| Term                              | Meaning                                                                          |
| --------------------------------- | -------------------------------------------------------------------------------- |
| **Partial dependency**            | A column depends on only **part of a composite key**                             |
| **Transitive dependency**         | A column depends on another **non-key column**, not directly on primary key      |
| **Non-superkey dependency**       | A non-key attribute determines other attributes                                  |
| **Multi-valued dependency (MVD)** | A record contains **two or more independent lists**                              |
| **Join dependency**               | Table can only be reconstructed correctly after joining **more than two tables** |
