# 📘 B.Tech 6th Semester — Complete Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** DBMS — All Topics
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 3: **RELATIONAL MODEL**
---

## ❓ Q1: Why We Need Query Optimization? Steps to Process a High-Level Query?

### ✅ Answer:

#### 📖 Why Query Optimization?
**Query Optimization** is the process of selecting the most **efficient execution plan** for a given query. The DBMS query optimizer analyzes multiple possible execution strategies and picks the one with the **minimum cost** (in terms of time, memory, and I/O).

**Why it is Needed:**
- The same query can be executed in multiple ways with drastically different performance.
- A poorly written or executed query can take minutes; an optimized one takes seconds.
- Reduces CPU usage, disk I/O, and memory consumption.
- Users write queries in high-level SQL — the DBMS must convert them to efficient low-level operations.
- Large databases with millions of rows need efficient query plans to be practical.

---

#### 📝 Steps to Process a High-Level Query:

```
High-Level Query (SQL)
        |
        ↓
1. PARSING & TRANSLATION
        |
        ↓
2. QUERY TREE / RELATIONAL ALGEBRA EXPRESSION
        |
        ↓
3. QUERY OPTIMIZATION (Select best plan)
        |
        ↓
4. EXECUTION PLAN (Query Code Generator)
        |
        ↓
5. QUERY EVALUATION ENGINE
        |
        ↓
     RESULT
```

**Step 1 — Parsing & Translation:**
- SQL query is scanned and parsed for syntax errors.
- Translated into an internal form — usually a **parse tree** or **relational algebra expression**.
- Validates table names, attribute names, and data types.

**Step 2 — Query Tree / Relational Algebra:**
- The parsed query is converted into a **query tree** (relational algebra expression tree).
- Represents the operations (SELECT, PROJECT, JOIN) to be performed.

**Step 3 — Query Optimization:**
- The optimizer generates multiple **equivalent execution plans**.
- Estimates the **cost** of each plan (based on statistics like table size, indexes available).
- Selects the plan with the **minimum estimated cost**.
- Applies **heuristic rules** (e.g., apply SELECT before JOIN to reduce tuple count).

**Step 4 — Code Generation:**
- The selected execution plan is converted into **executable code** for the query evaluation engine.

**Step 5 — Query Evaluation:**
- The query evaluation engine **executes** the plan.
- Retrieves data from storage and returns the result to the user.

---

#### 🔚 Conclusion:
Query optimization is essential for database performance. Without it, even simple queries could take unacceptably long times on large datasets. The DBMS performs automatic optimization so users can write simple SQL without worrying about efficiency.

---

## ❓ Q2: Entity Integrity, Referential Integrity, Domain Constraints & Key Constraints?

### ✅ Answer:

#### 📖 1. Entity Integrity Constraint
**Definition:** The **Entity Integrity Constraint** states that the **Primary Key of a table cannot be NULL**. Every tuple must be uniquely identifiable.

**Why Important:**
- If the primary key is NULL, the tuple cannot be uniquely identified.
- The fundamental purpose of a primary key is unique identification — a NULL value defeats this purpose.

**Example:**
```sql
CREATE TABLE Student (
    RollNo INT PRIMARY KEY,   -- Cannot be NULL
    Name VARCHAR(50)
);
-- INSERT INTO Student VALUES (NULL, 'Rishav'); → ERROR!
```

---

#### 📖 2. Referential Integrity Constraint
**Definition:** The **Referential Integrity Constraint** states that a **Foreign Key value must either match an existing Primary Key value** in the referenced table, or be NULL. It ensures consistency between related tables.

**Why Important:**
- Prevents orphan records (records in a child table with no matching parent).
- Ensures relationships between tables remain valid.
- Protects against invalid references.

**Example:**
```sql
CREATE TABLE Department (DeptID INT PRIMARY KEY, DeptName VARCHAR(50));

CREATE TABLE Employee (
    EmpID INT PRIMARY KEY,
    Name VARCHAR(50),
    DeptID INT,
    FOREIGN KEY (DeptID) REFERENCES Department(DeptID)
);
-- You cannot insert an Employee with DeptID=99 if DeptID=99 doesn't exist in Department.
```

---

#### 📖 3. Domain Constraints
**Definition:** **Domain Constraints** specify that the value of each attribute must be an **atomic value from the domain (data type)** defined for that attribute. The value must be of the correct type and within the allowed range.

