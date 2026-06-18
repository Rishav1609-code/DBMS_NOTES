# 📘 B.Tech 6th Semester — Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Database Management Systems (DBMS)
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 8: **FUNCTIONAL DEPENDENCIES & NORMALIZATION**
---

## ❓ Q1: Normalization, Functional Dependency, All Key Concepts?

### ✅ Answer:

#### 📖 Normalization:
**Normalization** is the process of **organizing a relational database** to reduce **data redundancy** and improve **data integrity** by decomposing tables into smaller, well-structured tables using **functional dependencies** as the theoretical basis.

**Goals:**
- Eliminate redundant data (same data stored multiple times).
- Ensure data dependencies make sense.
- Prevent anomalies: **Insertion, Deletion, Update anomalies**.

**Anomalies (Problems Normalization Solves):**
- **Insertion Anomaly:** Cannot insert data without inserting other unrelated data.
- **Deletion Anomaly:** Deleting a record causes loss of other important data.
- **Update Anomaly:** Updating one record requires updating multiple copies.

---

#### 📖 Functional Dependency (FD):
**Definition:** A **Functional Dependency** X → Y means that the value of attribute(s) X **uniquely determines** the value of attribute(s) Y in a relation. "X functionally determines Y."

**Notation:** X → Y (X determines Y)

**Example:**
```
Student(RollNo, Name, Age, DeptID, DeptName)
RollNo → Name     (Roll number determines name) ✅
RollNo → Age      (Roll number determines age) ✅
DeptID → DeptName (DeptID determines department name) ✅
```

---

#### 📖 Prime Key Attribute & Non-Prime Key Attribute:
- **Prime Attribute:** An attribute that is part of **any candidate key** of the relation.
- **Non-Prime Attribute:** An attribute that is **NOT part of any candidate key**.

**Example:**
- Relation: `Student(RollNo, Name, Marks, DeptID)`
- Candidate Key: `{RollNo}`
- **Prime Attribute:** `RollNo`
- **Non-Prime Attributes:** `Name`, `Marks`, `DeptID`

---

#### 📖 Full Functional Dependency:
**Definition:** X → Y is a **Full Functional Dependency** if Y is functionally dependent on X, and Y is **NOT** dependent on any **proper subset** of X.

**Example:**
- `{StudentID, CourseID} → Grade` — Grade depends on BOTH StudentID AND CourseID (not on either alone). This is a **full** FD.

---

#### 📖 Partial Functional Dependency:
**Definition:** X → Y is a **Partial Functional Dependency** if Y is functionally dependent on X, but Y is also dependent on a **proper subset** of X (i.e., part of the key determines Y).

**Example:**
- `{StudentID, CourseID} → StudentName` — StudentName depends only on StudentID (not on CourseID). This is a **partial** FD.
- Partial dependencies violate **2NF**.

---

#### 📖 Trivial Functional Dependency:
**Definition:** X → Y is **Trivial** if Y is a **subset of X** (Y ⊆ X). It always holds true and is not useful for normalization.

**Example:**
- `{RollNo, Name} → RollNo` — trivial (RollNo is a subset of the left side) ✅
- `{RollNo, Name} → {RollNo, Name}` — trivial ✅

---

#### 📖 Non-Trivial Functional Dependency:
**Definition:** X → Y is **Non-Trivial** if Y is **NOT a subset of X** (Y ⊄ X). These are the meaningful dependencies.

**Example:**
- `RollNo → Name` — non-trivial ✅ (Name is not a subset of RollNo)

---

#### 📖 Transitive Functional Dependency:
**Definition:** X → Z is a **Transitive Dependency** if there exists Y such that X → Y and Y → Z (where Y is NOT a candidate key and Y does not determine X).

**Example:**
```
RollNo → DeptID (RollNo determines DeptID)
DeptID → DeptName (DeptID determines DeptName)
→ RollNo → DeptName is a TRANSITIVE dependency
```
- Transitive dependencies violate **3NF**.

---

#### 📖 Non-Transitive Functional Dependency:
**Definition:** X → Y is **Non-Transitive** if Y cannot be derived through a chain of dependencies via another non-key attribute. The dependency is direct.

**Example:**
- `RollNo → Name` — direct, non-transitive ✅

---

