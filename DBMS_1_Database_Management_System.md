# 📘 B.Tech 6th Semester — Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Database Management Systems (DBMS)
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 1: **Database Management System**
---

## ❓ Question 1: Describe 3-Schema Architecture. What is the function of a Database Manager? What is the difference between Database & Table?

### ✅ Answer:

---

#### 📖 Part A — 3-Schema Architecture

##### Definition:
The **3-Schema Architecture** (also called ANSI/SPARC Architecture) is a framework that separates the database system into three distinct levels or schemas. It was proposed to achieve **data independence** and to separate user applications from the physical database.

---

##### 📝 The Three Levels:

---

**1. 🔵 External Level (View Level / User Level)**
- This is the **highest level** of abstraction.
- It describes **how individual users see the data**.
- Different users may have different views of the same database.
- Each user group has a customized view called a **subschema** or **external schema**.
- Example: A bank employee sees only customer account details, not the internal storage structure.

---

**2. 🟡 Conceptual Level (Logical Level)**
- This is the **middle level** of abstraction.
- It describes **what data is stored** in the database and the **relationships** among data.
- It hides the physical storage details but exposes the logical structure.
- Defined by the **DBA (Database Administrator)**.
- Includes entities, attributes, relationships, constraints, and security information.
- Example: Tables like `Student(RollNo, Name, Marks)` are defined here.

---

**3. 🔴 Internal Level (Physical Level)**
- This is the **lowest level** of abstraction.
- It describes **how data is physically stored** in the database.
- Deals with storage allocation, indexing, hashing, data compression, etc.
- This level is managed by the **storage engine** of the DBMS.
- Example: Whether data is stored as B-trees, hash files, or sequential files.

---

##### 📊 Diagram — 3-Schema Architecture:

```
+---------------------------------------------+
|         EXTERNAL LEVEL (User Views)         |
|   View 1    |    View 2    |    View 3       |
+---------------------------------------------+
                     |
                     | (External/Conceptual Mapping)
                     ↓
+---------------------------------------------+
|        CONCEPTUAL LEVEL (Logical Schema)    |
|  Entities, Attributes, Relationships,       |
|  Constraints, Security                      |
+---------------------------------------------+
                     |
                     | (Conceptual/Internal Mapping)
                     ↓
+---------------------------------------------+
|        INTERNAL LEVEL (Physical Schema)     |
|  File Organization, Indexing, Hashing,      |
|  Storage Allocation                         |
+---------------------------------------------+
                     |
                     ↓
              [ Physical Database ]
```

---

##### 💡 Data Independence:
The 3-schema architecture provides two types of data independence:
- **Logical Data Independence:** Changes at conceptual level do not affect external level.
- **Physical Data Independence:** Changes at internal level do not affect conceptual level.

---

#### 📖 Part B — Functions of a Database Manager

A **Database Manager (DBM)** is a software module that acts as an interface between the low-level data stored in the database and the application programs/queries submitted to the system.

##### Key Functions:

**1. Interaction with File Manager:**
- Translates DML statements into low-level file system commands.
- Manages storage of data on disk.

**2. Integrity Enforcement:**
- Checks that data satisfies integrity constraints (e.g., age > 0, primary key must be unique).

**3. Security Enforcement:**
- Ensures unauthorized users do not access the data.
- Implements access control mechanisms.

**4. Backup and Recovery:**
- Ensures data is recovered after system failures.
- Manages transaction logs for rollback and recovery.

**5. Concurrency Control:**
- Manages simultaneous access by multiple users without data inconsistency.
- Uses locking and timestamp protocols.

**6. Query Processing:**
- Converts high-level queries (SQL) into efficient execution plans.

---

#### 📖 Part C — Difference Between Database & Table

