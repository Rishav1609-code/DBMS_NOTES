# 📘 B.Tech 6th Semester — Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Database Management Systems (DBMS)
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 5: **DATABASE SECURITY**
---

## ❓ Q1: Importance of View for Security. GRANT & REVOKE Commands?

### ✅ Answer:

#### 📖 View for Security:
A **View** acts as a security layer over base tables by allowing users to access **only the data they are authorized to see**, without exposing the full table.

**How Views Provide Security:**

**1. Column-Level Security:**
- Hide sensitive columns from unauthorized users.
```sql
CREATE VIEW PublicEmployee AS SELECT EmpID, Name, Department FROM Employee;
-- Salary, BankAccount columns are hidden
```

**2. Row-Level Security:**
- Restrict users to specific rows only.
```sql
CREATE VIEW MyOrders AS SELECT * FROM Orders WHERE CustomerID = CURRENT_USER();
```

**3. Masking Complex Joins:**
- Users see a simple view without knowing the underlying table structure.

**4. Preventing Direct Table Access:**
- Users are given permission only on views, not on base tables.
- Even if they try to directly query base tables, they are denied.

---

#### 📖 GRANT Command:
**Definition:** GRANT is a DCL command that gives **specific privileges** to a user or role on a database object.

**Syntax:**
```sql
GRANT privilege_list ON object TO user [WITH GRANT OPTION];
```

**Privileges:** SELECT, INSERT, UPDATE, DELETE, ALL PRIVILEGES

**Examples:**
```sql
-- Allow user1 to read from Student table
GRANT SELECT ON Student TO user1;

-- Allow user2 to insert and update Employee
GRANT INSERT, UPDATE ON Employee TO user2;

-- Give all privileges
GRANT ALL PRIVILEGES ON Database.* TO admin@localhost;

-- Allow user to further grant this privilege to others
GRANT SELECT ON Student TO user1 WITH GRANT OPTION;
```

---

#### 📖 REVOKE Command:
**Definition:** REVOKE is a DCL command that **removes previously granted privileges** from a user or role.

**Syntax:**
```sql
REVOKE privilege_list ON object FROM user;
```

**Examples:**
```sql
-- Remove SELECT permission from user1
REVOKE SELECT ON Student FROM user1;

-- Remove all privileges from user2
REVOKE ALL PRIVILEGES ON Employee FROM user2;
```

---

#### 📖 Importance of GRANT & REVOKE:
- **Access Control:** Precisely control who can do what on which objects.
- **Least Privilege Principle:** Users get only the minimum permissions they need.
- **Accountability:** Tracks who has access to what.
- **Dynamic Security:** Permissions can be added or removed as roles change.
- **Protection against insider threats:** Limits damage from internal misuse.

---

#### 🔚 Conclusion:
Views combined with GRANT/REVOKE form a powerful, flexible security model in DBMS. Views hide data structurally, while GRANT and REVOKE control access rights dynamically, together implementing comprehensive database security.

---

## ❓ Q2: DAC, MAC & RBAC?

### ✅ Answer:

#### 📖 1. DAC — Discretionary Access Control

**Definition:** In **DAC**, access rights are determined by the **owner of the data**. The owner can grant or revoke permissions to other users at their own discretion.

**Characteristics:**
- The data owner controls who can access the data.
- Users can pass their permissions to others (using WITH GRANT OPTION).
- More flexible but less secure.
- Implemented via GRANT and REVOKE in SQL.

**Example:**
- User Rishav creates a table and grants SELECT permission to Raj.
- Raj can further grant this to Aryan (if GRANT OPTION is given).

**Drawback:** If a malicious user gains access, they can misuse the data and even grant access to others.

---

#### 📖 2. MAC — Mandatory Access Control

**Definition:** In **MAC**, access rights are determined by the **system/administrator** based on security labels. Neither data owners nor users can change access rights — only the system administrator controls them.

**Characteristics:**
- Data and users are assigned **security labels** (e.g., Top Secret, Secret, Confidential, Unclassified).
- Users can only access data at or below their security clearance level.
- Stricter and more secure than DAC.
- Used in military and government systems.

**Rules (Bell-LaPadula Model):**
- **No Read Up:** A user cannot read data above their clearance level.
- **No Write Down:** A user cannot write data to a lower classification level.

**Example:**
- A "Secret" level user can read "Confidential" and "Unclassified" data but NOT "Top Secret" data.

---

#### 📖 3. RBAC — Role-Based Access Control

**Definition:** In **RBAC**, access rights are assigned to **roles**, and users are assigned to roles. Users inherit permissions based on their role, not as individuals.

**Characteristics:**
- Permissions are attached to roles (e.g., Manager, Developer, Admin).
- Users are assigned one or more roles.
- Easy to manage in large organizations.
- Industry standard for enterprise database security.

**Example:**
```sql
-- Create roles
CREATE ROLE Manager;
CREATE ROLE Developer;

-- Grant permissions to roles
GRANT SELECT, INSERT, UPDATE ON Employee TO Manager;
GRANT SELECT ON Employee TO Developer;

-- Assign roles to users
GRANT Manager TO Rishav;
GRANT Developer TO Raj;
```

---

#### 📊 Comparison Table:

