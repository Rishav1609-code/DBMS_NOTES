# 📘 B.Tech 6th Semester — Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Database Management Systems (DBMS)
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 4: **SQL**
---

## ❓ Q1: Explain View. Discuss Different Levels of Views. Problems with UPDATE through Views?

### ✅ Answer:

#### 📖 What is a View?
A **View** is a **virtual table** in the database that is defined by a SQL query. It does not store data physically — it retrieves data dynamically from the base tables whenever it is accessed.

**Syntax:**
```sql
CREATE VIEW view_name AS
SELECT columns FROM table WHERE condition;
```

**Example:**
```sql
CREATE VIEW CS_Students AS
SELECT RollNo, Name, Marks FROM Student WHERE DeptID = 'CS';
```

Now `CS_Students` can be queried like a table:
```sql
SELECT * FROM CS_Students;
```

---

#### 📖 Levels of Views (Based on 3-Schema Architecture):

**1. External Views (User-Level):**
- Each user or user group sees only the data relevant to them.
- Provides security by hiding sensitive columns/rows.
- Example: A student sees only their own grades; a teacher sees all students' grades.

**2. Conceptual View (Logical-Level):**
- Represents the complete logical structure of the database.
- Views can be created to represent logical groupings of data.

**3. Physical-Level:**
- Views are always derived from physical base tables.
- Storage is not duplicated; views are computed at query time.

---

#### 📖 Advantages of Views:
- **Security:** Hide sensitive columns (e.g., salary, personal info).
- **Simplicity:** Complex queries can be saved as views for reuse.
- **Data Independence:** Applications use views; base table can change without breaking apps.
- **Customization:** Different users see different perspectives of the same data.

---

#### 📖 Problems with UPDATE Operations Through Views:

**1. Views Based on Multiple Tables (Joins):**
- If a view is defined using JOIN of two or more tables, it is generally NOT updatable.
- The DBMS cannot determine which base table to update.
```sql
CREATE VIEW Emp_Dept AS
SELECT E.Name, D.DeptName FROM Employee E JOIN Department D ON E.DeptID = D.DeptID;
-- UPDATE on this view is ambiguous — which table to update?
```

**2. Views with Aggregate Functions:**
- Views using GROUP BY, SUM, COUNT, AVG cannot be updated.
```sql
CREATE VIEW DeptCount AS SELECT DeptID, COUNT(*) AS Total FROM Employee GROUP BY DeptID;
-- Cannot UPDATE or INSERT — aggregates don't map to individual rows.
```

**3. Views with DISTINCT:**
- If DISTINCT is used, updates are not allowed.

**4. Views Without Primary Key Columns:**
- If the view does not include the primary key of the base table, INSERT/UPDATE cannot uniquely identify the row.

**5. NOT NULL Constraints:**
- If a base table has NOT NULL columns not included in the view, INSERT through view will fail.

---

#### 🔚 Conclusion:
Views are powerful tools for security, simplicity, and logical data independence. However, update operations through views are restricted in many scenarios due to the complexity of mapping view changes back to base tables unambiguously.

---

## ❓ Q2: Recovery Implications of Buffer Management at COMMIT?

### ✅ Answer:

#### 📖 Background:
A **buffer** is a memory area (RAM) where database pages are temporarily held before being written to disk. **COMMIT** makes a transaction's changes permanent.

---

#### 🔵 a. Forcing Buffers to the Database at COMMIT (FORCE Policy):

**What it means:**
- At the time of COMMIT, ALL modified pages (buffers) are **immediately written to disk**.
- No modified page stays in the buffer after COMMIT.

**Recovery Implications:**
- **Advantage:** No REDO is needed during recovery — committed data is already on disk.
- **Disadvantage:** COMMIT takes longer because it involves forced disk writes (expensive I/O).
- On recovery after a crash, we only need to UNDO uncommitted transactions; no need to redo committed ones.
- Leads to **more frequent disk writes** → performance overhead.

**Recovery Action Needed:** Only UNDO (for uncommitted transactions).

---

#### 🟡 b. Never Physically Writing Buffers Prior to COMMIT (NO-FORCE / STEAL Policy):

**What it means:**
- Modified pages are NOT written to disk until after COMMIT (or the buffer needs the space).
- A transaction's changes may stay only in RAM until explicitly flushed.

**Recovery Implications:**
- **Advantage:** Better performance — no forced writes during COMMIT.
- **Disadvantage:** If the system crashes BEFORE the buffer is written to disk, the committed transaction's changes are LOST.
- During recovery, we need to **REDO** all committed transactions whose changes were not yet on disk.
- Requires maintaining a **Write-Ahead Log (WAL)** — all changes must be logged before applying to disk.

