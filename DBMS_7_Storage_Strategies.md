# 📘 B.Tech 6th Semester — Exam Preparation
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Database Management Systems (DBMS)
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---
# 📂 MODULE 7: **STORAGE STRATEGIES**
---

## ❓ Q1: Four Main Differences Between File-Processing System & DBMS?

### ✅ Answer:

| Difference | File-Processing System | DBMS |
|------------|----------------------|------|
| **1. Data Redundancy & Inconsistency** | Same data stored in multiple files → high redundancy. Updates in one file not reflected in others → inconsistency. | Data stored once in centralized DB → low redundancy. All applications share same data → consistency maintained. |
| **2. Data Access** | Must write specific programs to retrieve data. No ad-hoc query capability. | SQL allows ad-hoc queries. No programming needed for data retrieval. |
| **3. Data Isolation** | Data scattered in different files with different formats → difficult to access related data together. | All data in one integrated system. Relationships maintained. Easy multi-table access. |
| **4. Concurrent Access & Security** | No concurrency control → simultaneous access leads to data corruption. No built-in security — basic file permissions only. | Concurrency control ensures safe simultaneous access. Role-based security with GRANT/REVOKE. |

---

## ❓ Q2: Primary Index, Secondary Index, Clustering Index & Ordered Index?

### ✅ Answer:

#### 📖 Ordered Index:
An **Ordered Index** is an index where the entries are stored in **sorted order** based on the search key. It allows efficient range queries using binary search.

---

#### 📖 1. Primary Index (Sparse Index on Ordered File):
**Definition:** A **Primary Index** is defined on an **ordered data file** on the **ordering key field** (which is also the primary key). It is a sparse index — not every record has an index entry.

**Properties:**
- File must be physically sorted by the primary key.
- One index entry per **block** of the data file (points to first record of each block).
- Number of index entries = Number of disk blocks.
- Also called **Dense or Sparse index on ordered file**.

**Example:**
```
Data File (sorted by EmpID):    Primary Index:
Block 1: EmpID 1,2,3           1 → Block 1
Block 2: EmpID 4,5,6           4 → Block 2
Block 3: EmpID 7,8,9           7 → Block 3
```

---

#### 📖 2. Secondary Index (Dense Index on Unordered Field):
**Definition:** A **Secondary Index** is defined on a **non-ordering field** of the data file. The data file is NOT sorted by the indexed field.

**Properties:**
- Dense index — one entry for every record.
- The data file is not physically sorted by this field.
- Provides alternate access path.
- More entries than primary index → larger index size.

**Example:**
```
If Employee file is ordered by EmpID, but we create a secondary index on DeptID:
Index: DeptID 10 → [Record pointers to all employees in Dept 10]
       DeptID 20 → [Record pointers to all employees in Dept 20]
```

---

#### 📖 3. Clustering Index (Index on Non-Key Ordered Field):
**Definition:** A **Clustering Index** is defined on an **ordered non-key field** (a field that is not the primary key and may have duplicate values, but the file is physically sorted by it).

**Properties:**
- One index entry per **distinct value** of the clustering field.
- Points to the first block containing that value.
- Sparse-like index.

**Example:**
```
Employee file sorted by DeptID (non-key, has duplicates):
DeptID 10: Rishav, Raj, Priya
DeptID 20: Aryan, Suma

Clustering Index:
10 → First block with DeptID=10
20 → First block with DeptID=20
```

---

#### 📊 Comparison Table:

| Feature           | Primary Index      | Secondary Index         | Clustering Index         |
|-------------------|--------------------|-------------------------|--------------------------|
| File Order        | Ordered on key     | Unordered on this field | Ordered on non-key field |
| Field Type        | Key (Primary Key)  | Non-ordering field      | Non-key ordering field   |
| Index Type        | Sparse             | Dense                   | Sparse (per distinct val)|
| Duplicates in Field| No               | Possible                | Yes                      |
| # of Index Entries| One per block      | One per record          | One per distinct value   |

---

## ❓ Q3: Blocking Factor. B-Tree vs B+ Tree Indexing?

### ✅ Answer:

#### 📖 Blocking Factor:
**Definition:** The **Blocking Factor (bfr)** is the number of **records that fit in one disk block (page)**.

**Formula:**
```
Blocking Factor (bfr) = ⌊Block Size / Record Size⌋
```

**Example:**
- Block Size = 1024 bytes
- Record Size = 100 bytes
- bfr = ⌊1024 / 100⌋ = **10 records per block**

**Significance:**
- Higher blocking factor → fewer disk accesses needed → better performance.
- Used to calculate the number of blocks needed: `Blocks = ⌈Number of Records / bfr⌉`

---

#### 📖 B-Tree:

**Definition:** A **B-Tree** (Balanced Tree) is a self-balancing, multi-level tree data structure where:
- Every node can have multiple keys and children.
- All leaf nodes are at the same level (balanced).
- **Data pointers can appear at ANY node** (internal or leaf).
- Used for indexing in databases.

**Properties:**
- Order m B-Tree: each node has at most m children and m-1 keys.
- Search, Insert, Delete: O(log n).
- Data records can be retrieved from any level.

---

#### 📖 B+ Tree:

**Definition:** A **B+ Tree** is an extension of B-Tree where:
- **All actual data pointers are stored ONLY at leaf nodes**.
- Internal nodes store only keys (no data) — used purely for navigation.
- Leaf nodes are linked together in a **linked list** for efficient range queries.
- Most modern DBMS use B+ Trees for indexing (MySQL InnoDB, Oracle).

**Properties:**
- Leaf nodes contain all key values + data pointers.
- Internal nodes contain only keys for routing.
- Range queries are very efficient (traverse linked leaf list).

---

#### 📊 B-Tree vs B+ Tree:

| Feature                 | B-Tree                             | B+ Tree                            |
|-------------------------|------------------------------------|------------------------------------|
| Data Pointers           | At any node (internal or leaf)     | ONLY at leaf nodes                 |
| Key Redundancy          | Keys appear only once              | Keys may be repeated in leaves     |
| Leaf Node Linking       | Not linked                         | Leaf nodes linked as linked list   |
| Range Queries           | Less efficient                     | Very efficient (linked leaves)     |
| Space Usage             | Data in internal nodes wastes space| Internal nodes compact (keys only) |
| Height                  | Generally taller                   | Generally shorter (more fan-out)   |
| Preferred In            | File systems                       | DBMS indexing (MySQL, Oracle)      |

---

#### 📊 B+ Tree Diagram:
```
        [30 | 70]               ← Internal node (keys only)
       /     |     \
   [10|20] [40|60] [80|90]      ← Internal nodes
   /  |  \    ...
[5][15][25]...                  ← Leaf nodes (data pointers here)
 → linked list → → → →
```

---

## ❓ Q4: Causes of Bucket Overflow in Hash File Organization?

### ✅ Answer:

#### 📖 Hash File Organization:
In hash file organization, a **hash function** maps a search key to a **bucket** (disk block). Each bucket stores a set of records with the same hash value.

#### 📖 Bucket Overflow:
**Bucket overflow** occurs when a bucket is **full and cannot accommodate more records** with the same hash value.

---

#### 📝 Causes of Bucket Overflow:

**1. Insufficient Buckets:**
- The total number of buckets allocated is too small for the number of records.
- More records exist than buckets × bucket capacity.

**2. Skewed Distribution (Non-Uniform Hashing):**
- A poor hash function distributes records unevenly.
- Many records hash to the same bucket while others are nearly empty.
- Called **clustering** or **hash skew**.

**3. Growth of Database:**
- The database was initially sized for a smaller number of records.
- As data grows, existing buckets overflow while the hash table is not resized.

**4. Multiple Records with Same Key Value:**
- If many records have the same key value, they all hash to the same bucket → overflow.

**5. Poor Hash Function:**
- A hash function that does not uniformly distribute values across all buckets.

---

#### 📝 Solutions to Bucket Overflow:
- **Overflow Chaining:** Attach overflow buckets (linked list) to the full bucket.
- **Open Addressing:** Find another empty bucket using linear/quadratic probing.
- **Dynamic Hashing (Extendible Hashing):** Hash table dynamically expands to handle more records.
- **Linear Hashing:** Incrementally expand the hash file as it grows.

