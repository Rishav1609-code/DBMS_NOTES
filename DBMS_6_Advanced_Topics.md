# 📘 B.Tech 6th Semester — Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Database Management Systems (DBMS)
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 6: **ADVANCED TOPICS**
---

## ❓ Q1: Centralized vs Distributed Database?

### ✅ Answer:

#### 📖 Centralized Database:
**Definition:** A **Centralized Database** is stored and managed at a **single location** (a single server or data center). All users access the database from this central location via a network.

**Characteristics:**
- Single server stores all data.
- All processing happens at one location.
- Easy to manage, maintain, and secure.
- Single point of failure — if server goes down, entire system is unavailable.

**Advantages:**
- Simple administration and maintenance.
- Data consistency is easy to maintain.
- Lower implementation cost.
- Strong security at a single location.

**Disadvantages:**
- Single point of failure.
- Network bottleneck for remote users.
- Poor scalability.
- High latency for geographically distributed users.

---

#### 📖 Distributed Database:
**Definition:** A **Distributed Database** is a collection of databases that are **physically distributed across multiple locations** (sites/nodes) connected via a network, but logically appear as a single unified database to users.

**Characteristics:**
- Data is spread across multiple computers/sites.
- Sites communicate through a network.
- Data may be **replicated** (same data at multiple sites) or **fragmented** (different data at different sites).
- Managed by a **Distributed DBMS (DDBMS)**.

**Advantages:**
- No single point of failure — high availability.
- Better performance for geographically distributed users.
- Scalable — add more nodes as needed.
- Local autonomy — sites can operate independently.

**Disadvantages:**
- Complex to design, manage, and maintain.
- Ensuring data consistency across nodes is challenging.
- Higher implementation and communication cost.
- Security is harder to enforce across distributed sites.

---

#### 📊 Comparison Table:

| Feature              | Centralized Database          | Distributed Database               |
|----------------------|-------------------------------|------------------------------------|
| Location             | Single site                   | Multiple geographically distributed sites |
| Availability         | Single point of failure       | High availability                  |
| Performance          | Can bottleneck                | Better for distributed users       |
| Complexity           | Simple to manage              | Complex administration             |
| Data Consistency     | Easy to maintain              | Challenging (distributed consensus)|
| Scalability          | Limited                       | Highly scalable                    |
| Cost                 | Lower                         | Higher                             |
| Example              | Small business DB             | Google, Amazon, Banking systems    |

---

#### 🔚 Conclusion:
Centralized databases are simpler and cost-effective for small-scale applications with a single location. Distributed databases provide the scalability, availability, and performance needed by modern large-scale applications operating across multiple geographic locations.

---

## ❓ Q2: Difference Between Distributed DBMS & Object-Oriented DBMS?

### ✅ Answer:

#### 📖 Distributed DBMS (DDBMS):
A **Distributed DBMS** manages a database that is stored across **multiple physical locations** connected by a network. It provides the illusion of a single unified database to users.

**Key Features:**
- Data is fragmented (horizontal/vertical) across sites.
- Data may be replicated for availability.
- Supports distributed transactions.
- Uses **distributed query processing**.
- Example: Apache Cassandra, Google Spanner, Amazon Aurora.

---

#### 📖 Object-Oriented DBMS (OODBMS):
An **Object-Oriented DBMS** stores data as **objects** (like in object-oriented programming). Objects have attributes (data) and methods (behavior). It is suitable for complex data types.

**Key Features:**
- Data is represented as objects with attributes and methods.
- Supports **inheritance, encapsulation, polymorphism** from OOP.
- Handles complex data types (images, audio, video, spatial data).
- Objects have **OIDs (Object Identifiers)** for unique identification.
- Example: db4o, ObjectDB, Versant.

---

#### 📊 Difference Table:

| Feature              | Distributed DBMS                    | Object-Oriented DBMS               |
|----------------------|-------------------------------------|------------------------------------|
| Primary Focus        | Distribution of data across sites   | Storing complex object data        |
| Data Model           | Relational (tables)                 | Object model (objects, classes)    |
| Location             | Multiple physical locations         | Can be centralized or distributed  |
| Data Types           | Standard SQL types                  | Complex types (objects, multimedia)|
| Query Language       | Distributed SQL                     | OQL (Object Query Language)        |
| Inheritance Support  | No                                  | Yes                                |
| Best For             | Geographically distributed apps     | CAD, multimedia, complex apps      |
| Complexity           | Network & distribution complexity   | Schema design complexity           |
| Example              | Google Spanner, Cassandra           | db4o, ObjectDB                     |

---

#### 🔚 Conclusion:
Distributed DBMS solves the problem of geographic distribution and availability for large-scale applications, while OODBMS solves the problem of representing and storing complex, real-world objects beyond the capability of traditional relational tables. They address fundamentally different challenges in database management.

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*