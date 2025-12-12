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

---

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
For every functional dependency A → B:  
👉 A must be a **superkey**.

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

💡 **Tables are now in BCNF.**

---

### ✅ 4NF — Fourth Normal Form

#### Rule of 4NF:
Removes **Multi-valued Dependencies (MVD)**.  
```
A →→ B (independent multi-valued attributes)
```

#### Check table 3 (STUDENT_COURSE_PHONE):
| **StudentID** | **Course** | **Phone** |
|---------------|------------|-----------|

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

| **NF**   | **Removes**               | **Example Problem**                     |
|----------|---------------------------|-----------------------------------------|
| **1NF**  | Multi-valued attributes   | Phone = 9876,8888                       |
| **2NF**  | Partial dependencies      | StudentName depends only on StudentID   |
| **3NF**  | Transitive dependencies   | Course → Instructor                     |
| **BCNF** | Non-superkey determinants | Strict 3NF                              |
| **4NF**  | Multi-valued dependency   | Student →→ Phone, Course                |

---