---

## ❓ Q5: Dense & Sparse Indexing with Examples?

### ✅ Answer:

#### 📖 Dense Index:
**Definition:** A **Dense Index** contains **one index entry for every record** in the data file. Every search key value appears in the index.

**Properties:**
- Larger index size (one entry per record).
- Faster search — every record has a direct pointer.
- Can determine if a record exists without accessing data file.
- Used in Secondary Indexes.

**Example:**
```
Data File:         Dense Index:
Record 1 (ID=1)   1 → Pointer to Record 1
Record 2 (ID=2)   2 → Pointer to Record 2
Record 3 (ID=3)   3 → Pointer to Record 3
Record 4 (ID=4)   4 → Pointer to Record 4
```

---

#### 📖 Sparse Index:
**Definition:** A **Sparse Index** contains index entries for **only some records** — typically one entry per disk block (pointing to the first record of each block).

**Properties:**
- Smaller index size (one entry per block).
- Requires the data file to be physically sorted.
- Slower than dense (may need sequential scan within a block).
- Used in Primary Indexes.

**Example:**
```
Block 1: Records ID 1,2,3     Sparse Index:
Block 2: Records ID 4,5,6     1 → Block 1
Block 3: Records ID 7,8,9     4 → Block 2
                               7 → Block 3
```

---

#### 📊 Comparison:

| Feature           | Dense Index                    | Sparse Index                      |
|-------------------|--------------------------------|-----------------------------------|
| Entries Per       | One per RECORD                 | One per BLOCK                     |
| Index Size        | Large                          | Small                             |
| File Requirement  | Not necessarily sorted         | Must be sorted                    |
| Search Speed      | Faster                         | Slightly slower (scan within block)|
| Used In           | Secondary Index                | Primary Index                     |

---

## ❓ Q6: What is File Indexing?

### ✅ Answer:

#### 📖 Definition:
**File Indexing** is the process of creating a **supplementary data structure (index)** on a file that provides faster access to records without scanning the entire file.

An **index** is like a book's table of contents — instead of reading the whole book, you look up the page number in the index and go directly to the relevant page.

#### 📝 Why File Indexing?
- Without indexing, searching requires **linear scan** → O(n) time → very slow for large files.
- With indexing, search takes **O(log n)** time using binary search on the index.

#### 📝 Structure of an Index:
An index file contains entries in the form: `(Search Key, Pointer to Record/Block)`

#### 📝 Types of File Indexes:
- Primary Index, Secondary Index, Clustering Index (described in Q2).
- Dense Index, Sparse Index (described in Q5).
- B-Tree Index, B+ Tree Index (described in Q3).
- Hash Index (based on hashing).

#### 💡 Example:
```sql
-- Create an index on Name column of Student table
CREATE INDEX idx_student_name ON Student(Name);

-- Now searches on Name are faster:
SELECT * FROM Student WHERE Name = 'Rishav';
-- DBMS uses the index instead of scanning all rows
```

---

## ❓ Q7: Different Hashing Techniques?

### ✅ Answer:

#### 📖 Hashing:
**Hashing** is a technique that uses a **hash function** to directly compute the address (bucket) of a record based on its key value, providing O(1) average-case access time.

`Hash Address = h(Key)`

---

#### 📝 Hashing Techniques:

**1. Static Hashing:**
- Fixed number of buckets allocated at the start.
- Hash function: `h(K) = K mod M` where M = number of buckets.
- Problem: Overflow if data grows beyond capacity.

**Example:**
```
M = 5 buckets, Key = 23
h(23) = 23 mod 5 = 3 → Store in Bucket 3
```

---

**2. Dynamic Hashing (Extendible Hashing):**
- The hash table **dynamically expands** as the database grows.
- Uses a **directory** with a global depth.
- When a bucket overflows, it **splits** and the directory doubles.
- No overflow buckets needed.

---

**3. Linear Hashing:**
- Expands hash file **one bucket at a time** (linearly).
- Uses a split pointer that moves through buckets.
- More gradual expansion than extendible hashing.

