# Servers Get Stressed Too
### Subtle Ways We Overload Our Systems Without Realizing It  
**By Abdulmateen Tairu (@taycode) – PyCon NG 2025**

---

## Why This Talk?

- I’ve spent years building and scaling backend systems.
- I noticed something painful: we stress our servers more than our users do.
- Sometimes it’s not traffic that kills performance — it’s code.
- This talk is about those subtle anti-patterns that creep into our systems and quietly cause pain.

---

## What Happens When Servers Are Stressed?

- High p95/p99 latency
- Increased timeouts and failures
- Spikes in CPU/memory usage
- Infrastructure cost creep
- Cascading failures across services

> “It’s not just scale that hurts systems — it’s poor design under scale.”

---

## 6 Subtle Anti-Patterns That Stress Your Servers

We’ll go through each with examples and fixes:
1. Long request cycles
2. OFFSET pagination
3. N+1 queries
4. Hot-path aggregations
5. Fat payloads
6. Multiple row locking

---

## 1. Long Request Cycles

**Problem:** Doing heavy work like image/PDF generation, DB writes, or S3 uploads inside HTTP requests.

**Why it’s bad:**  
- Slows response times  
- Ties up workers  
- Increases timeouts under load

**Fix:**  
- Offload to background workers  
- Return 202 + job ID

---

## 2. OFFSET Pagination

**Problem:** Using OFFSET for deep pagination:
```sql
SELECT * FROM orders ORDER BY created_at DESC LIMIT 50 OFFSET 10000;
```

**Why it’s bad:**  
- Deep pages = slow DB scans  
- Wasted CPU & I/O  
- Poor user experience

**Fix:**  
- Use keyset/cursor-based pagination  
- Index smartly

---

## 3. N+1 Queries

**Problem:** Fetching related data in loops:
```python
for post in posts:
    print(post.author.name)
```

**Why it’s bad:**  
- 1 query becomes 101  
- Latency grows with data size

**Fix:**  
- Use select_related / include / batch queries

---

## 4. Hot-Path Aggregations

**Problem:** Real-time COUNTs and SUMs on dashboards:
```sql
SELECT COUNT(*) FROM users;
```

**Why it’s bad:**  
- Expensive queries on every page load  
- Locks tables  
- Slows everything

**Fix:**  
- Cache results  
- Use materialized views  
- Precompute in background

---

## 5. Fat Payloads

**Problem:** Sending entire objects, base64 images, unused fields in JSON

**Why it’s bad:**  
- Slower responses  
- More memory + bandwidth  
- Harder to parse and cache

**Fix:**  
- Return only required fields  
- Compress responses  
- Use object storage for blobs

---

## 6. Multiple Row Locking

**Problem:** Updating or locking many rows at once:
```sql
SELECT * FROM jobs WHERE status = 'pending' FOR UPDATE;
```

**Why it’s bad:**  
- Causes contention  
- Leads to deadlocks and slowdowns

**Fix:**  
- Lock fewer rows  
- Use SKIP LOCKED  
- Break into smaller batches

---

## Server Health Checklist ✅

- [ ] Offload long work to background queues  
- [ ] Avoid OFFSET – use keyset pagination  
- [ ] Kill N+1s with eager loading  
- [ ] Cache or precompute heavy stats  
- [ ] Trim API payloads  
- [ ] Avoid locking many rows at once

---

## Final Thoughts

- Performance is systemic, not just code-deep.
- Your server is doing its best — help it breathe.
- Empathy isn’t just for users… it’s for infrastructure too.

---

## Q&A

Let’s talk backend pain.

👤 @taycode  
🧠 GitHub | LinkedIn | Twitter/X
