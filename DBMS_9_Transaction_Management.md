# 📘 B.Tech 6th Semester — Transaction Processing
**Author:** Rishav Raj | **Semester:** 6th Sem | **Subject:** DBMS — Transaction Management
**Marks per Answer:** 5–10 Marks | **Format:** 2–3 Pages per Answer

---

# ⭐ NUMERICAL ON SERIALIZABILITY (MUST PRACTICE)

## 📊 Key Concepts for Numericals:

### What is a Schedule?
A **Schedule** is a sequence of operations (Read/Write) from multiple transactions executed in some order.

### Conflict Serializability Test — Precedence Graph Method:

**Steps:**
1. Identify all **conflicting operations** between transactions.
2. Two operations conflict if:
   - They belong to **different transactions**
   - They operate on the **same data item**
   - At least **one of them is a WRITE**
3. Draw a **Precedence Graph (Serialization Graph)**:
   - Node for each transaction Ti
   - Edge Ti → Tj if Ti's operation conflicts with and precedes Tj's operation
4. If graph has **NO cycle** → Schedule is **Conflict Serializable** ✅
5. If graph has a **cycle** → Schedule is **NOT Conflict Serializable** ❌

---

### 🔢 Numerical 1:

**Schedule S:**
```
T1: R(A), W(A),         R(B), W(B)
T2:         R(A), W(A),       R(B), W(B)
```

**Full Schedule (interleaved):**
```
Step 1: T1 reads A      → R1(A)
Step 2: T1 writes A     → W1(A)
Step 3: T2 reads A      → R2(A)
Step 4: T2 writes A     → W2(A)
Step 5: T1 reads B      → R1(B)
Step 6: T1 writes B     → W1(B)
Step 7: T2 reads B      → R2(B)
Step 8: T2 writes B     → W2(B)
```

**Find Conflicts:**
| Conflict Pair     | Operations          | Reason                         | Edge    |
|-------------------|---------------------|--------------------------------|---------|
| W1(A) before R2(A)| Both on A, W1 first | Write-Read conflict            | T1 → T2 |
| W1(A) before W2(A)| Both on A, W1 first | Write-Write conflict           | T1 → T2 |
| W1(B) before R2(B)| Both on B, W1 first | Write-Read conflict            | T1 → T2 |
| W1(B) before W2(B)| Both on B, W1 first | Write-Write conflict           | T1 → T2 |

**Precedence Graph:**
```
T1 ──────→ T2
```
**No cycle** → Schedule is **CONFLICT SERIALIZABLE** ✅
**Equivalent serial schedule:** T1 → T2

---

### 🔢 Numerical 2:

**Schedule S:**
```
T1: R(A),       W(A)
T2:       R(A),       W(A)
T3:                         R(A), W(A)
```

**Interleaved Operations:**
```
R1(A), R2(A), W1(A), W2(A), R3(A), W3(A)
```

**Find Conflicts:**
| Conflict Pair     | Operations         | Edge    |
|-------------------|--------------------|---------|
| R1(A) before W2(A)| Read-Write on A   | T1 → T2 |
| R2(A) before W1(A)| Read-Write on A   | T2 → T1 |

**Precedence Graph:**
```
T1 ──→ T2
T2 ──→ T1
```
**CYCLE EXISTS (T1 → T2 → T1)** → Schedule is **NOT CONFLICT SERIALIZABLE** ❌

---

### 🔢 Numerical 3 (Classic Exam Problem):

**Schedule S:**
```
T1: R(X), R(Y),       W(X)
T2:             R(Y),       W(Y)
T3:                               R(X), W(X), W(Y)
```

**Step-by-step operations:**
```
1. T1: R(X)
2. T1: R(Y)
3. T2: R(Y)
4. T1: W(X)
5. T2: W(Y)
6. T3: R(X)
7. T3: W(X)
8. T3: W(Y)
```

**Find Conflicts:**
| Operations         | Same Item | Conflict Type  | Edge    |
|--------------------|-----------|----------------|---------|
| W1(X) before R3(X) | X        | Write-Read     | T1 → T3 |
| W1(X) before W3(X) | X        | Write-Write    | T1 → T3 |
| R2(Y) before W3(Y) | Y        | Read-Write     | T2 → T3 |
| W2(Y) before W3(Y) | Y        | Write-Write    | T2 → T3 |

**Precedence Graph:**
```
T1 ──→ T3
T2 ──→ T3
```
**No cycle** → Schedule is **CONFLICT SERIALIZABLE** ✅
**Equivalent serial schedules:** T1→T2→T3 or T2→T1→T3

---

### View Serializability Check:

A schedule S is **View Serializable** if it is view equivalent to some serial schedule.

**Three conditions for View Equivalence (S1 ≡v S2):**
1. **Initial Read:** If Ti reads initial value of X in S1, Ti must read initial value of X in S2.
2. **Updated Read:** If Ti reads value written by Tj in S1, same must hold in S2.
3. **Final Write:** If Ti does the final write on X in S1, Ti must do final write on X in S2.

**Note:** Every conflict serializable schedule is view serializable. But NOT vice versa.

---
---

## ❓ Question 1: Two Phase Locking Protocol. Locking vs Timestamp. Multi-Version 2PL. Dirty Read, Non-Repeatable Read.

### ✅ Answer:

---

#### 📖 What is Two Phase Locking (2PL) Protocol?

**Two Phase Locking (2PL)** is a concurrency control protocol that ensures **conflict serializability** by requiring that all lock acquisitions and releases by a transaction follow a strict two-phase discipline.

**Two Phases:**

**Phase 1 — Growing Phase:**
- The transaction can **acquire locks** (shared or exclusive).
- The transaction **cannot release** any lock in this phase.
- The transaction accumulates all the locks it needs.

**Phase 2 — Shrinking Phase:**
- The transaction can **release locks**.
- The transaction **cannot acquire** any new lock in this phase.
- Once the first lock is released, no new lock can be acquired.

