
# Recovery Mechanism in DBMS (How DBMS Supports Atomicity & Durability)



📌 What is Recovery Mechanism?

The Recovery Mechanism is a component of DBMS that:

Restores the database to a correct state after failure

Handles system crashes, power failures, and transaction failures

Ensures ACID properties, mainly:

Atomicity

Durability

🎯 Why Recovery is Needed?

Failures can occur:

During transaction execution

After commit

Before commit

Without recovery:

Partial updates remain

Data becomes inconsistent

🔑 Role of Recovery Mechanism
| Property       | How Recovery Helps                          |
| -------------- | ------------------------------------------- |
| **Atomicity**  | Undo partial effects of failed transactions |
| **Durability** | Redo committed transactions after crash     |


Two major implementation approaches are:
1. Shadow Copy Scheme
2. Log-Based Recovery Scheme


1️⃣ Shadow Copy Scheme
📌 Core Idea

Instead of modifying the original database directly:

DBMS works on a shadow copy

Original database remains unchanged

On commit → shadow becomes main database

👉 This guarantees Atomicity & Durability without logs

🧠 How It Works (Step-by-Step)

Maintain two pointers:

db_ptr → original database

shadow_ptr → shadow database

When transaction starts:

Create shadow copy

All updates go to shadow copy

If transaction fails:

Discard shadow copy

Original database remains intact

If transaction commits:

Atomically change db_ptr = shadow_ptr

📊 Diagram (Conceptual)
Before Commit:
db_ptr  ---> Original DB
shadow_ptr ---> Modified DB

After Commit:
db_ptr ---> Modified DB

🧩 Pseudocode (Shadow Copy)
begin_transaction():
    shadow_db = copy(original_db)

write(X, value):
    shadow_db[X] = value

commit():
    original_db = shadow_db
    flush_to_disk(original_db)

abort():
    discard(shadow_db)

✅ Guarantees
| Property   | How                                                                                                               |
| ---------- | ------------------------------------------------------------------------------------------------------------------|
| Atomicity  | Original DB untouched until commit. Hence, either all updates are reflected or none.                              |
| Durability | Pointer switch is atomic. If System get fails after db-pointer updated. After restarts , it will read new DB copy |

❌ Limitations

High memory usage

Not suitable for large databases

No concurrency support

📌 Used only in small / simple systems