**Recovery Actions Needed:** REDO committed transactions + UNDO uncommitted transactions.

---

#### 📊 Summary Table:

| Policy                  | Disk Write at COMMIT | Recovery Action      | Performance  |
|-------------------------|----------------------|----------------------|--------------|
| FORCE (write at commit) | Yes                  | UNDO only            | Slower       |
| NO-FORCE (no write)     | No                   | REDO + UNDO          | Faster       |

---

#### 🔚 Conclusion:
Buffer management policies directly affect database performance and recovery complexity. The FORCE policy is safer but slower; NO-FORCE is faster but requires a comprehensive logging mechanism (WAL) for proper recovery.

---

## ❓ Q3: What is a Trigger? Types of Triggers? What is Cursor?

### ✅ Answer:

#### 📖 What is a Trigger?
A **Trigger** is a stored procedure in a database that **automatically executes** (fires) in response to certain events on a table (INSERT, UPDATE, DELETE). It is invoked automatically by the DBMS — not explicitly by the user.

**Syntax:**
```sql
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE ON table_name
FOR EACH ROW
BEGIN
    -- trigger body
END;
```

**Example:**
```sql
CREATE TRIGGER after_insert_student
AFTER INSERT ON Student
FOR EACH ROW
BEGIN
    INSERT INTO AuditLog VALUES (NEW.RollNo, 'Student Added', NOW());
END;
```

---

#### 📖 Types of Triggers:

**Based on Timing:**
- **BEFORE Trigger:** Fires BEFORE the triggering event (can modify data before it's written).
- **AFTER Trigger:** Fires AFTER the triggering event (used for logging, auditing).
- **INSTEAD OF Trigger:** Fires INSTEAD OF the triggering event (used for views).

**Based on Event:**
- **INSERT Trigger:** Fires on INSERT operation.
- **UPDATE Trigger:** Fires on UPDATE operation.
- **DELETE Trigger:** Fires on DELETE operation.

**Based on Level:**
- **Row-Level Trigger:** Fires once for EACH row affected.
- **Statement-Level Trigger:** Fires once for the ENTIRE SQL statement.

---

#### 📊 Trigger Types Summary:

| Type          | When it Fires                          | Common Use                      |
|---------------|----------------------------------------|---------------------------------|
| BEFORE INSERT | Before new row is inserted             | Validate or modify incoming data|
| AFTER INSERT  | After new row is inserted              | Logging, audit trail            |
| BEFORE UPDATE | Before row is updated                  | Validate changes                |
| AFTER UPDATE  | After row is updated                   | Sync related tables             |
| BEFORE DELETE | Before row is deleted                  | Archive before deletion         |
| AFTER DELETE  | After row is deleted                   | Cleanup related records         |
| INSTEAD OF    | Instead of the operation (on views)    | Make views updatable            |

---

#### 📖 What is a Cursor?
A **Cursor** is a **database object** used to retrieve, process, and manipulate **one row at a time** from the result set of a query. It acts like a pointer that moves through the rows of a result set sequentially.

**Why Needed:**
- SQL is set-oriented — it retrieves multiple rows at once.
- A cursor allows row-by-row processing when needed (e.g., in stored procedures).

**Types of Cursors:**
- **Implicit Cursor:** Automatically created by DBMS for single-row queries.
- **Explicit Cursor:** Manually defined by the programmer for multi-row processing.

**Cursor Lifecycle:**
```
DECLARE cursor → OPEN cursor → FETCH row → Process → FETCH next → CLOSE cursor
```

**Example (PL/SQL):**
```sql
DECLARE
    CURSOR student_cursor IS SELECT Name, Marks FROM Student;
    v_name VARCHAR(50); v_marks INT;
BEGIN
    OPEN student_cursor;
    LOOP
        FETCH student_cursor INTO v_name, v_marks;
        EXIT WHEN student_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_name || ' - ' || v_marks);
    END LOOP;
    CLOSE student_cursor;
END;
```

---

#### 🔚 Conclusion:
Triggers automate database actions in response to data changes, enforcing business rules and maintaining audit trails without user intervention. Cursors provide row-by-row processing capability essential in procedural database programming where set-based SQL operations are insufficient.

---

## ❓ Q4: Embedded SQL & Dynamic SQL. Five Reasons for NULL Values?

### ✅ Answer:

#### 📖 Embedded SQL:
**Definition:** **Embedded SQL** refers to SQL statements that are **written inside a host programming language** (like C, Java, COBOL). The SQL is embedded within the program code and precompiled before compilation.

**Characteristics:**
- SQL statements are written directly in the host language code.
- Prefixed with `EXEC SQL` in most implementations.
- **Static:** The SQL queries are fixed at compile time — they don't change at runtime.
- Precompiled by a **precompiler** that converts SQL into function calls.

**Example (in C):**
```c
EXEC SQL BEGIN DECLARE SECTION;
    int roll; char name[50];
EXEC SQL END DECLARE SECTION;

EXEC SQL SELECT Name INTO :name FROM Student WHERE RollNo = :roll;
printf("Student Name: %s", name);
```

---

#### 📖 Dynamic SQL:
**Definition:** **Dynamic SQL** refers to SQL statements that are **constructed and executed at runtime** — the query is not fixed at compile time. The SQL string is built dynamically based on user input or program logic.

**Characteristics:**
- SQL query is a string that is built during execution.
- More flexible than Embedded SQL.
- Used when the query structure is not known in advance.
- Uses `PREPARE` and `EXECUTE` statements.

**Example:**
```sql
-- Dynamic query built as a string at runtime:
SET @query = CONCAT('SELECT * FROM ', @tableName, ' WHERE Age > ', @ageValue);
PREPARE stmt FROM @query;
EXECUTE stmt;
```

---

#### 📊 Difference Table:

| Feature            | Embedded SQL                       | Dynamic SQL                         |
|--------------------|------------------------------------|-------------------------------------|
| Query Fixed At     | Compile time                       | Runtime                             |
| Flexibility        | Less flexible                      | Highly flexible                     |
| Performance        | Faster (pre-compiled)              | Slower (compiled at runtime)        |
| Security           | More secure                        | Vulnerable to SQL injection         |
| Use Case           | Known, fixed queries               | Ad-hoc or user-defined queries      |

---

#### 📖 Five Reasons NULL Values May Be Introduced:

**1. Value Unknown:**
- The value exists but is not known at the time of data entry.
- Example: A student's phone number is not known when registering.

**2. Value Not Applicable:**
- The attribute does not apply to the entity.
- Example: `SpouseName` for an unmarried employee.

**3. Value Not Yet Available:**
- The value will be provided later but is missing now.
- Example: Final exam marks before the exam is conducted.

**4. Attribute Does Not Exist for the Entity:**
- The attribute is irrelevant for that particular tuple.
- Example: `MaternityLeave` for a male employee.

**5. Data Entry Error or Missing Data:**
- The value was accidentally omitted during data entry.
- Example: A form field left blank during online registration.

---

#### 🔚 Conclusion:
Embedded SQL provides a structured way to integrate database queries into programs, while Dynamic SQL offers flexibility for runtime query construction. NULL values represent missing, unknown, or inapplicable data — a fundamental concept in relational databases that must be handled carefully in queries and constraints.

---

## ❓ Q5: Difference Between DELETE, TRUNCATE & DROP?

### ✅ Answer:

#### 📖 Definitions:

**DELETE:** A DML command that removes **specific rows** from a table based on a WHERE condition (or all rows if no condition is given). Changes can be rolled back.

**TRUNCATE:** A DDL command that removes **all rows** from a table at once without logging individual row deletions. Cannot be rolled back in most DBMS.

**DROP:** A DDL command that **completely removes** the table (or database) including its structure, data, indexes, and constraints from the database.

---

#### 📊 Comparison Table:

| Feature              | DELETE                          | TRUNCATE                         | DROP                              |
|----------------------|---------------------------------|----------------------------------|-----------------------------------|
| Category             | DML                             | DDL                              | DDL                               |
| Removes              | Specific rows (with WHERE)      | All rows                         | Entire table/database             |
| Table Structure      | Preserved                       | Preserved                        | Removed completely                |
| WHERE Clause         | Yes (optional)                  | No                               | No                                |
| Rollback Possible    | Yes (transaction-safe)          | No (in most DBMS)                | No                                |
| Triggers Fired       | Yes (row-level triggers fire)   | No                               | No                                |
| Speed                | Slow (logs each row)            | Fast (minimal logging)           | Fastest                           |
| Auto Increment Reset | No                              | Yes (resets identity counter)    | N/A (table is removed)            |
| Space Released       | No (pages not deallocated)      | Yes (storage released)           | Yes (all storage released)        |

---

#### 💡 Examples:
```sql
-- DELETE: Remove specific student
DELETE FROM Student WHERE RollNo = 5;

-- DELETE all rows (with rollback possible)
DELETE FROM Student;

-- TRUNCATE: Remove all rows quickly (no rollback)
TRUNCATE TABLE Student;

-- DROP: Remove the entire Student table permanently
DROP TABLE Student;
```

---

#### 🔚 Conclusion:
DELETE is used for selective row removal with transaction safety; TRUNCATE is a fast way to empty a table permanently; DROP eliminates the table entirely from the database. Choosing the right command is critical — DROP and TRUNCATE are irreversible in most systems.

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*