```
Locks Held
    ↑
    |        *
    |      *   *
    |    *       *
    |  *           *
    +──────────────────→ Time
    |←Growing→|←Shrinking→|
    |  Phase   |   Phase   |
              ↑
        Lock Point
      (max locks held)
```

---

#### 📖 How 2PL Guarantees Serializability:

**Theorem:** A schedule produced by transactions following 2PL is **conflict serializable**.

**Proof (intuition):**
- The **lock point** of a transaction is the moment it acquires its last lock (end of growing phase).
- The serial order of transactions is determined by the order of their lock points.
- Since no transaction can acquire a lock after releasing one, the conflict ordering is consistent with the serial order based on lock points.
- This ensures a **cycle-free precedence graph** → conflict serializable.

---

#### 📖 Variants of 2PL:

**1. Basic 2PL:**
- Follows two phases as described above.
- Does NOT prevent cascading rollbacks (if T1 releases a lock and T2 reads dirty data, then T1 aborts → T2 must also abort).

**2. Strict 2PL:**
- All **exclusive (write) locks** are held until the transaction **commits or aborts**.
- Prevents cascading rollbacks and dirty reads.
- Most commonly used in practice.

**3. Rigorous 2PL:**
- ALL locks (shared and exclusive) are held until commit/abort.
- Strictest form — ensures recoverability and prevents all anomalies.

**4. Conservative 2PL (Static 2PL):**
- Transaction acquires ALL required locks before it starts execution.
- Prevents deadlocks but reduces concurrency.

---

#### 📖 Locking vs Timestamp Protocols:

| Feature                | Locking Protocol (2PL)               | Timestamp Protocol                         |
|------------------------|--------------------------------------|--------------------------------------------|
| Basic Mechanism        | Transactions acquire locks on data   | Each transaction gets a unique timestamp   |
| Timestamp Used?        | No                                   | Yes — TS(Ti) assigned at start             |
| Deadlock Possible?     | YES — transactions can wait for each other | NO — no waiting, transactions rolled back |
| Concurrency            | Moderate                             | Higher (no blocking)                       |
| Conflict Resolution    | Block (wait for lock release)        | Abort and restart the younger transaction  |
| Starvation Possible?   | Possible                             | Possible (if rollback loop occurs)         |
| Serializability        | Conflict serializable                | Ensures timestamp-order serializability    |
| Overhead               | Lock table management                | Timestamp comparison per operation         |
| Implementation         | Lock manager needed                  | Simpler — just compare timestamps          |
| Schedule Type          | Based on lock acquisition order      | Based on timestamp ordering                |

---

#### 📖 Multi-Version Two Phase Locking (MV-2PL):

**Definition:**
In **Multi-Version 2PL**, the DBMS maintains **multiple versions** of each data item. Each write creates a new version, and read operations can access older versions — allowing reads and writes to proceed without blocking each other.

**How it works:**
- Each data item X has multiple versions: X1, X2, X3, ... (each version has a timestamp).
- When transaction Ti **reads X**, it reads the version of X with the largest timestamp ≤ TS(Ti).
- When transaction Ti **writes X**, it creates a new version Xi with timestamp TS(Ti).
- **Read-only transactions** never block — they always find an appropriate old version.
- **Update transactions** still use 2PL among themselves.

**Advantage:** Read-only transactions proceed without any locking, dramatically improving concurrency in read-heavy workloads.

---

#### 📖 Concurrency Problems:

**1. Dirty Read:**
**Definition:** A transaction T2 reads a value that was **written by T1 but T1 has not yet committed**. If T1 later aborts, T2 has read invalid (dirty) data.

```
T1: Write(X=50)     ← T1 writes X (not committed yet)
T2:         Read(X) ← T2 reads X=50 (dirty read!)
T1:             ABORT ← T1 rolls back X to old value
T2 now has wrong value of X!
```

**2. Non-Repeatable Read:**
**Definition:** A transaction T1 **reads the same data item twice** but gets **different values** because T2 modified and committed the data between T1's two reads.

```
T1: Read(X)=50              ← T1 reads X=50
T2:          Write(X=80), COMMIT ← T2 updates X
T1:                  Read(X)=80  ← T1 reads X=80 (DIFFERENT value!)
```
T1 reads the same X twice but gets different results — non-repeatable.

**3. Phantom Read (Bonus):**
- T1 executes a query returning a set of rows.
- T2 inserts a new row matching T1's query condition and commits.
- T1 repeats the query and gets a different set (new "phantom" row appears).

---

#### 🔚 Conclusion:
Two Phase Locking is the most widely used concurrency control protocol guaranteeing serializability. Its variants (Strict, Rigorous) address cascading rollback problems. Timestamp protocols offer deadlock-free execution at the cost of rollbacks. Multi-version 2PL dramatically improves read concurrency by maintaining multiple data versions.

---
---

## ❓ Question 2: Recovery Using LOG Files — Immediate & Deferred Updation. Phantoms. Two-Phase Commit.

### ✅ Answer:

---

#### 📖 Log-Based Recovery:
A **Log** (also called Journal) is a sequence of log records maintained on stable storage. Before any change is made to the database, the change is first recorded in the log (**Write-Ahead Logging — WAL**).

**Each log record contains:**
- Transaction ID (Ti)
- Data item name (X)
- Old value (before image)
- New value (after image)
- Type: START, COMMIT, ABORT

---

#### 🔵 a. Immediate Update (Undo Logging):

**Rule:** Changes are **written to the database immediately** — even before the transaction commits. The log records the OLD value (before image) so we can UNDO if needed.

**Log Record Format:** `<Ti, X, old_value, new_value>`

**Recovery Actions:**
- If Ti **committed** → No action needed (changes already written).
- If Ti was **active** at crash → **UNDO** Ti using old_value from log.