| Feature           | Database                                         | Table                                          |
|-------------------|--------------------------------------------------|------------------------------------------------|
| Definition        | A collection of organized data stored together   | A structured set of data in rows and columns   |
| Scope             | Broader concept — contains many tables           | A single unit within a database                |
| Components        | Contains tables, views, indexes, procedures      | Contains rows (records) and columns (fields)   |
| Example           | `SchoolDB` (entire school database)              | `Student` table with Name, RollNo, Marks       |
| Creation Command  | `CREATE DATABASE SchoolDB;`                      | `CREATE TABLE Student(...);`                   |
| Storage           | Stored as a set of files on disk                 | Stored as a relation/entity within the DB      |
| Relationship      | A database holds multiple tables                 | A table belongs to one database                |

---

#### 🔚 Conclusion:
The 3-Schema Architecture is a fundamental concept in DBMS that ensures data independence, security, and abstraction across different user levels. The Database Manager acts as the brain of the DBMS ensuring integrity, security, recovery, and concurrency. A database is the complete organized data store, while a table is a single organized unit within it.

---
---

## ❓ Question 2: Difference Between Procedural & Non-Procedural DML? Explain DDL, DML & DCL?

### ✅ Answer:

---

#### 📖 Part A — Procedural vs Non-Procedural DML

**DML (Data Manipulation Language)** is the language used to access and manipulate data in the database. It is divided into two types:

---

**🔵 Procedural DML:**
- The user specifies **WHAT data is needed AND HOW to get it**.
- The user must write step-by-step instructions to retrieve or manipulate data.
- Requires knowledge of internal data structures.
- Example: Relational Algebra (used internally by DBMS query processors).

**🟡 Non-Procedural DML:**
- The user specifies **only WHAT data is needed**, not HOW to get it.
- The DBMS itself decides the best way to retrieve the data.
- Easier to use, no need to know internal storage details.
- Example: **SQL (Structured Query Language)**, QBE (Query By Example).

---

##### 📊 Comparison Table:

| Feature              | Procedural DML                      | Non-Procedural DML               |
|----------------------|--------------------------------------|----------------------------------|
| User Specifies       | What + How                           | Only What                        |
| Complexity           | More complex                         | Easier to use                    |
| Knowledge Required   | Internal data structure knowledge    | No internal knowledge needed     |
| Optimization         | Done by the user                     | Done by the DBMS                 |
| Example              | Relational Algebra, IMS DL/I         | SQL, QBE, QUEL                   |
| Flexibility          | More control                         | Less control but more convenient |

---

#### 📖 Part B — DDL, DML & DCL

---

**1. 🔵 DDL — Data Definition Language**

##### Definition:
DDL is used to **define, modify, and delete** the structure (schema) of database objects like tables, indexes, and views.

##### Functions:
- Defines the schema of the database.
- Creates, alters, and drops database objects.
- The DDL compiler generates a set of table templates stored in the **data dictionary**.

##### Common DDL Commands:

| Command   | Description                              | Example                                     |
|-----------|------------------------------------------|---------------------------------------------|
| CREATE    | Creates a new table/database/view        | `CREATE TABLE Student(ID INT, Name VARCHAR);`|
| ALTER     | Modifies an existing table structure     | `ALTER TABLE Student ADD Marks INT;`        |
| DROP      | Deletes a table or database permanently  | `DROP TABLE Student;`                       |
| TRUNCATE  | Removes all records but keeps structure  | `TRUNCATE TABLE Student;`                   |
| RENAME    | Renames a table                          | `RENAME TABLE Student TO Learner;`          |

---

**2. 🟡 DML — Data Manipulation Language**

##### Definition:
DML is used to **insert, update, delete, and retrieve** data stored in the database.

##### Types:
- **Procedural DML** — user specifies how to get data.
- **Non-Procedural DML** — user specifies what data to get.

##### Common DML Commands:

| Command   | Description                              | Example                                      |
|-----------|------------------------------------------|----------------------------------------------|
| SELECT    | Retrieves data from a table              | `SELECT * FROM Student;`                     |
| INSERT    | Adds new records to a table              | `INSERT INTO Student VALUES(1, 'Rishav');`   |
| UPDATE    | Modifies existing records                | `UPDATE Student SET Marks=90 WHERE ID=1;`    |
| DELETE    | Removes records from a table             | `DELETE FROM Student WHERE ID=1;`            |

---

