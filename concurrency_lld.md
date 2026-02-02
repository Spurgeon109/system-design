

# Concurrency Fundamentals for System Design

Concurrency issues in software systems typically fall into **three major categories**:

1. **Correctness**
2. **Coordination**
3. **Resource Scarcity**

Understanding these patterns is essential for designing reliable, scalable, and efficient multi-threaded systems.

---

## 📚 Table of Contents

- [1. Correctness Issues](#1-correctness-issues)
  - [Check-Then-Act Pattern](#check-then-act-pattern)
  - [Read–Modify–Write Pattern](#readmodifywrite-pattern)
- [2. Coordination Problems](#2-coordination-problems)
  - [Busy Waiting (What to Avoid)](#-naive-approach--busy-waiting)
  - [Blocking Mechanisms (Best Practice)](#-proper-approach--blocking-mechanisms)
- [3. Resource Scarcity](#3-resource-scarcity)
  - [Semaphores](#-solution--semaphores)
  - [Common Pitfalls](#-common-pitfall)
- [Summary Table](#-summary-table)
- [Next Topics to Explore](#-next-topics-to-explore)

---

## 1. Correctness Issues

Correctness problems occur when **multiple threads access and modify shared state**, leading to inconsistent or corrupted data.

### 🔹 Check-Then-Act Pattern

This issue arises when a thread:

1. Checks a condition  
2. Performs an action based on that condition  

**Problem:**  
Between the **check** and the **action**, another thread may change the shared state.

**Example Scenario**
- Thread A checks if a resource is available.
- Before Thread A uses it, Thread B also checks and uses the same resource.
- This results in **duplicate usage or corrupted state**.

**Solution:**  
Use **mutual exclusion mechanisms** so the operation becomes atomic:
- Locks (`synchronized`, `ReentrantLock`, `mutex`)
- Critical sections

---

### 🔹 Read–Modify–Write Pattern

This occurs when a thread:

1. Reads a value  
2. Modifies it  
3. Writes it back  

**Problem:**  
If two threads execute this sequence concurrently, one update may overwrite the other.

**Example**
nitial value = 5
Thread A reads 5
Thread B reads 5
Thread A writes 6
Thread B writes 6 ❌ Lost update


**Solution:**  
Use **atomic operations** or synchronization:
- Atomic variables (`AtomicInteger`, `compareAndSet`)
- Locks
- Synchronized blocks

---

## 2. Coordination Problems

Coordination issues arise when **threads must work together** and need a way to know when work is available.

### ❌ Naive Approach — Busy Waiting

A worker thread continuously checks for tasks:

```
while(true) {
  checkForWork();
}
```


**Issues:**
- Wastes CPU cycles  
- Causes unnecessary system load  
- Scales poorly

---

### ✅ Proper Approach — Blocking Mechanisms

Use **blocking data structures** that automatically put threads to sleep when no work is available.

**Best Practice: Use a Blocking Queue**

**Benefits:**
- Threads sleep automatically when the queue is empty  
- Threads wake up when new tasks arrive  
- Prevents CPU wastage  
- Naturally provides **backpressure** when the queue is bounded  

**Examples**
- `BlockingQueue` (Java)  
- `Channel` (Go)  
- `queue.Queue` (Python with blocking)

---

## 3. Resource Scarcity

Scarcity occurs when **limited resources** must be shared among many threads.

**Examples of scarce resources:**
- Database connections  
- Thread pools  
- File handles  
- Network sockets  

If too many threads compete for limited resources, the system can become unstable or crash.

---

### 🔹 Solution — Semaphores

A **semaphore** limits how many threads can access a resource simultaneously.

**How it works:**
- Threads acquire a permit before accessing the resource  
- When finished, they release the permit  


---

### ⚠️ Common Pitfall

If a thread acquires a permit but fails to release it (for example, due to an exception), other threads may be permanently blocked.

This can lead to:
- Resource starvation  
- Deadlocks  
- System slowdown

**Best Practice: Always release permits in a finally block**

```java
semaphore.acquire();
try {
    // use resource
} finally {
    semaphore.release();
}