**Example:**
```
Transaction T1: Transfers ₹500 from Account A (₹1000) to Account B (₹500)

LOG RECORDS (written before DB changes):
1. <T1, START>
2. <T1, A, 1000, 500>   ← old value of A = 1000, new = 500 
3. Database: A = 500     ← Immediate write to DB
4. <T1, B, 500, 1000>   ← old value of B = 500, new = 1000
5. Database: B = 1000    ← Immediate write to DB
6. <T1, COMMIT>

CRASH SCENARIO 1 — Crash after step 3 (before commit):
→ T1 not committed → UNDO: Restore A = 1000 (from log record 2)

CRASH SCENARIO 2 — Crash after step 6 (after commit):
→ T1 committed → Changes already in DB → No action needed
```

**Advantages:** Changes visible immediately; no need to redo committed transactions.
**Disadvantages:** More complex undo logic; dirty writes may affect other transactions.

---

#### 🟡 b. Deferred Update (Redo Logging):

**Rule:** Changes are **NOT written to the database until the transaction commits**. All changes are held in a buffer (or log) until commit. If transaction aborts, just discard the log records — no undo needed.

**Log Record Format:** `<Ti, X, new_value>` (only new value stored — no undo needed)

**Recovery Actions:**
- If Ti **committed** → **REDO** Ti by writing new_value to database.
- If Ti was **active** at crash → Simply **ignore** Ti's log records (no undo needed).

**Example:**
```
Transaction T1: Transfers ₹500 from A (₹1000) to B (₹500)

LOG RECORDS (DB NOT updated yet):
1. <T1, START>
2. <T1, A, 500>   ← new value of A (deferred write)
3. <T1, B, 1000>  ← new value of B (deferred write)
4. <T1, COMMIT>   ← Only NOW are changes written to DB
5. Database: A = 500, B = 1000

CRASH SCENARIO 1 — Crash after step 3 (before commit):
→ T1 not committed → Just ignore log records → DB unchanged → SAFE ✅

CRASH SCENARIO 2 — Crash after step 4 (after commit, before DB write):
→ T1 committed → REDO: Write A=500, B=1000 from log records 2 & 3
```

**Advantages:** Simpler — no UNDO needed. Aborted transactions leave no trace.
**Disadvantages:** Updates delayed until commit; more REDO work on recovery.

---

#### 📊 Comparison:

| Feature              | Immediate Update              | Deferred Update                 |
|----------------------|-------------------------------|---------------------------------|
| When DB is Updated   | During transaction            | Only after COMMIT               |
| Log Records Needed   | Old + New values (UNDO+REDO)  | New values only (REDO)          |
| On Abort             | UNDO using old values         | Just discard log (no UNDO)      |
| On Commit + Crash    | No REDO needed                | REDO needed                     |
| Complexity           | Higher (UNDO logic)           | Simpler (no UNDO)               |

---

#### 📖 What is Phantom?

**Definition:** A **Phantom** occurs when a transaction T1 executes a query (e.g., SELECT with a range condition) twice, and in between T2 inserts or deletes rows that match T1's query — causing T1 to see different sets of rows (new "phantom" rows appear or old ones disappear).

**Example:**
```
T1: SELECT * FROM Student WHERE Age > 20;  → Returns 5 rows
T2: INSERT INTO Student VALUES (6, 'New', 22);  COMMIT;
T1: SELECT * FROM Student WHERE Age > 20;  → Returns 6 rows (phantom!)
```

**Solution:** Use **Predicate Locking** or **Index Locking** — lock the range/predicate, not just individual rows.

---

#### 📖 Two-Phase Commit Protocol (2PC):

**Purpose:** Ensures **atomicity** of distributed transactions — either ALL sites commit or ALL sites abort.

**Two Phases:**

**Phase 1 — Voting Phase (Prepare Phase):**
1. **Coordinator** sends `PREPARE` message to all participants.
2. Each participant writes a log record and responds:
   - `VOTE-COMMIT` → ready to commit, will not abort unilaterally.
   - `VOTE-ABORT` → cannot commit, requests abort.

**Phase 2 — Decision Phase (Commit Phase):**
3. Coordinator collects all votes:
   - If ALL voted COMMIT → sends `GLOBAL-COMMIT` to all.
   - If ANY voted ABORT → sends `GLOBAL-ABORT` to all.
4. Participants execute the decision and acknowledge.

---

#### 📖 Failure Implications in 2PC:

**i) Failure of Coordinator:**

| Phase of Failure       | Implication                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| Before sending PREPARE | Participants time out, all abort independently.                             |
| After PREPARE, before decision | Participants are in uncertain state — cannot proceed or abort safely. They must block and wait for coordinator to recover. |
| After sending COMMIT   | Some participants may have committed, others not. On recovery, coordinator re-sends COMMIT. |

**Problem:** Participants are left in a **blocking state** when coordinator fails after PREPARE — this is the main weakness of 2PC.

**ii) Failure of a Participant:**

| Phase of Failure       | Implication                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| Before VOTE            | Coordinator times out, sends GLOBAL-ABORT.                                  |
| After VOTE-COMMIT sent | Participant recovers and checks log — if COMMIT received, commits; else aborts. |
| After receiving decision | Participant re-executes the decision on recovery using log.               |

---

#### 🔚 Conclusion:
Log-based recovery with immediate and deferred updates provides reliable crash recovery guarantees. Two-Phase Commit extends atomicity to distributed systems. Phantoms represent a subtle concurrency anomaly requiring predicate-level locking solutions.

---
---

## ❓ Question 3: Log-Based Recovery, Anomalies in Concurrent Execution, Deferred Update, Transaction States.

### ✅ Answer:

---

#### 📖 Log-Based Recovery:
(Covered in detail in Q2 — see above for Immediate & Deferred techniques)

**Key Points:**
- WAL (Write-Ahead Logging): Log record must be written to stable storage BEFORE the actual DB modification.
- **UNDO:** Restores old values for uncommitted transactions.
- **REDO:** Re-applies new values for committed transactions whose changes didn't reach disk.
- Log is kept on **stable storage** (non-volatile, survives crashes).

---

#### 📖 Anomalies in Concurrent Execution:

**1. Dirty Read (Temporary Update Problem):**
- T2 reads a value written by T1 which has not yet committed.
- If T1 aborts, T2 has read an invalid value.