| Feature             | DAC                          | MAC                              | RBAC                           |
|---------------------|------------------------------|----------------------------------|--------------------------------|
| Control by          | Data owner                   | System administrator             | Role assignment                |
| Flexibility         | High                         | Low                              | Medium                         |
| Security Level      | Moderate                     | Very High                        | High                           |
| Use Case            | Commercial databases         | Military, government systems     | Enterprise systems             |
| Permission Basis    | User identity                | Security labels/clearance        | User roles                     |
| Example             | SQL GRANT/REVOKE             | Military DB security             | Hospital DB (Doctor, Nurse)    |

---

#### 🔚 Conclusion:
DAC offers flexibility with user-controlled access, MAC provides strict system-enforced security for classified environments, and RBAC simplifies access management in organizations through role-based permission inheritance. Most modern enterprise databases use RBAC for practical security management.

---

## ❓ Q3: What is Intrusion Detection?

### ✅ Answer:

#### 📖 Definition:
**Intrusion Detection** (in database security) is the process of **monitoring database activities** and identifying **unauthorized, suspicious, or malicious access patterns** that may indicate a security breach or attack.

#### 📝 Types of Intrusion Detection Systems (IDS):

**1. Signature-Based IDS:**
- Compares activity against a database of known attack patterns (signatures).
- Effective against known threats; cannot detect new/unknown attacks.
- Example: Detecting a known SQL injection pattern.

**2. Anomaly-Based IDS:**
- Establishes a baseline of normal behavior.
- Raises alerts when activity deviates significantly from the baseline.
- Can detect novel/unknown attacks.
- Example: A user who normally reads 10 records/day suddenly reads 10,000 records.

**3. Hybrid IDS:**
- Combines both signature-based and anomaly-based detection.

---

#### 📝 What Intrusion Detection Monitors:
- **Failed login attempts** — brute force attacks.
- **Unusual query patterns** — large data extractions, accessing tables not normally used.
- **Privilege escalation** — users accessing resources beyond their role.
- **Off-hours access** — database access at unusual times.
- **SQL injection attempts** — malformed queries in input fields.
- **Unauthorized schema changes** — DDL operations by non-admin users.

---

#### 📝 Intrusion Detection in DBMS:
- **Audit Logs:** DBMS records all database activities in audit logs for later analysis.
- **Database Activity Monitoring (DAM):** Real-time monitoring of all SQL activity.
- **Alerts:** Automatic notifications when suspicious activity is detected.

**Example:**
```sql
-- Enable auditing in Oracle
AUDIT SELECT ON Employee BY USER;
-- All SELECT operations on Employee table are logged for security review
```

---

#### 🔚 Conclusion:
Intrusion detection is a proactive security measure that acts as a surveillance system for the database. By continuously monitoring activities and detecting anomalies or known attack signatures, it helps prevent data breaches, unauthorized access, and internal threats.

---

## ❓ Q4: What is SQL Injection?

### ✅ Answer:

#### 📖 Definition:
**SQL Injection** is a **security vulnerability/attack** where an attacker inserts or "injects" malicious SQL code into an input field of an application that is then executed by the database. It is one of the most dangerous and common web application security threats.

---

#### 📝 How SQL Injection Works:

Consider a login form that builds this query:
```sql
SELECT * FROM Users WHERE Username = '$user' AND Password = '$pass';
```

**Normal Input:**
- Username: `rishav`, Password: `pass123`
- Query: `SELECT * FROM Users WHERE Username = 'rishav' AND Password = 'pass123';` ✅

**Malicious Input (SQL Injection):**
- Username: `admin' --`, Password: `anything`
- Query becomes: `SELECT * FROM Users WHERE Username = 'admin' --' AND Password = 'anything';`
- The `--` comments out the password check → attacker logs in as admin without knowing the password! ❌

---

#### 📝 Types of SQL Injection:

**1. Classic/In-Band SQL Injection:**
- Attacker uses the same channel to inject and retrieve data.
- Most common type.

**2. Blind SQL Injection:**
- No data is returned directly; attacker infers information from true/false responses.
- **Boolean-based:** Ask true/false questions to extract data.
- **Time-based:** Use SLEEP() to infer data from response delays.

**3. Out-of-Band SQL Injection:**
- Data is extracted through different channels (e.g., DNS requests, HTTP requests).

---

#### 📝 Dangerous SQL Injection Examples:

```sql
-- Drop entire table:
'; DROP TABLE Student; --

-- Extract all usernames and passwords:
' UNION SELECT Username, Password FROM Users --

-- Bypass authentication:
' OR '1'='1
```

---

#### 📝 Prevention of SQL Injection:

**1. Prepared Statements (Parameterized Queries):**
```sql
-- SAFE (Parameterized):
SELECT * FROM Users WHERE Username = ? AND Password = ?;
-- Values are passed separately — cannot alter query structure
```

**2. Input Validation & Sanitization:**
- Reject or escape special characters like `'`, `--`, `;`.

**3. Stored Procedures:**
- Use stored procedures instead of dynamic SQL.

**4. Least Privilege:**
- Database users used by applications should have minimal permissions.

**5. Web Application Firewalls (WAF):**
- Filter and block SQL injection patterns at the network level.

---

#### 🔚 Conclusion:
SQL injection is a critical security threat that can lead to unauthorized data access, data loss, or complete database takeover. It exploits poor input handling in applications. Using parameterized queries and input validation are the most effective defenses against SQL injection attacks.

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*