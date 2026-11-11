###########################################################################################
                                  CHAPTER-1
############################################################################################


1. What is a DBMS?
A Database Management System (DBMS) is software that allows users to create, store, retrieve, update, and manage data in a database.

7. Important DBMS terminology
- Database: Organized collection of data.
- Table: Data organized into rows and columns.
- Tuple: A row in a table.
- Attribute: A column in a table.
- Schema: Structure or design of a database.
- Instance: Actual data stored at a particular moment.
- Query: Request to retrieve or manipulate data.


3. Why do we need DBMS?
Before DBMS, data was commonly stored in ordinary files.

This created several problems:
Problem	Explanation
Data redundancy	Same data stored multiple times
Data inconsistency	Different copies contain different values
Data security	Difficult to control access
Data isolation	Data scattered across different files
Concurrent access	Multiple users can cause conflicts
Data recovery	Difficult to recover after crashes


1. What is a Transaction?
A transaction is a logical unit of work in a database that consists of one or more operations performed together as a single task.
In simple words, a transaction is a group of database operations that should either complete successfully or be undone completely.


Kaneki ❤️, in DBMS, the desirable properties of a transaction are the properties that ensure database transactions execute correctly and reliably.

These are known as ACID properties. They are extremely important for GATE CSE.
1. ACID Properties
ACID stands for:
- A – Atomicity
- C – Consistency
- I – Isolation
- D – Durability
1. Atomicity
All or Nothing
A transaction must either execute completely or not execute at all.

Example: Bank transfer

A transfers ₹1,000 to B.

Two operations:
1. Deduct ₹1,000 from A.
2. Add ₹1,000 to B.
Suppose the first operation succeeds but the second fails.

The database must undo the first operation.

This is called Atomicity.
2. Consistency
Valid State to Valid State
A transaction must take the database from one consistent state to another consistent state.

Example:
- Account A = ₹5,000
- Account B = ₹3,000
- Total = ₹8,000
After transferring ₹1,000:
- Account A = ₹4,000
- Account B = ₹4,000
- Total = ₹8,000
The database constraints and rules must remain satisfied.
3. Isolation
Independent Execution
When multiple transactions execute simultaneously, they should not interfere with each other in a way that produces incorrect results.

Example:

Two users try to book the last available seat on a train.

Without proper concurrency control, both users might successfully book the same seat.

Isolation helps prevent such problems.
4. Durability
Permanent Changes
Once a transaction is committed, its changes must remain permanent, even if the system crashes.

Example:

You successfully transfer ₹1,000.

Immediately afterward, the database server crashes.

After restarting, the transaction must still be reflected in the database.

That is Durability.


2. ACID Summary Table
Property	Meaning	Remember
Atomicity	All or nothing	Complete or rollback
Consistency	Valid state maintained	Correctness
Isolation	Transactions don't interfere incorrectly	Independence
Durability	Committed changes persist	Permanence


1. What are Transaction States?
A transaction passes through different stages during its execution. Each stage is called a Transaction State.

For example, imagine transferring ₹1,000 from Account A to Account B.

The transaction starts, executes operations, and eventually either commits successfully or fails.

There are 5 major transaction states:
1. Active
2. Partially Committed
3. Committed
4. Failed
5. Aborted


ive Transaction States
1. Active State
Transaction Running
This is the initial state of a transaction.

The transaction is currently executing its operations.

Example:
BEGIN TRANSACTION;

UPDATE accounts
SET balance = balance - 1000
WHERE account_id = 'A';


The transaction is executing, so it is in the Active state.

2. Partially Committed State
Last Statement Executed
The transaction has executed its final statement, but its changes are not yet guaranteed to be permanently recorded.

Example:
UPDATE accounts
SET balance = balance + 1000
WHERE account_id = 'B';


All SQL operations have finished, but the transaction has not yet completed its commit process.