**Example:**
- `Age INT` → Age cannot be 'twenty', must be an integer.
- `Marks DECIMAL(5,2)` → Marks must be a decimal number.
- `Gender CHAR(1) CHECK (Gender IN ('M','F'))` → Only 'M' or 'F' allowed.

---

#### 📖 4. Key Constraints
**Definition:** **Key Constraints** state that in a relation, no two tuples can have the same value for the **key attribute(s)**. The key must be unique across all tuples.

**Types:**
- **Uniqueness:** Key value must be distinct for each tuple.
- **Minimality (for candidate keys):** No subset of the key should itself be a key.

**Example:**
```sql
CREATE TABLE Student (
    RollNo INT UNIQUE NOT NULL,   -- Key constraint: must be unique
    Email VARCHAR(100) UNIQUE     -- Alternative key constraint
);
```

---

#### 📊 Summary Table:

| Constraint         | Rule                                          | Violation Example                          |
|--------------------|-----------------------------------------------|--------------------------------------------|
| Entity Integrity   | Primary Key cannot be NULL                    | INSERT student with NULL RollNo            |
| Referential Integrity | FK must match existing PK or be NULL       | Employee with non-existent DeptID          |
| Domain Constraint  | Value must match attribute's data type/range  | Age = 'twenty' instead of 20              |
| Key Constraint     | Key values must be unique                     | Two students with same RollNo              |

---

#### 🔚 Conclusion:
These four constraints form the foundation of data integrity in relational databases. Entity and referential integrity protect the correctness of relationships, domain constraints enforce valid data types, and key constraints ensure unique identification of every tuple.

---

## ❓ Q3: Disadvantage of Cartesian Product & How to Recover?

### ✅ Answer:

#### 📖 Cartesian Product:
The **Cartesian Product** (×) of two relations R and S produces a relation that contains **every combination of tuples** from R and S.

If R has m tuples and n attributes, and S has p tuples and q attributes:
- Result has **m × p tuples** and **n + q attributes**.

**Example:**
```
R (A, B):         S (C, D):
  1   a              x   10
  2   b              y   20

R × S:
  1   a   x   10
  1   a   y   20
  2   b   x   10
  2   b   y   20
```

---

#### 📝 Disadvantages of Cartesian Product:

**1. Exponential Size:**
- If R has 1000 tuples and S has 1000 tuples → R × S has **1,000,000 tuples**.
- This blows up storage and memory rapidly.

**2. Meaningless Results:**
- Most combinations produced are **semantically meaningless** — unrelated rows are joined together.
- Only a small subset of the Cartesian product is actually meaningful.

**3. Very Slow Performance:**
- Processing millions of unnecessary tuples wastes CPU and I/O resources massively.

**4. Requires Additional Filtering:**
- A SELECT (σ) condition must always be applied after Cartesian product to get meaningful results, making it a two-step process.

---

#### 📝 How to Recover (Solution):

**Use JOIN instead of Cartesian Product + SELECT:**

The **JOIN operation** combines the Cartesian Product and the SELECT condition into a single, efficient operation that only produces meaningful tuples.

```
Instead of:
  σ(condition) (R × S)    ← Inefficient: generates all combinations first

Use:
  R ⋈(condition) S        ← Efficient JOIN: only produces matching tuples
```

**Example:**
```sql
-- BAD (Cartesian Product):
SELECT * FROM Employee, Department WHERE Employee.DeptID = Department.DeptID;

-- GOOD (JOIN):
SELECT * FROM Employee INNER JOIN Department ON Employee.DeptID = Department.DeptID;
```

The JOIN is optimized internally to avoid generating all combinations first.

---

#### 🔚 Conclusion:
Cartesian Product is a theoretically complete but practically expensive operation. It should always be replaced with the appropriate JOIN operation, which applies the join condition during tuple combination to avoid unnecessary intermediate results.

---

## ❓ Q4: Full Outer Join, Left Outer Join, Right Outer Join with Examples. What is Theta Join?

### ✅ Answer:

**Outer Joins** are extensions of the natural join that preserve tuples from one or both relations that do NOT have matching tuples in the other relation. NULL values are used to fill missing attributes.

---

#### 📖 Setup Example Tables:
```
Employee (EmpID, Name, DeptID):     Department (DeptID, DeptName):
  1     Rishav    10                  10    IT
  2     Raj       20                  20    HR
  3     Aryan     NULL                30    Finance
```