**2. Non-Repeatable Read (Incorrect Analysis):**
- T1 reads X twice; T2 modifies X between reads → T1 gets different values.

**3. Lost Update:**
- Both T1 and T2 read X, modify it, and write it back.
- T2's write overwrites T1's write → T1's update is lost.
```
T1: Read(X)=10,        Write(X=15)
T2:          Read(X)=10,        Write(X=20)  ← T1's update lost!
Final: X=20 (T1's change from 10→15 is gone)
```

**4. Phantom Read:**
- T1 queries a set of rows; T2 inserts/deletes matching rows; T1 requeries and gets different results.

**5. Unrepeatable Read / Inconsistent Retrievals:**
- T1 reads multiple items during an aggregate calculation while T2 modifies some of them concurrently → inconsistent result.

---

#### 📖 Deferred Update Technique — Advantages & Disadvantages:

(Covered fully in Q2 — key points:)

**Advantages:**
- No UNDO needed — aborted transactions never touch the DB.
- Simpler recovery logic (only REDO).
- Cleaner transaction semantics.

**Disadvantages:**
- All changes buffered until commit → more memory usage.
- Increased REDO work during recovery.
- Transaction commit may take longer (flush all changes at once).
- Not suitable for long-running transactions (large buffer usage).

---

#### 📖 What is a Transaction?

**Definition:** A **Transaction** is a **logical unit of work** consisting of one or more database operations (reads/writes) that must be executed as a single indivisible unit — either ALL operations complete successfully, or NONE of them take effect.

**Example:**
```sql
BEGIN TRANSACTION;
    UPDATE Account SET Balance = Balance - 500 WHERE AccID = 'A';
    UPDATE Account SET Balance = Balance + 500 WHERE AccID = 'B';
COMMIT;
```
Both updates must succeed together or fail together — partial execution is not allowed.

---

#### 📖 Transaction States:

A transaction goes through the following states during its lifetime:

```
              BEGIN
                |
                ↓
           [ACTIVE] ─────────────────────────────────────┐
                |                                         |
         (Execution)                               (Error/Abort request)
                |                                         |
                ↓                                         ↓
       [PARTIALLY COMMITTED] ──────────────────→ [FAILED]
                |                (failure after last        |
         (All ops done,          statement)                 |
          before commit)                                    ↓
                |                                     [ABORTED]
         (Commit decision)                          (Rollback done,
                |                                   DB restored)
                ↓
          [COMMITTED]
         (All changes
          permanent)
```

**States Explained:**

| State                  | Description                                                             |
|------------------------|-------------------------------------------------------------------------|
| **Active**             | Transaction is currently executing operations (read/write).             |
| **Partially Committed**| Last operation has been executed; transaction is about to commit.        |
| **Committed**          | Transaction has successfully completed; changes are permanent.           |
| **Failed**             | An error occurred; normal execution cannot proceed.                     |
| **Aborted**            | Transaction has been rolled back; DB restored to state before transaction.|

---

#### 🔚 Conclusion:
Transactions are the fundamental unit of work in DBMS, providing all-or-nothing semantics. Log-based recovery handles crashes by undoing incomplete transactions and redoing committed ones. Concurrent execution anomalies (dirty read, lost update, phantom) motivate the need for concurrency control mechanisms.

---
---

## ❓ Question 4: Log-Based Recovery & Checkpoints. ACID Properties. Deadlock Handling. Schedule. Wait-Die & Wound-Wait. Conflict & Non-Conflict Serializability.

### ✅ Answer:

---

#### 📖 Log-Based Recovery & Checkpoints:

**Checkpoint:** A **Checkpoint** is a point in the log where the DBMS **flushes all modified buffers to disk** and records a special `<CHECKPOINT>` log record. It limits the amount of log that must be scanned during recovery.

**Without Checkpoint:** On crash, must scan entire log from beginning → very slow.
**With Checkpoint:** On crash, only scan log from last checkpoint → much faster.

**Checkpoint Procedure:**
1. Stop accepting new transactions temporarily.
2. Write all modified buffer pages to disk.
3. Write `<CHECKPOINT>` record to log.
4. Resume accepting transactions.

**Recovery Using Checkpoints:**
- Start from the most recent `<CHECKPOINT>` record.
- REDO all committed transactions after checkpoint.
- UNDO all active (uncommitted) transactions after checkpoint.

---

#### 📖 ACID Properties:

**ACID** stands for: **Atomicity, Consistency, Isolation, Durability**

**1. 🔵 Atomicity:**
- A transaction is treated as a single, indivisible unit.
- Either ALL operations complete, or NONE are applied.
- If any operation fails, the entire transaction is rolled back.
- Ensured by: Recovery Manager (UNDO).
- Example: Bank transfer — either both debit and credit happen, or neither.

**2. 🟡 Consistency:**
- A transaction takes the database from one **consistent state** to another consistent state.
- All integrity constraints must be satisfied before and after the transaction.
- Ensured by: Application logic + integrity constraints.
- Example: Total money in the bank must remain constant after a transfer.

**3. 🔴 Isolation:**
- Concurrent transactions execute as if they are running in isolation.
- Intermediate results of one transaction are NOT visible to others.
- Ensured by: Concurrency Control (locking, timestamping).
- Example: While a transfer is happening, another transaction doesn't see partially transferred amount.

**4. 🟢 Durability:**
- Once a transaction commits, its changes are **permanent** — even if the system crashes immediately after.
- Ensured by: Recovery Manager (REDO) using write-ahead logging.
- Example: After COMMIT, the bank transfer is permanent even if the server crashes next second.

---

#### 📖 Deadlock:

**Definition:** A **Deadlock** is a situation where two or more transactions are **waiting for each other** to release locks, and none can proceed.

```
T1 holds lock on A, waits for lock on B
T2 holds lock on B, waits for lock on A
→ Neither can proceed → DEADLOCK
```

**Deadlock Handling Methods:**