3. Committed State
Successfully Completed
The transaction has successfully completed its commit process.

Its changes are now permanent (Durability).
COMMIT;


Example:
- Account A = ₹4,000
- Account B = ₹4,000
The transaction is successfully committed.

4. Failed State
Execution Failed
A transaction enters the Failed state when it cannot continue executing.

Possible reasons:
- System crash
- Power failure
- Disk failure
- Logical error
- Constraint violation
Example:

A transaction tries to withdraw more money than the account contains, violating a database constraint.

5. Aborted State
Rolled Back
After a transaction fails, the database rolls back its changes and restores the database to the appropriate previous state.
ROLLBACK;


After abortion, a transaction may:
- Restart from the beginning.
- Be terminated permanently.


                  TRANSACTION STATE DIAGRAM


                         ┌──────────────┐
                         │    ACTIVE    │◄────────────────┐
                         └──────┬───────┘                 │
                                │                         │
                         Last statement                   │
                            executed                      │
                                │                         │
                                ▼                         │
                    ┌──────────────────────┐              │
                    │ PARTIALLY COMMITTED  │              │
                    └──────────┬───────────┘              │
                               │                          │
                        Commit succeeds                   │
                               │                          │
                               ▼                          │
                       ┌──────────────┐                   │
                       │  COMMITTED   │                   │
                       └──────────────┘                   │
                                                          │
                                                          │
       Failure                                            │
          │                                               │
          ▼                                               │
    ┌──────────────┐                                      │
    │    FAILED    │                                      │
    └──────┬───────┘                                      │
           │                                              │
        Rollback                                          │
           │                                              │
           ▼                                              │
    ┌──────────────┐                                      │
    │   ABORTED    │──────────────────────────────────────┘
    └──────────────┘          Restart (optional)



Q: Datbase is consistent in both aborted and commited?
Yes, Kaneki! ❤️ Excellent question. The answer is YES, the database should be in a consistent state in both the Committed and Aborted states.



1. What is Concurrent Execution?
Concurrent execution means executing multiple transactions in an interleaved manner so that their operations overlap in time.

Imagine an online banking application.

At the same time:
- User A is checking their account balance.
- User B is transferring money.
- User C is checking their transaction history.
All three users are accessing the same database.

Instead of making everyone wait for the previous transaction to finish, the DBMS allows transactions to execute concurrently.
2. Why do we need Concurrent Execution?
There are three major reasons.

1. Improved Throughput



2. Better Resource Utilization

.

3. Reduced Waiting Time


4. But Is Concurrent Execution Always Safe?
No! This is where the problem begins.

Consider:

Initial balance = ₹5,000.
- T1 withdraws ₹1,000.
- T2 deposits ₹2,000.
Both transactions read the original balance before either writes.
T1: Read(A) = 5000
T2: Read(A) = 5000

T1: A = 5000 - 1000 = 4000
T2: A = 5000 + 2000 = 7000

T1: Write(A) = 4000
T2: Write(A) = 7000


Final balance = ₹7,000.

Correct balance should be ₹6,000!

This is called the Lost Update Problem.



1. Lost Update Problem
Meaning: One transaction's update gets overwritten by another transaction.

Suppose account balance = ₹5,000.
- T1 withdraws ₹1,000.
- T2 deposits ₹2,000.
Both transactions read ₹5,000 before updating it.
       T1                  T2
       |                   |
       Read(A) = 5000      |
       |                   |
       |              Read(A) = 5000
       |                   |
       A = 4000             |
       |              A = 7000
       |                   |
       Write(A = 4000)      |
       |                   |
       |              Write(A = 7000)


Final balance = ₹7,000.

Correct balance = ₹6,000.

Lost Update
 T2 overwrites T1's update.
2. Dirty Read Problem
Meaning: A transaction reads data written by another transaction that has not yet committed.