---

#### 🔵 1. Left Outer Join (R ⟕ S):
**Definition:** Returns ALL tuples from the **LEFT table** and matching tuples from the right table. If no match, NULL is placed for right table attributes.

```sql
SELECT * FROM Employee LEFT OUTER JOIN Department ON Employee.DeptID = Department.DeptID;
```
```
Result:
EmpID | Name   | DeptID | DeptName
  1   | Rishav |  10    | IT
  2   | Raj    |  20    | HR
  3   | Aryan  | NULL   | NULL       ← Aryan has no DeptID, still included
```

---

#### 🟡 2. Right Outer Join (R ⟖ S):
**Definition:** Returns ALL tuples from the **RIGHT table** and matching tuples from the left table. If no match, NULL is placed for left table attributes.

```sql
SELECT * FROM Employee RIGHT OUTER JOIN Department ON Employee.DeptID = Department.DeptID;
```
```
Result:
EmpID | Name   | DeptID | DeptName
  1   | Rishav |  10    | IT
  2   | Raj    |  20    | HR
NULL  | NULL   |  30    | Finance    ← Finance has no employees, still included
```

---

#### 🔴 3. Full Outer Join (R ⟗ S):
**Definition:** Returns ALL tuples from **BOTH tables**. Non-matching tuples from either side get NULL values for the other side's attributes.

```sql
SELECT * FROM Employee FULL OUTER JOIN Department ON Employee.DeptID = Department.DeptID;
```
```
Result:
EmpID | Name   | DeptID | DeptName
  1   | Rishav |  10    | IT
  2   | Raj    |  20    | HR
  3   | Aryan  | NULL   | NULL       ← From LEFT (no dept)
NULL  | NULL   |  30    | Finance    ← From RIGHT (no employee)
```

---

#### 📊 Summary Table:

| Join Type        | Includes from Left | Includes from Right | NULLs for Non-match  |
|------------------|--------------------|---------------------|----------------------|
| Inner Join       | Matching only      | Matching only       | No NULLs             |
| Left Outer Join  | ALL tuples         | Matching only       | Right side NULLs     |
| Right Outer Join | Matching only      | ALL tuples          | Left side NULLs      |
| Full Outer Join  | ALL tuples         | ALL tuples          | Both sides NULLs     |

---

#### 📖 Theta Join (θ-Join):
**Definition:** A **Theta Join** combines two relations using any comparison condition (θ), not just equality. The condition can use operators: =, ≠, <, >, ≤, ≥.

**Notation:** R ⋈(θ) S

**Example:**
```
R ⋈(R.Salary > S.MinSalary) S
→ Joins tuples where salary in R is greater than minimum salary in S
```

When θ is equality (=), it becomes an **Equi-Join**.
When equi-join result has duplicate columns removed, it becomes a **Natural Join**.

---

#### 🔚 Conclusion:
Outer joins are essential when we need to preserve all data from one or both tables, even when there is no matching record on the other side. They are widely used in reporting and data analysis where completeness of data matters more than strict matching.

---

## ❓ Q5: Unary & Binary Operations in Relational Algebra. What is Projection Operator?

### ✅ Answer:

#### 📖 Relational Algebra:
Relational Algebra is a **procedural query language** that takes one or two relations as input and produces a new relation as output. Its operations are classified as **Unary** and **Binary**.

---

#### 🔵 Unary Operations:
Operations that operate on **only ONE relation**.

**1. SELECT (σ) — Selection:**
- Selects tuples from a relation that satisfy a given condition.
- **Syntax:** σ(condition)(R)
- Example: σ(Age > 20)(Student) → selects all students older than 20.

**2. PROJECT (π) — Projection:**
- Selects specific **columns (attributes)** from a relation.
- Eliminates duplicate tuples in the result.
- **Syntax:** π(attribute list)(R)
- Example: π(Name, Marks)(Student) → shows only Name and Marks columns.

**3. RENAME (ρ):**
- Renames a relation or its attributes.
- **Syntax:** ρ(new_name)(R)

---

#### 🟡 Binary Operations:
Operations that operate on **TWO relations**.

**1. UNION (∪):**
- Combines tuples of two relations and removes duplicates.
- Relations must be **union compatible** (same schema).
- Example: Student_CS ∪ Student_IT → all CS and IT students.

