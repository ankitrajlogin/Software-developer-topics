# Database Management System (DBMS) — Basic Concepts

## Introduction

A **Database Management System (DBMS)** is software that allows users to define, create, maintain, and control access to a database. It acts as an interface between the user and the database, ensuring data is organized and easily accessible.

---


## ✅ 1.  Data

**Data** is raw facts and figures that have no meaning by themselves.   
- *Data is like **unprocessed information***.

### Example Table

| Data      | Meaning                |
|-----------|------------------------|
| “25”      | Could be age, price, marks |
| “Patna”   | Could be birthplace, location |


### Types of Data   

**a. Quantitative**             
&nbsp; &nbsp; &nbsp; i. Numerical form   
&nbsp; &nbsp; &nbsp; ii. Weight, volume, cost of an item.

**b. Qualitative**             
&nbsp; &nbsp; &nbsp; i. Descriptive, but not numerical.  
&nbsp; &nbsp; &nbsp; ii. Name, gender, hair color of a person.


---

## ✅ 2. Information

**Information** is processed and meaningful data.  
When data is arranged or interpreted so it gives meaning, it becomes **information**.

- If **data** is the raw material, **information** is the final useful product.

#### Example:
- **Data:** “Ankit, 25, Patna”
- **Information:** “Ankit is 25 years old and lives in Patna.”

Now the data gives clear meaning → it becomes **information**.

### Example Table

| Data           | After Processing       | Information                  |
|----------------|------------------------|------------------------------|
| 35, 40, 50     | Calculate average      | “Average score is 41.6”      |

---


### 📌 Difference Between Data and Information

| Data          | Information                    |
| ------------- | ------------------------------ |
| Raw facts     | Processed facts                |
| No meaning    | Meaningful                     |
| Unorganized   | Organized                      |
| Example: “50” | Example: “Temperature is 50°C” |

---

## ✅ 3. What is a Database?

A **Database** is an organized collection of data that can be easily accessed, managed, and updated.  
Example: A library catalog, an employee record system, or an online shopping inventory.

###  Example of a Database      
**Student Database** : Contains roll no, name, marks, attendance.   
**Banking Database** : Contains customer accounts, transactions.    
**Hospital Database** : Contains patient records, doctor details.   
**E-commerce Database** : Contains products, orders, users. 

 

### Why Do We Need a Database?  
A database helps to:    
1. **Store large data safely**.
2. **Avoid duplicate data**.
3. **Maintain accuracy (data integrity)**.
4. **Retrieve data quickly**.
5. **Manage multi-user access**.
6. **Ensure security**.



##  Characteristics of a Database

| Feature           | Explanation                                         |
|-------------------|-----------------------------------------------------|
| **Organized Data**| Stored in a structured format (tables, documents, etc.). |
| **Shared**        | Multiple users can access the data.                 |
| **Consistent**    | Same data everywhere; no mismatch.                  |
| **Secure**        | Only authorized users can access.                   |
| **Scalable**      | Can handle huge data growth.                        |
| **Reliable**      | Backup & recovery features.                         |


## Types of Databases
Based on structure, usage, location, and data format, databases are classified into several types.

We’ll cover all major types important for exams and understanding DBMS.

**1. Relational Database (SQL)**
- Stores data in tables (rows & columns)
- Uses SQL for queries  
**Example**: MySQL, PostgreSQL, Oracle

**2. Non-Relational Database (NoSQL)**
- Stores data in documents, key-value pairs, graphs, or columns
- Flexible structure    
**Example**: MongoDB, Cassandra, Redis

**3. Centralized Database**
- Data stored in one central location.  
**Example:** Railway Reservation System.

**4. Distributed Database**
- Data stored in multiple locations but connected.  
**Example:** Google search data servers.

**5. Cloud Database**
- Stored and accessed on cloud platforms.  
**Example:** AWS RDS, Firebase, Azure SQL.

**6. Object-Oriented**
- Stores data as objects (like in OOP languages).

**6. Hierarchical Database**
- Data stored in a tree-like structure (parent–child relationship). 
**Examples:** IBM IMS

**7. Graph Database**   
- Uses nodes and edges to represent data and relationships.   
- Examples:Neo4j, Amazon Neptune

**8. Time-Series Database**     
- Stores data that changes with time (timestamps).        
- Examples: InfluxDB, TimescaleDB

**9. Spatial Database**     
- Stores geographical / location-based data.      
- Used for: Maps (Google Maps) , GIS systems      
- Examples: PostGIS, Oracle Spatial

**10. Network Database**
- Similar to hierarchical database but allows a many-to-many relationship.
- Example: IDMS (Integrated Database Management System)

## Components of a Database

1. **Tables (Relations)** → Store data.  
2. **Fields (Columns)** → Data attributes.  
3. **Records (Rows)** → Individual data entries.  
4. **Keys (Primary, Foreign)** → Maintain relationships.  
5. **Indexes** → Speed up search.  
6. **Schemas** → Structure of the database.
---

## ✅ 4. What is DBMS?

A **DBMS** is software that manages databases. It provides tools to store, retrieve, update, and delete data efficiently while ensuring data integrity and security.



### Advantages of DBMS

1. **Data Redundancy Control**: Reduces duplication of data.
2. **Data Integrity**: Ensures accuracy and consistency of data.
3. **Data Security**: Provides controlled access to data.
4. **Data Sharing**: Allows multiple users to access data simultaneously.
5. **Backup and Recovery**: Ensures data is recoverable in case of failure.

---

### Disadvantages of DBMS