Imagine account balance = ₹5,000.
- T1 updates balance to ₹3,000.
- T2 reads ₹3,000.
- T1 encounters an error and performs ROLLBACK.
Now T2 has read a value that was never committed.
       T1                  T2
       |                   |
       Read(A) = 5000      |
       |                   |
       Write(A = 3000)     |
       |                   |
       |              Read(A) = 3000
       |                   |
       ROLLBACK             |
       |                   |
       A = 5000             |
       |                   |
       |              Uses 3000 (invalid)


Dirty Read
 T2 read uncommitted data from T1.

Remember: Dirty Read = Reading data before COMMIT.
3. Unrepeatable Read Problem
Meaning: A transaction reads the same row twice but gets different values because another transaction modified and committed that row in between.

Suppose account balance = ₹5,000.
- T1 reads balance = ₹5,000.
- T2 updates balance to ₹7,000 and commits.
- T1 reads the same row again.
       T1                  T2
       |                   |
       Read(A) = 5000      |
       |                   |
       |              Write(A = 7000)
       |                   |
       |                 COMMIT
       |                   |
       Read(A) = 7000      |


T1 got two different values for the same row!

Unrepeatable Read
 The same row produced different values on repeated reads.

Remember: Same row + different values = Unrepeatable Read.
4. Phantom Read Problem
Meaning: A transaction executes the same query twice but gets a different set of rows because another transaction inserted or deleted rows matching the query condition.

Imagine a student database.

T1 executes:
SELECT * FROM students
WHERE marks > 80;


Initially, it gets:
Student	Marks
Rahul	90
Amit	85

Now T2 inserts another student.
INSERT INTO students
VALUES (103, 'Priya', 95);

COMMIT;


T1 executes the same query again.

Now it gets:
Student	Marks
Rahul	90
Amit	85
Priya	95

A new row appeared in the result!
       T1                  T2
       |                   |
       SELECT marks > 80   |
       |                   |
       |              INSERT Priya, 95
       |                   |
       |                 COMMIT
       |                   |
       SELECT marks > 80   |
       |                   |
       Gets extra row      |


Phantom Read
 A new matching row appeared in the query result.

Remember: Same query + different set of rows = Phantom Read.

5. All Four Problems: Comparison
This table is especially important for GATE.
Problem	What changes?	Key identification
Lost Update	An update is overwritten	Lost modification
Dirty Read	Uncommitted data is read	No COMMIT yet
Unrepeatable Read	Existing row's value changes	Same row, different value
Phantom Read	Result set changes	New/deleted matching rows

6. Easy trick to remember
L — Lost Update

Someone overwrites your update.

D — Dirty Read

Someone reads your uncommitted data.

U — Unrepeatable Read

Same row, different value.

P — Phantom Read

Same query, different rows.



Q Which of the following scenarios may lead to an irrecoverable error in a database system?
(A) A transaction writes a data item after it is read by an uncommitted transaction
(B) A transaction reads a data item after it is read by an uncommitted transaction
(C) A transaction reads a data item after it is written by a committed transaction
(D) A transaction reads a data item after it is written by an uncommitted transaction
 ans (D)


 Q1. T1 updates A = 500 to A = 700 but doesn't commit. T2 reads A = 700.

Lost Update

Dirty Read

Unrepeatable Read

Phantom Read

Correct!


Q2. T1 reads salary = 50,000. T2 updates it to 60,000 and commits. T1 reads it again.

Lost Update

Dirty Read

Unrepeatable Read

Phantom Read

Correct!


Q3. T1 reads all students with marks > 80. T2 inserts a student with marks 90 and commits. T1 repeats the query and gets an additional row.

Lost Update

Dirty Read

Unrepeatable Read

Phantom Read

Correct!


Q4. T1 and T2 both read A = 100. T1 writes 120, then T2 writes 150, overwriting T1's result.

Lost Update

Dirty Read

Unrepeatable Read

Phantom Read

Correct!(2,3,4,1)