**3. 🔴 DCL — Data Control Language**

##### Definition:
DCL is used to **control access and permissions** of users on the database. It manages security and authorization.

##### Common DCL Commands:

| Command   | Description                              | Example                                             |
|-----------|------------------------------------------|-----------------------------------------------------|
| GRANT     | Gives access/permissions to a user       | `GRANT SELECT ON Student TO user1;`                 |
| REVOKE    | Removes access/permissions from a user   | `REVOKE SELECT ON Student FROM user1;`              |

---

##### 📊 Summary Diagram:

```
         SQL Language
            |
   ---------+-----------+----------
   |                    |          |
  DDL                  DML        DCL
(Structure)          (Data)    (Control)
   |                    |          |
CREATE               SELECT      GRANT
ALTER                INSERT      REVOKE
DROP                 UPDATE
TRUNCATE             DELETE
```

---

#### 🔚 Conclusion:
Procedural DML gives the user more control but requires knowledge of data structures, while Non-Procedural DML (like SQL) is user-friendly and widely used. DDL manages the structure of the database, DML manages the actual data, and DCL manages user access and security — together forming the complete SQL language.

---
---

## ❓ Question 3: Advantages of DBMS Over Conventional File System & Short Note on Advantages of DBMS

### ✅ Answer:

---

#### 📖 Part A — DBMS vs Conventional File System

In a **conventional file system**, data is stored in separate files and managed by application programs. This approach has several drawbacks that DBMS overcomes.

---

##### 📊 Comparison Table:

| Feature                    | Conventional File System                     | DBMS                                           |
|----------------------------|----------------------------------------------|------------------------------------------------|
| Data Redundancy            | High — same data stored in multiple files    | Low — data stored once, shared across apps     |
| Data Inconsistency         | Common — updates in one file not reflected   | Avoided — single source of truth               |
| Data Sharing               | Difficult — files are application-specific   | Easy — multiple users access same DB           |
| Data Security              | Limited — basic file permissions only        | Strong — role-based access control             |
| Data Independence          | Not supported                                | Supported (logical & physical)                 |
| Query Processing           | Manual — need to write programs              | Automated — use SQL queries                    |
| Backup & Recovery          | Manual and unreliable                        | Automatic with transaction logs                |
| Concurrent Access          | Not supported                                | Supported with concurrency control             |
| Data Integrity             | No built-in constraints                      | Constraints enforced automatically             |
| Cost                       | Low initial cost                             | Higher initial cost but long-term savings      |

---

#### 📖 Part B — Advantages of DBMS (Short Note)

---

**1. 🔵 Reduced Data Redundancy:**
In file systems, the same data is often duplicated across multiple files. DBMS centralizes data storage, ensuring each piece of data is stored only once, reducing storage waste and redundancy.

---

**2. 🟡 Data Consistency:**
Since data is stored in one place and shared among applications, updates are reflected everywhere automatically. This avoids inconsistencies that occur in file systems where the same data may be updated in one file but not in another.

---

**3. 🔴 Data Sharing:**
DBMS allows multiple users and applications to access the same data simultaneously. The concurrency control mechanism ensures no conflicts arise during simultaneous access.

---

**4. 🟢 Data Security:**
DBMS provides robust security features including:
- User authentication
- Role-based access control (GRANT/REVOKE)
- Encryption of sensitive data

---

**5. 🟣 Data Independence:**
- **Physical Data Independence:** Changing storage structures does not affect applications.
- **Logical Data Independence:** Changing the logical schema does not affect user views.

---

**6. 🔶 Backup and Recovery:**
DBMS provides automatic backup and recovery mechanisms. In case of system failure, data can be restored using transaction logs through rollback and recovery procedures.

---

**7. 🔷 Enforcement of Integrity Constraints:**
DBMS enforces rules like:
- Primary Key (unique identification)
- Foreign Key (referential integrity)
- NOT NULL, CHECK constraints
- These ensure the correctness and validity of data at all times.

---

**8. 🟤 Efficient Query Processing:**
DBMS uses optimized query processors to execute SQL queries efficiently. Users do not need to write complex programs to retrieve data.