1. **Complexity**: Requires skilled personnel to manage.
2. **Cost**: High initial setup and maintenance costs.
3. **Performance**: May be slower for small-scale applications.
4. **Hardware Dependency**: Requires specific hardware configurations.

---

### Types of DBMS


1. **Hierarchical DBMS**: Data is organized in a tree-like structure.
2. **Network DBMS**: Data is organized as a graph, allowing many-to-many relationships.
3. **Relational DBMS (RDBMS)**: Data is stored in tables (relations).  
   Example: MySQL, PostgreSQL.
4. **Non-Relational Database (NoSQL)**:  Stores data in documents, key-value pairs, graphs, or columns. It has Flexible structure.  
5. **Object-Oriented DBMS**: Data is stored as objects, similar to object-oriented programming.

---

### 📌 Difference Between Database and DBMS 

| Database           | DBMS                                    |
| ------------------ | --------------------------------------- |
| Collection of data | Software that manages the data          |
| Like files         | Like the cupboard that stores files     |
| Stores data        | Helps create, edit, delete, secure data |


## ✅ 5. What is a File System?
**Definition:**         
A file system is a method used by the operating system to store, organize, and manage files (text, images, videos, documents) on storage devices like HDD, SSD, pen drives.

**Layman Example:**
- Your computer folders → Files → Subfolders
- Everything is stored manually by the user.

**Characteristics:**
- Data stored in separate files
- No centralized control
- No security features
- No relationship between files
- Redundant and inconsistent data is common

**Examples**: NTFS, FAT32, exFAT, ext4


### Example to Understand Difference Between File System and DBMS
**Suppose you maintain student records.**  

Using File System:
- Create separate files like student.txt, marks.txt, attendance.txt
- If a student changes phone number → you must update it in all 3 files
- Easy to miss one file → inconsistency


Using DBMS:     
- Only one table stores student info
- Other tables link using primary & foreign keys
- Update once → reflected everywhere
- Faster, cleaner, safer


---



### File System vs DBMS – Key Differences

| Feature               | File System                        | DBMS                                |
| --------------------- | ---------------------------------- | ----------------------------------- |
| **Definition**        | OS method to store/manage files    | Software to store/manage databases  |
| **Data Storage**      | In separate files (txt, doc, csv)  | In tables / structured format       |
| **Data Access**       | Manual search; slow for large data | Fast access via SQL, indexing       |
| **Redundancy**        | High redundancy (duplicate data)   | Very low redundancy                 |
| **Consistency**       | Hard to maintain                   | DBMS ensures consistency (ACID)     |
| **Security**          | Minimal protection                 | Strong security & user roles        |
| **Backup & Recovery** | Manual                             | Automatic & efficient               |
| **Relationships**     | No relationships between files     | Supports relationships using keys   |
| **Multi-user Access** | Hard & error-prone                 | Safe with concurrency control       |
| **Scalability**       | Limited                            | Highly scalable                     |
| **Querying**          | No querying                        | Uses SQL/queries for data retrieval |
| **Integrity**         | Cannot enforce integrity           | Ensures data integrity rules        |



## IMORTANT TERMS : 
##  1. Data Mining

### Definition

**Data Mining** is the process of discovering hidden patterns, trends, or useful information from large datasets.

### Purpose
- Predict future behavior.
- Find patterns.
- Support decision-making.

### Example
- **Amazon** recommends products based on your previous purchases.
- **Banks** detect fraud transactions using data mining.

### Techniques
1. Classification  
2. Clustering  
3. Association rule mining  
4. Regression  
5. Anomaly detection  

---

## 2. Data Integrity

### Definition

**Data Integrity** refers to the accuracy, consistency, and reliability of data over its entire life cycle.

### Types
1. **Entity Integrity**: Each row must have a unique primary key.  
2. **Referential Integrity**: Foreign keys must match with primary keys.  
3. **Domain Integrity**: Values must be valid for a column.

### Example
- The **Age** column should not contain text.  
- A student record cannot exist in the **Marks Table** without existing in the **Students Table**.

### Goal
Prevent incorrect, incomplete, or duplicate data.

---

## 3. Data Analysis

### Definition

**Data Analysis** is the process of cleaning, processing, interpreting, and modeling data to extract useful insights.

### Steps
1. Collect data.  
2. Clean data.  
3. Process and analyze.  
4. Interpret results.

### Types
1. **Descriptive analysis**: What happened?  
2. **Diagnostic analysis**: Why did it happen?  
3. **Predictive analysis**: What will happen next?  
4. **Prescriptive analysis**: What should be done?

### Example

Analyzing **sales data** to understand which product sells more in winter.

---

## 4. Data Visualization

### Definition

**Data Visualization** means showing data in graphs, charts, diagrams, etc., to make it easy to understand.

### Why Useful?
- Makes data easy to interpret.
- Helps identify patterns and trends quickly.
- Useful for presentations & dashboards.

### Common Visualization Tools
- Power BI  
- Tableau  
- Matplotlib / Seaborn (Python)  
- Excel charts  

### Examples
- **Bar chart** of monthly sales.  
- **Pie chart** of user demographics.  
- **Line graph** of website traffic.

---


## ✔️ Summary for Quick Revision

| Term               | Meaning                                   |
|--------------------|-------------------------------------------|
| **DBMS**           | Software to manage data                  |
| **RDBMS**          | Table-based database using SQL           |
| **NoSQL**          | Non-table database for flexible data     |
| **Data Mining**    | Finding hidden patterns from big data    |
| **Data Integrity** | Accuracy & consistency of data           |
| **Data Analysis**  | Studying data to extract insights        |
| **Data Visualization** | Representing data with charts         |

---