**1. Deadlock Prevention:** Ensure deadlock conditions cannot arise (Conservative 2PL, Wait-Die, Wound-Wait).
**2. Deadlock Detection:** Allow deadlocks to occur, detect using Wait-For Graph, then break by aborting a transaction.
**3. Deadlock Avoidance:** Use additional information to ensure deadlock won't occur (Banker's algorithm).

---

#### 📖 Wait-Die & Wound-Wait Protocols:

Both use **timestamps** to resolve deadlocks without detection. Older transactions have smaller timestamps.

**Wait-Die (Non-Preemptive):**
- If Ti requests a lock held by Tj:
  - If Ti is **older** (TS(Ti) < TS(Tj)) → Ti **WAITS**
  - If Ti is **younger** (TS(Ti) > TS(Tj)) → Ti **DIES** (aborts and restarts with same timestamp)
- Older transactions wait; younger ones die.
- **Non-preemptive:** A transaction is never forcibly aborted by another holding the lock.

**Wound-Wait (Preemptive):**
- If Ti requests a lock held by Tj:
  - If Ti is **older** (TS(Ti) < TS(Tj)) → Ti **WOUNDS** Tj (Tj aborts, Ti gets lock)
  - If Ti is **younger** (TS(Ti) > TS(Tj)) → Ti **WAITS**
- Older transactions preempt (wound) younger ones; younger ones wait.
- **Preemptive:** An older transaction can force a younger one to abort.

**Comparison:**

| Feature          | Wait-Die                        | Wound-Wait                      |
|------------------|---------------------------------|---------------------------------|
| Older Ti wants lock of younger Tj | Ti WAITS         | Ti WOUNDS Tj (Tj aborts)        |
| Younger Ti wants lock of older Tj | Ti DIES (aborts) | Ti WAITS                        |
| Who aborts       | Younger transaction             | Younger transaction             |
| Type             | Non-preemptive                  | Preemptive                      |
| Rollbacks        | Fewer (young aborts itself)     | More (old forces young to abort)|

---

#### 📖 What is a Schedule?

A **Schedule (History)** is a sequence of READ and WRITE operations from multiple transactions arranged in some execution order.

**Types of Schedules:**
- **Serial Schedule:** Transactions execute one after another, no interleaving.
- **Non-Serial Schedule:** Transactions are interleaved.
- **Serializable Schedule:** A non-serial schedule equivalent to some serial schedule.

---

#### 📖 Conflict Serializability:

**Conflict:** Two operations conflict if they:
1. Belong to different transactions.
2. Access the same data item.
3. At least one is a WRITE.

**Conflict Serializable:** A schedule S is conflict serializable if it can be transformed into a serial schedule by swapping **non-conflicting** operations.

**Test:** Using Precedence Graph (see Numericals section above).

**Example:**
```
S: R1(A), R2(A), W1(A), W2(A)
Conflicts: R1(A)-W2(A) [T1→T2], W1(A)-R2(A) [T1→T2], W1(A)-W2(A) [T1→T2]
Precedence Graph: T1 → T2 (no cycle) → CONFLICT SERIALIZABLE ✅
```

---

#### 📖 Non-Conflict Serializability (View Serializability):

A schedule is **View Serializable** if it is view equivalent to some serial schedule (satisfies the 3 view equivalence conditions).

**Key Difference:**
- Every conflict serializable schedule is view serializable.
- A view serializable schedule may NOT be conflict serializable.
- View serializability is a strictly larger class.

**Example of View Serializable but NOT Conflict Serializable:**
```
S: W1(X), W2(X), W1(X)
Conflicts: W1(X)-W2(X) [T1→T2], W2(X)-W1(X) [T2→T1] → CYCLE → NOT conflict serializable
But: View equivalent to serial T2,T1 (both produce final W1(X)) → View Serializable
```

---

#### 🔚 Conclusion:
ACID properties define the correctness requirements for transactions. Checkpoints improve recovery performance. Deadlock prevention protocols (Wait-Die, Wound-Wait) use timestamps to avoid circular waits. Conflict serializability (tested via precedence graphs) and view serializability define what makes a concurrent schedule correct.

---
---

## ❓ Question 5: ARIES Recovery. Integrity Constraints. Serial vs Serializable Schedule. Problems of Concurrent Execution.

### ✅ Answer:

---

#### 📖 ARIES Recovery Algorithm:

**ARIES (Algorithm for Recovery and Isolation Exploiting Semantics)** is a modern, industry-standard recovery algorithm used by most commercial DBMS (IBM DB2, Microsoft SQL Server, etc.).