---

**4. Open Addressing (Collision Resolution):**
When two keys hash to the same bucket (collision), find another empty slot:

- **Linear Probing:** Try next slot: h(k)+1, h(k)+2, ...
- **Quadratic Probing:** Try h(k)+1², h(k)+2², h(k)+3², ...
- **Double Hashing:** Use second hash function to determine step size.

---

**5. Chaining (Overflow Chaining):**
- Each bucket has a **linked list** of overflow records.
- Collisions handled by adding to the chain.
- Simple but performance degrades with long chains.

---

#### 📊 Summary:

| Technique           | How It Works                            | Advantage                        |
|---------------------|-----------------------------------------|----------------------------------|
| Static Hashing      | Fixed buckets, simple mod function      | Simple, fast                     |
| Dynamic (Extendible)| Directory doubles on overflow           | No overflow, handles growth      |
| Linear Hashing      | Splits one bucket at a time             | Gradual, controlled growth       |
| Open Addressing     | Find next available slot                | No extra storage                 |
| Chaining            | Overflow linked list per bucket         | Simple collision handling        |

---

## ❓ Q8: Vertical & Horizontal Fragmentation. Differences?

### ✅ Answer:

#### 📖 Fragmentation in Distributed Databases:
**Fragmentation** is the process of **dividing a relation into smaller pieces (fragments)** that are stored at different sites in a distributed database.

**Why Fragment?**
- Store data closer to where it is most frequently used.
- Improve local query performance.
- Enable parallel processing.

---

#### 📖 Horizontal Fragmentation:
**Definition:** **Horizontal Fragmentation** divides a relation into **subsets of rows (tuples)**. Each fragment contains a subset of rows from the original table, selected based on a condition (like a SELECT operation).

**How:** Like a selection (σ) operation — rows are divided based on a predicate.

**Example:**
```
Employee Table:
EmpID | Name   | DeptID | City
  1   | Rishav | CS     | Delhi
  2   | Raj    | IT     | Mumbai
  3   | Aryan  | CS     | Delhi

Horizontal Fragmentation:
Fragment 1 (Delhi employees) → stored at Delhi site:
  1 | Rishav | CS | Delhi
  3 | Aryan  | CS | Delhi

Fragment 2 (Mumbai employees) → stored at Mumbai site:
  2 | Raj | IT | Mumbai
```

**Reconstruction:** UNION of all fragments gives original table.

---

#### 📖 Vertical Fragmentation:
**Definition:** **Vertical Fragmentation** divides a relation into **subsets of columns (attributes)**. Each fragment contains a subset of columns from the original table (like a PROJECT operation). The primary key is included in EVERY fragment for reconstruction.

**How:** Like a projection (π) operation — columns are divided.

**Example:**
```
Employee Table:
EmpID | Name | Salary | DeptID | PhoneNo

Vertical Fragmentation:
Fragment 1 (HR data) → stored at HR site:
  EmpID | Name | DeptID

Fragment 2 (Finance data) → stored at Finance site:
  EmpID | Salary

(EmpID included in both for reconstruction via JOIN)
```

**Reconstruction:** JOIN of all fragments on primary key gives original table.

---

#### 📊 Difference Table:

| Feature              | Horizontal Fragmentation          | Vertical Fragmentation             |
|----------------------|-----------------------------------|------------------------------------|
| Divides              | Rows (tuples)                     | Columns (attributes)               |
| Based On             | A condition/predicate on rows     | Selection of attribute groups      |
| Operation Type       | Like SELECT (σ)                   | Like PROJECT (π ⌋                  |
| Primary Key          | Included in each fragment         | Must be included in every fragment |
| Reconstruction       | UNION of all fragments            | JOIN of all fragments on PK        |
| Example              | Employees by city/region          | Employee data by department access |
| Use Case             | Geographic distribution           | Functional/departmental separation |

---

#### 🔚 Conclusion:
Horizontal fragmentation is ideal when different groups of users at different locations need different subsets of rows. Vertical fragmentation is useful when different applications need access to different attributes of the same entities. Both strategies optimize data locality and query performance in distributed databases.

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*