#### 📖 Closure of Attributes:
**Definition:** The **Closure** of a set of attributes X (written as X⁺) is the **set of all attributes** that can be functionally determined from X using the given set of FDs.

**Algorithm:**
1. Start: X⁺ = X
2. For each FD A → B: if A ⊆ X⁺, then add B to X⁺
3. Repeat until no more attributes can be added.

**Example:**
```
Attributes: {A, B, C, D, E}
FDs: A → B, B → C, C → D
Find closure of A: A⁺
Step 1: A⁺ = {A}
Step 2: A → B → A⁺ = {A, B}
Step 3: B → C → A⁺ = {A, B, C}
Step 4: C → D → A⁺ = {A, B, C, D}
Result: A⁺ = {A, B, C, D}
```

**Use:** Find candidate keys — if X⁺ = all attributes, then X is a superkey.

---

#### 📖 Inclusion Dependency:
**Definition:** An **Inclusion Dependency** (IND) is a constraint stating that the values in one set of attributes must be a **subset of values** in another set of attributes. It generalizes the concept of referential integrity (foreign key).

**Notation:** R[X] ⊆ S[Y] — values of X in relation R must appear in Y in relation S.

**Example:**
- `Employee[DeptID] ⊆ Department[DeptID]`
- Every DeptID in Employee must exist in Department — this IS a foreign key constraint.
- Inclusion dependencies capture referential integrity formally.

---

## ❓ Q2: Explain 1NF, 2NF, 3NF, BCNF, 4NF, 5NF

### ✅ Answer:

#### 📊 Example Relation (Unnormalized):
```
StudentCourse(StudentID, StudentName, CourseID, CourseName, Instructor, InstructorPhone, Grade)
FDs:
StudentID → StudentName
CourseID → CourseName, Instructor
Instructor → InstructorPhone
{StudentID, CourseID} → Grade
```

---

#### 🔵 1NF — First Normal Form:

**Rule:** A relation is in **1NF** if:
1. All attributes contain **atomic (indivisible) values** — no multivalued or composite attributes.
2. All values in a column are of the same domain.
3. Each row is unique (has a primary key).

**Violation Example (NOT in 1NF):**
```
Student(RollNo, Name, PhoneNumbers)
1  Rishav  [9876543210, 7654321098]  ← Multiple phone numbers in one cell (non-atomic)
```

**Fix:** Separate into atomic values:
```
Student(RollNo, Name, PhoneNumber)
1  Rishav  9876543210
1  Rishav  7654321098
```

**1NF eliminates:** Repeating groups, non-atomic values.

---

#### 🟡 2NF — Second Normal Form:

**Rule:** A relation is in **2NF** if:
1. It is in **1NF**.
2. Every non-prime attribute is **fully functionally dependent** on the entire primary key.
3. No **partial dependencies** exist (no non-key attribute depends on part of the key).

**Applicable when:** Primary key is composite.

**Violation Example (in 1NF, NOT in 2NF):**
```
StudentCourse(StudentID, CourseID, StudentName, CourseName, Grade)
PK: {StudentID, CourseID}

StudentID → StudentName  (PARTIAL — StudentName depends only on part of PK)
CourseID → CourseName    (PARTIAL — CourseName depends only on part of PK)
{StudentID, CourseID} → Grade  (FULL — Grade depends on entire PK) ✅
```

**Fix (Decompose to remove partial dependencies):**
```
Student(StudentID, StudentName)         ← StudentID is PK
Course(CourseID, CourseName)            ← CourseID is PK
Enrollment(StudentID, CourseID, Grade)  ← {StudentID, CourseID} is composite PK
```

**2NF eliminates:** Partial functional dependencies.

---

#### 🔴 3NF — Third Normal Form:

**Rule:** A relation is in **3NF** if:
1. It is in **2NF**.
2. No **transitive dependencies** exist — no non-prime attribute depends on another non-prime attribute.

**Violation Example (in 2NF, NOT in 3NF):**
```
Course(CourseID, CourseName, Instructor, InstructorPhone)
PK: CourseID

CourseID → Instructor    (direct dependency) ✅
Instructor → InstructorPhone  (TRANSITIVE — non-prime → non-prime)
→ CourseID → InstructorPhone is TRANSITIVE dependency ❌
```

**Fix:**
```
Course(CourseID, CourseName, Instructor)
Instructor(Instructor, InstructorPhone)
```

