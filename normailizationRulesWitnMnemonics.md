# Normalization Rules with Mnemonics

## Shortest possible rules + easy mnemonics, no examples.

(All rules sourced from standard definitions of 1NF, 2NF, 3NF, BCNF as described in GeeksforGeeks and similar references. [GeeksForGeeks](https://www.geeksforgeeks.org/dbms/normal-forms-in-dbms/))

---

## 1NF — First Normal Form

**Rule:**

- All values are **atomic** (no multi‑value fields).
- No repeating groups.
- Rows are unique.

**Mnemonic:** **A** for **A**tomic → **1NF = “Atomic First.”**

---

### **2NF — Second Normal Form**

**Rule:**

- Must already be in 1NF.
- **No partial dependency**: every non‑key attribute depends on the **whole** composite key (if composite key exists).

**Mnemonic:** **W** for **W**hole → **2NF = “Whole Key, Nothing Less.”**

---

### **3NF — Third Normal Form**

**Rule:**

- Must already be in 2NF.
- **No transitive dependency**: non‑key attributes must not depend on other non‑key attributes.

**Mnemonic:** **N** for **N**on‑key → **3NF = “Nothing Depends on Non‑keys.”**

---

### **BCNF — Boyce–Codd Normal Form**

**Rule:**

- For every functional dependency X → Y, **X must be a superkey**.

**Mnemonic:** **S** for **S**uperkey → **BCNF = “Superkey or Bust.”**

---

### One‑line summary to memorize all four

- **1NF:** Atomic
- **2NF:** Whole key
- **3NF:** No non‑key chains
- **BCNF:** Superkey only

If you want, I can also give you a compressed “flashcard” version you can memorize in under 10 seconds.