**2. INTERSECTION (∩):**
- Returns tuples that appear in BOTH relations.
- Example: Student_CS ∩ Student_IT → students in both CS and IT.

**3. SET DIFFERENCE (−):**
- Returns tuples in R that are NOT in S.
- Example: Student_CS − Student_IT → CS students not in IT.

**4. CARTESIAN PRODUCT (×):**
- Returns all combinations of tuples from both relations.

**5. JOIN (⋈):**
- Combines related tuples from two relations based on a condition.

---

#### 📊 Summary:

| Operation     | Type    | Symbol | Description                               |
|---------------|---------|--------|-------------------------------------------|
| Select        | Unary   | σ      | Filters rows based on condition           |
| Project       | Unary   | π      | Selects specific columns                  |
| Rename        | Unary   | ρ      | Renames relation/attributes               |
| Union         | Binary  | ∪      | Combines rows from two relations          |
| Intersection  | Binary  | ∩      | Common rows in both relations             |
| Set Difference| Binary  | −      | Rows in R but not in S                    |
| Cartesian Prod| Binary  | ×      | All combinations                          |
| Join          | Binary  | ⋈      | Combines based on condition               |

---

#### 📖 Projection Operator (π) — Detailed:
- **Projects** specific columns from a relation.
- Removes duplicate rows from result (since result is a set).
- Does NOT filter rows — only columns.
- Can be combined with SELECT for powerful queries.

**Example:**
```
Student (RollNo, Name, Age, Marks):
π(Name, Marks)(Student) → shows only Name and Marks of all students
π(Name)(σ(Marks > 80)(Student)) → names of students scoring above 80
```

---

#### 🔚 Conclusion:
Relational algebra provides the theoretical foundation for SQL. Unary operations work on single relations (select rows or columns), while binary operations combine data from multiple relations. The projection operator is fundamental to retrieving only relevant attributes, reducing output size and improving query efficiency.

---

## ❓ Q6: Unsafe Relational Calculus, Tuple Relational Calculus, Database Models, Domain, Union Compatibility

### ✅ Answer:

#### 📖 1. Tuple Relational Calculus (TRC):
**Definition:** TRC is a **non-procedural query language** based on mathematical logic. It specifies WHAT data is needed, not HOW to get it. Queries are expressed using **tuple variables** that range over tuples of a relation.

**Notation:** `{ t | P(t) }` — the set of all tuples t such that predicate P(t) is true.

**Example:**
- Find all students with marks > 80:
  `{ t | t ∈ Student ∧ t.Marks > 80 }`

**Components:**
- **Tuple variable (t):** Ranges over tuples of a relation.
- **Predicate P(t):** A condition that must be satisfied.
- **Existential quantifier (∃):** "There exists"
- **Universal quantifier (∀):** "For all"

---

#### 📖 2. Unsafe Relational Calculus Expression:
**Definition:** A relational calculus expression is **unsafe** if it can generate an **infinite result** — i.e., it may produce tuples that do NOT come from the actual database.

**Why Unsafe:**
- Relational calculus uses logical predicates. If not carefully written, the result set can be theoretically infinite.
- An unsafe expression evaluates to values outside the domain of the database.

**Example of Unsafe Expression:**
`{ t | ¬(t ∈ Student) }` 
→ This means "all tuples NOT in Student" — this is an infinite set because there are infinitely many possible tuples not in Student!

**Safe Expression (Equivalent Safe Version):**
`{ t | t ∈ AllPeople ∧ ¬(t ∈ Student) }`
→ Bound to a finite domain (AllPeople), so the result is finite and safe.

**Rule for Safety:** An expression is safe if all values in the result are from the **domain of the expression** (finite set of values actually appearing in the database).

---

#### 📖 3. Database Models:
A **Database Model** defines the logical structure of how data is organized, stored, and related.

| Model                    | Description                                          | Example        |
|--------------------------|------------------------------------------------------|----------------|
| Hierarchical Model       | Tree structure, parent-child relationships           | IBM IMS        |
| Network Model            | Graph structure, many-to-many relationships          | CODASYL        |
| Relational Model         | Tables (relations) with rows and columns             | MySQL, Oracle  |
| Object-Oriented Model    | Data stored as objects with attributes & methods     | db4o           |
| Entity-Relationship Model| Conceptual model using entities and relationships    | Used in design |
| NoSQL / Document Model   | Flexible schema (JSON, key-value, graph)             | MongoDB        |

