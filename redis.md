# Redis Fundamentals for System Design

Redis is a high-performance, in-memory data store widely used in distributed systems for caching, rate limiting, messaging, and more.

---

## ⚡ Key Characteristics of Redis

1. **Single-threaded command execution**  
   Redis processes commands using a single main thread, which avoids locking overhead and ensures operations are atomic at the command level.

2. **In-memory storage**  
   Data is primarily stored in memory, making Redis extremely fast compared to disk-based databases.  
   (It can optionally persist data to disk using RDB or AOF.)

3. **Efficient access vs N+1 query problem**  
   Traditional SQL databases can suffer from the **N+1 query problem**, where multiple dependent queries degrade performance.  
   Redis allows fast direct key-based access, often reducing the need for repeated database queries.

---

## 🧰 Common Redis Commands

| Command | Purpose |
|--------|---------|
| `SET key value` | Store a value |
| `GET key` | Retrieve a value |
| `INCR key` | Increment a numeric value |
| `EXPIRE key seconds` | Set a time-to-live (TTL) |
| `XADD` | Add an entry to a Redis Stream |

---

## 🚀 Common Usage Patterns

### 1️⃣ Caching Layer

Redis is often used as a **cache** in front of a primary database.

**Flow**
1. Application checks Redis for data  
2. If present → return cached value  
3. If missing → fetch from DB and store in Redis  

**Benefits**
- Reduces database load  
- Improves response time  
- Handles read-heavy traffic efficiently  

**Challenges**

🔹 **Hot Key Problem**  
If one key is requested extremely often (e.g., a viral post), it can overload a single Redis node.

**Mitigations**
- Replication
- Local in-memory caching in application
- Sharding Redis

🔹 **Expiration Policies**
Choosing the right eviction and expiration strategy is important.

Common policies:
- **TTL (Time To Live)** — Key expires after a fixed time  
- **LRU (Least Recently Used)** — Evicts least recently accessed keys when memory is full  

---

### 2️⃣ Rate Limiting

Redis is widely used to limit how frequently users can perform certain actions.

**Example:** Limit login attempts or API calls.

**How it works**
- Maintain a key per user or IP  
- Increment the counter for each request using `INCR`  
- Set an expiry using `EXPIRE` to define the time window  


If the count exceeds a threshold, reject further requests.

---

## 🌊 Redis Streams

Redis Streams are a data structure designed for **event streaming and messaging**.

They behave like an **append-only log** of ordered entries.

### Key Concepts

- **Stream** → Ordered list of records  
- **Producer** → Adds entries using `XADD`  
- **Consumer Group** → Group of workers processing messages  
- **Consumers** → Workers that read and process messages  

Each consumer keeps track of which messages it has processed using stream IDs.

**Workflow**
1. Producers add events to a stream  
2. Consumer groups read from the stream  
3. Messages are processed and acknowledged  

**Use Cases**
- Task queues  
- Event-driven architectures  
- Log processing pipelines  

---

## 📌 Summary

| Feature | What It Enables |
|--------|-----------------|
| In-memory storage | Ultra-fast reads/writes |
| Single-threaded model | Atomic operations without locks |
| TTL & eviction policies | Automatic cache management |
| INCR + EXPIRE | Rate limiting |
| Streams | Lightweight message queues |

---

**Tip:** Redis is commonly used alongside databases, not as a replacement for all storage. It shines in **speed-critical** and **transient data** use cases.
