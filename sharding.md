# Database Sharding — System Design Fundamentals

**Sharding** is the process of splitting a large database into smaller, independent pieces called **shards**, which are distributed across multiple machines.

Each shard:
- Is a standalone database
- Contains a subset of the data
- Helps overcome the limits of **vertical scaling**

Sharding improves scalability, but it introduces new design and operational challenges.

---

## ⚖️ Trade-offs of Sharding

While sharding improves scalability, it adds complexity:

1. **Operational complexity** — More servers, monitoring, backups, and recovery plans  
2. **Shard routing** — Determining which shard a request should go to  
3. **Failure handling** — Deciding what happens when a shard becomes unavailable  

---

## 🧠 How Do We Decide a Sharding Strategy?

Before sharding, ask two key questions:

### 1️⃣ What should we shard by?
This refers to the **shard key** — the column used to partition data.

Examples:
- `user_id`
- `tenant_id`
- `region`
- `order_id`

### 2️⃣ How should we distribute the data?
This determines how data groups are assigned to shards.

---

## ✅ Properties of a Good Shard Key

A good shard key should have:

- **High cardinality** → Many unique values  
- **Even distribution** → Prevents some shards from being overloaded  
- **Query alignment** → Most queries should hit **only one shard**

---

## 🗂️ Sharding Strategies

### 1️⃣ Range-Based Sharding

Data is divided into **continuous ranges**.

**Example**
Shard 1 → User IDs 1–100
Shard 2 → User IDs 101–200


**Pros**
- Simple to implement  
- Easy to reason about  

**Cons**
- Risk of **uneven load**
- New data may always go to the last shard (hot shard problem)

---

### 2️⃣ Hash-Based Sharding

The shard key is hashed, and the result determines the shard.

shard_id = hash(user_id) % number_of_shards


**Pros**
- More even data distribution  
- Reduces hotspot risk  

**Cons**
- Adding a new shard causes **massive data reshuffling**  
- Hard to rebalance without downtime  

**Improvement:**  
Use **Consistent Hashing** to reduce data movement when nodes are added or removed.

---

### 3️⃣ Directory-Based Sharding

A **lookup service** (directory) maps each key to a specific shard.

user_id → lookup table → shard_id


**Pros**
- Flexible data placement  
- Easy to move specific users or tenants between shards  

**Cons**
- Extra network call adds **latency**  
- Directory can become a **single point of failure**

---

## 🚧 Common Sharding Challenges

### 1️⃣ Hotspots

A **hotspot** occurs when a single shard receives disproportionate traffic.

**Example:**  
A viral post causes millions of users to query the same record, overloading one shard.

**Solutions:**
- **Compound shard keys** (e.g., `post_id + hash(user_id)`)
- **Read replicas** for popular data
- **Dedicated shard** for high-demand entities
- Caching layers (Redis, CDN)

---

### 2️⃣ Cross-Shard Operations

Some queries require data from multiple shards.

**Problems**
- Increased latency  
- Complex joins  
- Higher failure probability  

**Mitigations**
- **Denormalization**
- **Caching**
- Pre-aggregated data
- Application-level joins instead of DB joins

---

### 3️⃣ Consistency Across Shards

Transactions that span multiple shards are difficult.

**Problems**
- Partial failures  
- Inconsistent data  
- Hard rollbacks  

**Solutions**
- **Two-Phase Commit (2PC)** — Strong consistency, but slower  
- **Saga Pattern** — Eventual consistency using compensating actions

---

## 📌 Summary Table

| Aspect | Description | Challenge | Solution |
|-------|-------------|-----------|----------|
| Shard Key | Column used to partition data | Poor choice leads to imbalance | High-cardinality, query-aligned key |
| Range Sharding | Splits data into value ranges | Hot shards | Rebalancing, splitting ranges |
| Hash Sharding | Hash determines shard | Hard to add shards | Consistent hashing |
| Directory Sharding | Lookup table maps keys to shards | Extra latency, SPOF | Replicated directory service |
| Hotspots | Uneven traffic to one shard | Overloaded node | Compound keys, caching, replicas |
| Cross-Shard Queries | Queries spanning shards | High latency | Denormalization, caching |
| Distributed Transactions | Updates across shards | Inconsistency | 2PC, Saga |

---

## 🚀 Related Topics to Explore

- Replication vs Sharding  
- Leader–Follower replication  
- Consistent Hashing in depth  
- Data rebalancing strategies  
- Multi-region database design  
- CAP theorem in distributed databases  

---

**Tip:** Sharding is a core topic in **System Design Interviews**, especially for large-scale systems like social networks, e-commerce platforms, and SaaS products.