**3NF eliminates:** Transitive functional dependencies.

---

#### 🟢 BCNF — Boyce-Codd Normal Form:

**Rule:** A relation is in **BCNF** if:
1. It is in **3NF**.
2. For every non-trivial FD X → Y, **X must be a superkey**.
3. The left-hand side of every FD must be a candidate key (stricter than 3NF).

**When 3NF is NOT BCNF:**
- A prime attribute is functionally dependent on another attribute (non-key determines prime attribute).

**Violation Example (in 3NF, NOT in BCNF):**
```
CourseTeacher(StudentID, CourseID, Instructor)
FDs:
{StudentID, CourseID} → Instructor  (composite PK)
Instructor → CourseID               (Instructor determines CourseID — but Instructor is not a superkey!)
```
→ Instructor → CourseID violates BCNF (Instructor is not a superkey).

**Fix:**
```
InstructorCourse(Instructor, CourseID)
StudentInstructor(StudentID, Instructor)
```

**BCNF eliminates:** All redundancy based on FDs (stricter than 3NF, but may lose lossless join or dependency preservation).

---

#### 🟣 4NF — Fourth Normal Form:

**Rule:** A relation is in **4NF** if:
1. It is in **BCNF**.
2. It has **no non-trivial multi-valued dependencies (MVDs)** except those where the left side is a superkey.

**Multi-Valued Dependency (MVD):** X →→ Y means for each value of X, there is a set of values for Y, independent of other attributes Z.

**Violation Example:**
```
Employee(EmpID, Skill, Project)
EmpID →→ Skill   (An employee has multiple skills — independent of projects)
EmpID →→ Project (An employee works on multiple projects — independent of skills)
```
→ This causes unnecessary cross-product of skills and projects.

**Fix:**
```
EmpSkill(EmpID, Skill)
EmpProject(EmpID, Project)
```

**4NF eliminates:** Multi-valued dependencies (MVDs).

---

#### 🔶 5NF — Fifth Normal Form (Project-Join Normal Form / PJNF):

**Rule:** A relation is in **5NF** if:
1. It is in **4NF**.
2. Every **join dependency** in the relation is implied by the candidate keys.
3. The relation cannot be decomposed further without losing information.

**Join Dependency:** A relation R satisfies a join dependency if R can be reconstructed by joining multiple projections of itself.

**5NF eliminates:** Redundancy due to join dependencies — ensures lossless decomposition at the finest level.

**Example:**
```
Supplier-Part-Project(SupplierID, PartID, ProjectID)
If this relation has a join dependency that cannot be explained by the candidate key,
it violates 5NF and must be decomposed into:
SupplierPart(SupplierID, PartID)
SupplierProject(SupplierID, ProjectID)
PartProject(PartID, ProjectID)
```

---

#### 📊 Complete Normalization Summary:

| Normal Form | Condition                                      | Eliminates                         |
|-------------|------------------------------------------------|------------------------------------|
| 1NF         | All atomic values, no repeating groups         | Non-atomic / multi-valued cells    |
| 2NF         | 1NF + No partial FDs on prime key              | Partial functional dependencies    |
| 3NF         | 2NF + No transitive FDs                        | Transitive functional dependencies |
| BCNF        | 3NF + Every FD determinant is a superkey       | Remaining FD-based redundancy      |
| 4NF         | BCNF + No non-trivial MVDs                     | Multi-valued dependencies          |
| 5NF         | 4NF + No non-trivial join dependencies         | Join-based redundancy              |

---

#### 📊 Step-by-Step Normalization Flowchart:
```
UNNORMALIZED RELATION
        |
     [1NF] — Remove non-atomic values, repeating groups
        |
     [2NF] — Remove partial dependencies (on composite PK)
        |
     [3NF] — Remove transitive dependencies
        |
    [BCNF] — Every FD determinant must be a superkey
        |
     [4NF] — Remove multi-valued dependencies
        |
     [5NF] — Remove join dependencies
        |
  FULLY NORMALIZED RELATION
```

---

#### 🔚 Conclusion:
Normalization is a systematic process of refining database design to eliminate redundancy and anomalies. Each normal form builds upon the previous, addressing more subtle forms of dependency and redundancy. In practice, most databases are normalized to 3NF or BCNF, which provides a good balance between normalization benefits and query performance.

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*