---

#### 📖 4. Domain:
**Definition:** A **Domain** is the set of **all possible values** that an attribute can take. It defines the data type and valid range of an attribute.

**Examples:**
- `Age` → Domain: positive integers (1–150)
- `Gender` → Domain: {'M', 'F', 'Other'}
- `Marks` → Domain: real numbers between 0 and 100
- `Name` → Domain: strings of alphabetic characters

In relational calculus, **Domain Relational Calculus (DRC)** uses domain variables that range over domains (attribute values), unlike TRC which uses tuple variables.

---

#### 📖 5. Union Compatibility:
**Definition:** Two relations R and S are said to be **Union Compatible** if they satisfy these conditions:
1. They have the **same number of attributes** (same degree/arity).
2. The **domains of corresponding attributes must be compatible** (same or comparable data types).

**Why Needed:** Union, Intersection, and Set Difference operations require union-compatible relations.

**Example:**
```
R (RollNo INT, Name VARCHAR) — 2 attributes
S (EmpID INT, EmpName VARCHAR) — 2 attributes
→ R and S are union compatible (same degree, compatible domains)

R ∪ S → Valid (though the result may be semantically odd)
```

```
R (RollNo, Name, Marks) — 3 attributes
S (EmpID, EmpName) — 2 attributes
→ NOT union compatible (different degree) → R ∪ S is INVALID
```

---

#### 🔚 Conclusion:
Tuple relational calculus provides a declarative, logic-based approach to querying databases. Unsafe expressions must be avoided to ensure finite, meaningful results. Understanding database models, domains, and union compatibility forms the theoretical backbone of relational database design.

---

## ❓ Q7: Three Numerical Problems in Relational Algebra

### ✅ Answer:

#### 📊 Given Relations:

```
Student (RollNo, Name, Age, DeptID)
1   Rishav   21   CS
2   Raj      22   IT
3   Aryan    20   CS
4   Priya    23   ME

Department (DeptID, DeptName, HOD)
CS   Computer Sci   Dr. Sharma
IT   Info Tech      Dr. Gupta
ME   Mech Engg      Dr. Verma
```

---

#### 🔢 Numerical 1: Find names of all students in the CS department.

**Relational Algebra:**
```
π(Name) ( σ(DeptID = 'CS') (Student) )
```

**Steps:**
1. Apply SELECT: σ(DeptID='CS')(Student)
   → Returns: (1, Rishav, 21, CS) and (3, Aryan, 20, CS)
2. Apply PROJECT: π(Name)
   → Result: {Rishav, Aryan}

**SQL Equivalent:**
```sql
SELECT Name FROM Student WHERE DeptID = 'CS';
```

---

#### 🔢 Numerical 2: Find RollNo and Name of students older than 21.

**Relational Algebra:**
```
π(RollNo, Name) ( σ(Age > 21) (Student) )
```

**Steps:**
1. SELECT: σ(Age > 21)(Student)
   → Returns: (2, Raj, 22, IT), (4, Priya, 23, ME)
2. PROJECT: π(RollNo, Name)
   → Result: {(2, Raj), (4, Priya)}

**SQL Equivalent:**
```sql
SELECT RollNo, Name FROM Student WHERE Age > 21;
```

---

#### 🔢 Numerical 3: Find Name of students along with their Department Name.

**Relational Algebra:**
```
π(Name, DeptName) ( Student ⋈(Student.DeptID = Department.DeptID) Department )
```

**Steps:**
1. JOIN: Student ⋈ Department on DeptID
```
RollNo | Name   | Age | DeptID | DeptName        | HOD
  1    | Rishav | 21  | CS     | Computer Sci    | Dr. Sharma
  2    | Raj    | 22  | IT     | Info Tech       | Dr. Gupta
  3    | Aryan  | 20  | CS     | Computer Sci    | Dr. Sharma
  4    | Priya  | 23  | ME     | Mech Engg       | Dr. Verma
```
2. PROJECT: π(Name, DeptName)
```
Name    | DeptName
Rishav  | Computer Sci
Raj     | Info Tech
Aryan   | Computer Sci
Priya   | Mech Engg
```

**SQL Equivalent:**
```sql
SELECT S.Name, D.DeptName FROM Student S JOIN Department D ON S.DeptID = D.DeptID;
```
---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*
*You've got this! Study well and ace your 6th Semester! 💪*