**Key Features:**
- Uses **Write-Ahead Logging (WAL)**.
- Supports **steal** (can write dirty pages to disk before commit) and **no-force** (don't force writes at commit).
- Uses **Log Sequence Number (LSN)** to order log records.
- Uses a **dirty page table** and **transaction table** for efficient recovery.

---

**Three Phases of ARIES:**

**Phase 1 — Analysis Phase:**
- Scans the log **forward** from the last checkpoint.
- Determines:
  - Which transactions were active at the time of crash (need UNDO).
  - Which pages were dirty (modified but not written to disk — need REDO).
- Reconstructs the **Transaction Table** (active transactions at crash) and **Dirty Page Table** (modified pages not yet on disk).

**Phase 2 — REDO Phase:**
- Scans the log **forward** from the earliest LSN in the dirty page table.
- **REDOes all logged actions** (even those of aborted transactions!) to bring the database to the state it was at the time of crash.
- Applies all updates to bring pages up to date.

**Phase 3 — UNDO Phase:**
- Scans the log **backward**.
- **UNDOes the operations** of all transactions that were active at crash (those in the Transaction Table — uncommitted).
- Writes **compensation log records (CLRs)** for each undone operation (so UNDO is idempotent — safe to repeat if another crash occurs during recovery).

---

**ARIES Phase Summary:**
```
CRASH
  |
  ↓
ANALYSIS  → Scan forward from checkpoint
  |           → Build Transaction Table, Dirty Page Table
  ↓
REDO      → Scan forward from earliest dirty LSN
  |           → Redo ALL logged operations
  ↓
UNDO      → Scan backward
              → Undo operations of all loser (active at crash) transactions
              → Write CLRs
  |
  ↓
RECOVERY COMPLETE ✅
```

---

#### 📖 Integrity Constraints:

(Covered in detail in Relational Model section — key summary:)
- **Domain Constraint:** Values must be from the defined domain.
- **Entity Integrity:** Primary Key cannot be NULL.
- **Referential Integrity:** FK must reference existing PK or be NULL.
- **Key Constraint:** Key values must be unique.
- **Check Constraint:** Attribute values must satisfy a specified condition.

**Example:**
```sql
CREATE TABLE Student (
    RollNo INT PRIMARY KEY,                    -- Entity integrity
    Name VARCHAR(50) NOT NULL,                 -- Domain constraint
    Age INT CHECK (Age BETWEEN 15 AND 30),     -- Check constraint
    DeptID INT REFERENCES Department(DeptID)   -- Referential integrity
);
```

---

#### 📖 Serial vs Serializable Schedule:

**Serial Schedule:**
- Transactions execute **one at a time**, completely, without interleaving.
- No concurrency — one transaction finishes entirely before the next starts.
- Always correct (no concurrency issues) but very poor performance.
```
Serial Schedule: T1 completely → Then T2 completely → Then T3 completely
```

**Serializable Schedule:**
- A **non-serial (interleaved)** schedule that produces the **same result** as some serial schedule.
- Allows concurrency while maintaining correctness.
- The goal of concurrency control is to ensure all schedules are serializable.

```
Serializable (non-serial): R1(A), R2(B), W1(A), W2(B) ← interleaved but equivalent to T1→T2 or T2→T1
```

**Key Difference:**
- Serial: No interleaving (safe but slow).
- Serializable: Interleaved but equivalent to a serial schedule (safe AND concurrent).

---

#### 📖 Problems of Concurrent Execution:

1. **Lost Update** — T2 overwrites T1's update.
2. **Dirty Read** — T2 reads T1's uncommitted changes.
3. **Non-Repeatable Read** — T1 reads same item twice, gets different values.
4. **Phantom Read** — T1 re-executes a query and gets different rows.
5. **Inconsistent Analysis** — T1 reads a set of values while T2 modifies some of them.

---
---

## ❓ Question 6: Nested Transactions, Checkpoint Mechanism, Strict 2PL vs Conservative 2PL.

### ✅ Answer:

---

#### 📖 Why Transactions Cannot Be Nested:

**Standard DBMS transactions cannot be nested** because of the following reasons:

**1. Atomicity Conflict:**
- If a nested transaction aborts, should only the nested part roll back or the entire outer transaction?
- Standard transaction model doesn't define partial rollback semantics.

**2. Commit Semantics Issue:**
- When should the nested transaction's changes become permanent? At the nested commit? Or only when the outer commits?
- If nested commits but outer aborts → nested changes must be undone → violates atomicity of nested commit.

**3. Lock Management:**
- A nested transaction that commits would release locks. But the outer transaction still needs those data items.
- This would break isolation of the outer transaction.

**4. Recovery Complexity:**
- Log-based recovery would need to handle multiple levels of commit/abort — greatly complicating the recovery algorithm.

**Note:** Some advanced systems (like Savepoints) simulate partial rollback, but these are NOT true nested transactions.

```sql
-- SQL Savepoint (simulates nesting, but NOT true nested transactions):
SAVEPOINT sp1;
UPDATE Student SET Marks = 90 WHERE RollNo = 1;
ROLLBACK TO SAVEPOINT sp1;  -- Only undoes changes since sp1
COMMIT;
```

---

#### 📖 Checkpoint Mechanism:

**Purpose:** Reduce recovery time by periodically recording a consistent database state to stable storage.

**How Often Should Checkpoints Be Done?**

This involves a trade-off:
- **Too Frequent Checkpoints:**
  - More overhead during normal operation (frequent flushes to disk, pausing new transactions).
  - Reduces throughput.
  
- **Too Infrequent Checkpoints:**
  - On crash, need to scan a very long log → slower recovery.
  - More REDO work needed.

**Practical Rule:**
- Checkpoint every **few minutes** or after every **N log records** (e.g., every 1000-10000 log records).
- Modern DBMS use **fuzzy checkpoints** — checkpoint happens in background without stopping transactions, so overhead is minimal.

---

#### 📖 Strict 2PL vs Conservative (Static) 2PL:

| Feature                | Strict 2PL                              | Conservative (Static) 2PL                 |
|------------------------|-----------------------------------------|-------------------------------------------|
| When locks acquired    | During execution (on demand)            | ALL locks acquired BEFORE transaction starts |
| When locks released    | Exclusive locks held until commit/abort | Can release anytime (after lock point)    |
| Deadlock Possible      | YES                                     | NO (all locks pre-acquired, no circular wait) |
| Cascading Rollback     | Prevented (dirty reads prevented)       | Prevented                                 |
| Concurrency            | Good                                    | Lower (pre-locking may hold locks longer) |
| Practical Use          | Most DBMS use this                      | Rare — reduces concurrency too much       |
| Starvation             | Possible                                | Possible (waiting to get all locks at once)|

---

#### 🔚 Conclusion:
Nesting of transactions is theoretically and practically problematic due to atomicity, lock management, and recovery issues. Checkpoints are a critical performance tool for recovery — their frequency must be balanced. Strict 2PL is the most practical variant offering deadlock recovery and cascading rollback prevention.

---
---

## ❓ Question 7: Cascadeless Schedule. Deadlock — Conditions, Avoidance. Timestamp Protocol. Concurrency.

### ✅ Answer:

---

#### 📖 What is a Cascadeless Schedule?

**Definition:** A schedule is **Cascadeless** (also called **Avoiding Cascading Rollback Schedule**) if for every pair of transactions Ti and Tj, whenever Tj reads a data item previously written by Ti, Ti must have committed before Tj reads that item.

**Why Cascadeless is Desired:**
- Without it, if T1 writes X, T2 reads X, and T1 aborts → T2 must also abort (cascading rollback).
- Cascading rollbacks can propagate to many transactions, causing massive undo work.
- Cascadeless schedules guarantee that if a transaction commits, it never needs to be undone due to other transactions.
- **Strict 2PL** ensures cascadeless schedules.

**Example:**
```
NON-Cascadeless:
T1: W(X)
T2:       R(X)  ← T2 reads before T1 commits
T1:            ABORT → T2 must also ABORT (cascade!)

Cascadeless:
T1: W(X), COMMIT
T2:              R(X) ← T2 reads AFTER T1 commits → Safe, no cascade ✅
```

---

#### 📖 What is Deadlock?

**Definition:** A deadlock is a situation where a set of transactions are each waiting for resources (locks) held by others in the set — creating a circular wait with no possibility of progress.

**Example:**
```
T1 holds lock on A, waiting for lock on B
T2 holds lock on B, waiting for lock on A
→ Circular wait → DEADLOCK
```

---

#### 📖 Necessary & Sufficient Conditions for Deadlock (Coffman's Conditions):

All FOUR conditions must hold simultaneously for a deadlock to occur:

**1. Mutual Exclusion:**
- At least one resource must be held in a non-sharable mode.
- Example: Exclusive lock on a data item — only one transaction can hold it.

**2. Hold and Wait:**
- A transaction holding at least one resource is waiting to acquire additional resources held by other transactions.
- Example: T1 holds lock on A and is waiting for lock on B.

**3. No Preemption:**
- Resources (locks) cannot be forcibly taken away from a transaction; they must be released voluntarily.
- Example: T2's lock on B cannot be forcibly given to T1.

**4. Circular Wait:**
- A set of transactions {T1, T2, ..., Tn} exist such that T1 waits for T2, T2 waits for T3, ..., Tn waits for T1.
- Example: T1 → T2 → T1 (cycle).

---

#### 📖 Deadlock Avoidance Measures:

**Method 1: Wait-For Graph (Detection):**
- Maintain a graph where Ti → Tj means Ti is waiting for a lock held by Tj.
- If the graph has a cycle → deadlock detected → abort one transaction (victim selection).
- **Victim Selection Criteria:** Choose transaction that has done least work, or has lowest priority, or will cause minimum rollback.

**Method 2: Wait-Die & Wound-Wait (Prevention using Timestamps):**
(Covered in Q4 above)

**Method 3: Timeout:**
- If a transaction waits longer than a threshold time, abort it.
- Simple but may abort transactions unnecessarily.

**Method 4: Conservative 2PL (Pre-claiming):**
- Acquire all locks before starting.
- If any lock is unavailable, release all held locks and wait.
- Eliminates Hold-and-Wait condition → prevents deadlock.

---

#### 📖 Timestamp Protocol for Concurrency Control:

**Definition:** In the **Timestamp Protocol**, each transaction Ti is assigned a unique **timestamp TS(Ti)** when it begins. The protocol ensures that conflicting operations are executed in timestamp order.

**Rules:**

For a transaction Ti trying to perform Read(X) or Write(X):

**Read(X) by Ti:**
- If TS(Ti) < W-timestamp(X) → Ti is reading a value already overwritten by a newer transaction → **REJECT, ROLLBACK Ti**.
- Else → Allow read, update R-timestamp(X) = max(R-timestamp(X), TS(Ti)).

**Write(X) by Ti:**
- If TS(Ti) < R-timestamp(X) → Ti is trying to write a value already read by a newer transaction → **REJECT, ROLLBACK Ti**.
- If TS(Ti) < W-timestamp(X) → Ti is trying to overwrite a value written by a newer transaction → **REJECT, ROLLBACK Ti** (Thomas Write Rule: can just ignore this write).
- Else → Allow write, update W-timestamp(X) = TS(Ti).

**Key Properties:**
- No deadlocks (no waiting — transactions either proceed or are rolled back).
- Ensures timestamp-order serializability.
- Aborted transactions restart with a NEW (larger) timestamp.

---

#### 📖 What is Concurrency Control?

**Definition:** **Concurrency Control** is the mechanism used by DBMS to ensure that **concurrent execution of transactions** results in a database state equivalent to some **serial execution** — maintaining correctness while maximizing performance.

**Goals:**
- Ensure **serializability** of concurrent transactions.
- Prevent concurrency anomalies (dirty read, lost update, etc.).
- Maximize **throughput** and **resource utilization**.

**Methods:**
- **Locking Protocols:** 2PL, Strict 2PL, Rigorous 2PL.
- **Timestamp Protocols:** Basic Timestamp Ordering, Thomas Write Rule.
- **Validation Protocols:** Optimistic Concurrency Control.
- **Multi-Version Protocols:** MVCC, MV-2PL.

---

#### 🔚 Conclusion:
Cascadeless schedules prevent the domino effect of cascading rollbacks. Deadlock requires all four Coffman conditions and can be handled via prevention (Wait-Die/Wound-Wait, Conservative 2PL) or detection (Wait-For Graph). The Timestamp Protocol provides a deadlock-free concurrency control alternative to locking.

---
---

## ❓ Question 8: Optimistic Concurrency Control & Its Phases.

### ✅ Answer:

---

#### 📖 What is Optimistic Concurrency Control (OCC)?

**Definition:** **Optimistic Concurrency Control** (also called **Validation-Based Protocol** or **Certification-Based Protocol**) is a concurrency control technique based on the assumption that **conflicts between transactions are rare**. Rather than using locks or timestamps to prevent conflicts, OCC allows transactions to proceed freely and checks for conflicts only at the time of commit.

**Philosophy:**
- **Pessimistic CC (Locking/Timestamps):** "Conflicts will happen — prevent them proactively."
- **Optimistic CC:** "Conflicts are rare — let transactions run freely, validate at the end."

**Why Use OCC?**
- In applications with mostly read operations and infrequent writes, locking wastes time.
- OCC eliminates blocking and deadlocks entirely during execution.
- Better performance in low-conflict environments (e.g., reporting systems, analytics).

---

#### 📖 Three Phases of Optimistic Concurrency Control:

---

**Phase 1 — Read Phase:**

- The transaction **executes completely** — it reads data items from the database and performs all computations.
- **All WRITE operations are performed on local copies (private workspace)** — NOT on the actual database.
- The transaction maintains:
  - **Read Set (RS(Ti)):** All data items read by Ti.
  - **Write Set (WS(Ti)):** All data items written by Ti (in private copy).
- No locks acquired; no interference with other transactions.
- This phase can be long — the transaction does all its work here.

---

**Phase 2 — Validation Phase:**

- When the transaction is ready to commit, it enters the **critical validation phase**.
- The system **checks whether the transaction's execution was serializable** — i.e., it didn't create any conflicts with other concurrently committed transactions.
- Each transaction Ti is assigned a **timestamp** at the START of its validation phase.
- **Validation Test:** For each previously validated transaction Tj (with TS(Tj) < TS(Ti)), one of the following must hold:
  1. Tj completes its write phase before Ti starts its read phase.
  2. WS(Tj) ∩ RS(Ti) = ∅ and Tj completes its write phase before Ti starts its write phase.
  3. WS(Tj) ∩ RS(Ti) = ∅ and WS(Tj) ∩ WS(Ti) = ∅ and Tj completes its read phase before Ti completes its read phase.

- If validation passes → Ti proceeds to Write Phase.
- If validation fails → Ti is **aborted and restarted** from the Read Phase.

---

**Phase 3 — Write Phase:**

- If validation is successful, the transaction's **local changes are written to the actual database**.
- The private workspace values are copied to the real database.
- The transaction **commits**.
- If validation failed, there is no write phase — transaction restarts.

---

#### 📊 OCC Phase Diagram:

```
Start
  |
  ↓
┌─────────────────────┐
│    READ PHASE        │  → Read from DB + compute
│  (No locks needed)  │  → Write to local/private copy
│  RS(Ti), WS(Ti)     │  ← Can be long
└─────────────────────┘
          |
          ↓
┌─────────────────────┐
│   VALIDATION PHASE  │  → Check conflicts with
│   (Critical section)│    committed transactions
│   Assign TS(Ti)     │
└─────────────────────┘
       /         \
   PASS           FAIL
    |               |
    ↓               ↓
┌──────────┐   ABORT & RESTART
│  WRITE   │   (back to Read Phase)
│  PHASE   │  → Write private copies to DB
│          │  → COMMIT
└──────────┘
```

---

#### 📖 Advantages of OCC:

- **No deadlocks** — no locking at all during execution.
- **No blocking** — transactions don't wait for each other.
- Better performance in **read-heavy, low-conflict** workloads.
- Transactions always make progress during read phase.
- Suitable for distributed and in-memory databases.

---

#### 📖 Disadvantages of OCC:

- **Starvation possible:** A transaction may keep being aborted if conflicts repeatedly occur.
- **Poor performance in high-conflict** workloads — frequent rollbacks negate the benefit.
- Validation phase is a serial critical section — can become a bottleneck.
- All work done in read phase is wasted if validation fails (REDO from scratch).
- Not suitable for long transactions in high-write environments.

---

#### 📊 OCC vs Locking vs Timestamp:

| Feature              | OCC (Optimistic)                | Locking (Pessimistic)          | Timestamp Protocol         |
|----------------------|---------------------------------|--------------------------------|---------------------------|
| Conflict Assumption  | Rare                            | Common                         | Common                    |
| Blocking             | No                              | Yes (waiting for locks)        | No (abort instead)        |
| Deadlock             | No                              | Yes                            | No                        |
| Wasted Work          | On conflict (redo read phase)   | On deadlock (rollback)         | On conflict (rollback)    |
| Best For             | Low-conflict, read-heavy        | High-conflict, write-heavy     | Mixed workloads           |
| Overhead             | Validation phase overhead       | Lock management overhead       | Timestamp management      |
| Starvation           | Possible                        | Possible                       | Possible                  |

---

#### 🔚 Conclusion:
Optimistic Concurrency Control is an excellent alternative to locking in low-conflict environments. Its three phases — Read, Validation, and Write — allow transactions to execute freely and only check for correctness at commit time. While it eliminates deadlocks and blocking, it is less suitable for high-conflict environments where frequent rollbacks would negate its performance benefits.

---
---

# 📝 QUICK REVISION CARD — Transaction Processing

| Topic              | Key Point                                                              |
|--------------------|------------------------------------------------------------------------|
| 2PL                | Growing phase: acquire locks. Shrinking phase: release locks.          |
| Strict 2PL         | Exclusive locks held until commit. Prevents cascading rollback.        |
| Timestamp Protocol | No deadlocks. Abort if timestamp order violated.                       |
| Wait-Die           | Older waits, younger dies.                                             |
| Wound-Wait         | Older wounds (preempts) younger, younger waits.                        |
| ACID               | Atomicity, Consistency, Isolation, Durability.                         |
| Deferred Update    | DB updated only after commit. Need REDO on recovery. No UNDO needed.   |
| Immediate Update   | DB updated during execution. Need UNDO (and possibly REDO) on recovery.|
| Checkpoint         | Flush buffers to disk. Reduces recovery scan time.                     |
| ARIES              | Analysis → REDO → UNDO. Uses LSN, WAL, dirty page table.              |
| Serializability    | Use precedence graph. No cycle = conflict serializable.                |
| OCC Phases         | Read Phase → Validation Phase → Write Phase.                           |
| Deadlock Conditions| Mutual exclusion, Hold & Wait, No preemption, Circular wait.           |
| Cascadeless        | T2 reads Ti's write only after Ti commits. No cascading rollback.      |
| 2PC                | Voting Phase + Decision Phase. Coordinator + Participants.             |
| Phantom            | New rows appear in repeated range query due to concurrent insert.      |

---

*📑 Documented by Rishav | Wishing you all the very best in your upcoming examinations! 🌟🎓*
*You've covered everything — now practice the serializability numericals! 💪*