---

**9. Reduced Application Development Time:**
Since DBMS handles data management tasks, developers can focus on application logic, reducing development time significantly.

---

**10. Concurrent Access Control:**
DBMS allows many users to access and modify data simultaneously using locking and timestamp protocols without data corruption.

---

#### 🔚 Conclusion:
DBMS is a far superior approach to data management compared to the conventional file system. It eliminates redundancy, ensures consistency, provides security, enables data sharing, and offers reliable backup and recovery. For any organization dealing with large volumes of data, DBMS is the preferred and practical choice.

---
---

## ❓ Question 4: What is a Data Dictionary?

### ✅ Answer:

---

#### 📖 Definition:
A **Data Dictionary** (also called a **System Catalog** or **Metadata Repository**) is a centralized repository that stores **metadata** — that is, data about data. It contains information about the structure, definitions, relationships, and constraints of all the data stored in the database.

> In simple words: *"A data dictionary is a directory that describes every piece of data in the database."*

---

#### 📝 What Does a Data Dictionary Contain?

| Information Type       | Description                                              |
|------------------------|----------------------------------------------------------|
| Table Names            | Names of all tables/relations in the database            |
| Column Names           | Names and descriptions of all columns in each table      |
| Data Types             | Type of each attribute (INT, VARCHAR, DATE, etc.)        |
| Constraints            | Primary keys, foreign keys, NOT NULL, UNIQUE rules       |
| User Information       | List of authorized users and their access rights         |
| Relationships          | How tables are related to each other (via foreign keys)  |
| Indexes                | Information about indexes created for performance        |
| Views                  | Definitions of virtual tables (views)                    |
| Storage Information    | How and where data is physically stored                  |

---

#### 📝 Types of Data Dictionary:

**1. 🔵 Active Data Dictionary:**
- Automatically updated by the DBMS whenever schema changes are made.
- Always in sync with the actual database.
- Example: Oracle's internal system catalog.

**2. 🟡 Passive Data Dictionary:**
- Maintained separately and updated manually by the DBA.
- Not automatically integrated with the DBMS.
- Can become out of sync if not maintained properly.

---

#### 📊 Diagram — Role of Data Dictionary in DBMS:

```
    +----------------+          +-------------------+
    |   User / App   |          |   DBA (Admin)      |
    +----------------+          +-------------------+
            |                            |
            ↓                            ↓
    +-----------------------------------------------+
    |              DBMS Engine                      |
    |   - Query Processor                           |
    |   - Transaction Manager                       |
    +-----------------------------------------------+
            |
            ↓
    +-----------------------------------------------+
    |           DATA DICTIONARY (Metadata)          |
    |  - Table names    - Column definitions        |
    |  - Constraints    - User access rights        |
    |  - Indexes        - Relationships             |
    +-----------------------------------------------+
            |
            ↓
    +-----------------------------------------------+
    |              Physical Database                |
    +-----------------------------------------------+
```

---

#### 💡 Functions of Data Dictionary:

1. **Stores Metadata:** Keeps track of all schema definitions.
2. **Supports Query Optimization:** The DBMS uses it to create efficient query plans.
3. **Maintains Data Integrity:** Constraint information is stored and enforced using it.
4. **Manages Security:** User permissions and access roles are stored here.
5. **Assists DBA:** The DBA uses it to manage and understand the database structure.
6. **Supports Documentation:** Acts as a reference for developers and administrators.

---

#### 💡 Example:
In **MySQL**, the data dictionary is stored in the `information_schema` database. For example:

```sql
-- View all tables in the current database
SELECT * FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'SchoolDB';

-- View all columns of a table
SELECT * FROM information_schema.COLUMNS WHERE TABLE_NAME = 'Student';
```

---

#### 🔚 Conclusion:
The Data Dictionary is the backbone of any DBMS. It acts as the central metadata store that the DBMS relies on for query processing, security enforcement, constraint checking, and overall database management. Without a data dictionary, the DBMS would not know the structure, rules, or relationships of the data it manages.

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*