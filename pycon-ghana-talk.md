# PyCon Ghana — Working Doc

**Length:** 30 minutes
**Speaker:** Abdulmateen Tairu (@taycode)
**Deliverable:** reveal.js deck — `pycon-ghana.html`
**Stack shown on slides:** FastAPI + Postgres + Celery/Redis

---

## Accepted abstract (public, submitted — do not contradict)

As applications grow, the hardest problems are rarely about writing business
logic—they come from handling scale, throughput, expensive queries, large
datasets, and systems that begin to slow down under pressure.

This talk explores the practical realities of building data-intensive
applications in Python, focusing on the engineering decisions that help systems
remain performant and maintainable as data volume grows.

Topics promised:
- Efficient data access patterns and avoiding expensive queries
- Understanding database bottlenecks and indexing strategies
- Handling high-throughput workloads with batching and asynchronous processing
- Memory and CPU considerations when processing large datasets
- Pagination strategies and why OFFSET pagination breaks at scale
- Caching, aggregation trade-offs, and reducing unnecessary computation
- Observability techniques for identifying performance bottlenecks in production

---

## Submitted outline (verbatim, as filed with reviewers)

1. Introduction: What Makes Applications "Data-Intensive"?   (3 min)
   - Characteristics of data-intensive systems
   - Why systems become unreliable as scale increases
   - Common production pain points
2. Processing Workloads Reliably                             (6 min)
   - Background workers and asynchronous processing
   - Decoupling expensive operations from request cycles
   - Handling spikes in traffic and workload bursts
3. Reliability Patterns That Matter                          (8 min)
   - Idempotency and safe retries
   - Exponential backoff strategies
   - Circuit breakers for unstable third-party services
   - Preventing cascading failures
4. Databases & Consistency Under Load                        (6 min)
   - Database transactions and rollback strategies
   - Avoiding expensive queries and bottlenecks
   - Common scaling mistakes in backend systems
5. Observability & Debugging Production Systems              (4 min)
   - Logs, metrics, and tracing
   - Identifying bottlenecks before systems fail
   - Building visibility into system behavior
6. Closing Thoughts & Q&A                                    (3 min)

---

## Story slots — needed from Tay

Real incidents beat synthetic examples. For each: the moment, the number, the
fix. Raw bullets are fine. Flag anything confidential so it stays off the slides.

| Slot | Section | What's needed |
|---|---|---|
| #1 | §1 | An incident that opens the talk. Screenshot-able if possible — a log line, a graph, a Slack message. |
| #2 | §2 | Something expensive you moved out of a request. What it was, how long it took, what broke before. |
| #3 | §3 | A duplicate-work or retry incident. A near-miss counts. |
| #4 | §4 | A transaction, locking, or slow-query incident. Before/after numbers if you have them. |
| #5 | §5 | Optional — a dashboard that said everything was fine while it wasn't. |

Deck currently ships with synthetic examples in these positions, marked
`STORY SLOT #n` in `pycon-ghana.md`. They work as-is; swapping in real ones is
an upgrade, not a blocker.

---

## Structure

Topic-led, not narrative. Seven units, each teaching one idea through one real
incident. Every unit follows the same five beats:

```
1. The situation   — what we were building        (architecture diagram)
2. What broke      — the symptom, concretely
3. Why it broke    — the mechanism                (diagram or code)
4. What we did     — the change                   (diagram v2 + code)
5. The concept     — named, generalised, portable
```

Each slide carries a kicker showing its section and which beat it is, so the
audience always knows where they are.

| Unit | Example | Concept |
|---|---|---|
| 1 | Core + automation server, `BackgroundTasks` under 200k items | Decoupling the request cycle |
| 2 | The midnight loop over 30,000 mandates | Job granularity |
| — | *(interlude)* A daily export run by hand → Airflow; Celery vs Airflow vs Kafka | Picking the tool |
| 3 | Two servers, one cron, a double debit | Locking (pessimistic, optimistic, partitioning) → idempotency |
| 4 | A pipeline stalled by one slow third-party API | Backoff, jitter, circuit breakers, provider failover |
| 5 | A balance that went negative past its check | Atomicity, ledgers vs precomputed balances, transactions |
| 6 | Backfilling 100k rows on a live product | Batching, keyset pagination |
| 7 | Cloud functions diffing database snapshots | Named events, percentiles |

## Status

- [x] Deck built and restructured — `pycon-ghana.html`, **39 slides**
- [x] Speaker notes with a timing budget on every slide
- [ ] Timed rehearsal. 39 slides / 30 min ≈ 46s each — plausible, but unproven
- [ ] Trace IDs: dropped in the §5 rewrite, not yet restored

Preview: `python3 -m http.server 8000` → <http://localhost:8000/pycon-ghana.html>
Press **S** for speaker notes, **Esc** for the slide grid.

## Slide budget

| Section | Slides | Minutes |
|---|---|---|
| Opening | 3 | 2.5 |
| §2 Processing workloads | 11 | 9 |
| §3 Reliability | 9 | 7 |
| §4 Databases | 9 | 7 |
| §5 Observability | 5 | 3.5 |
| Close + Q&A | 2 | 2 |

§2 is deliberately the largest — it's the best-developed material and it sets
up everything after it.

## Cut in the restructure (51 → 39)

Eleven slide-pairs merged where two slides carried one idea — situation and
symptom, fix and concept. Plus one full cut: expand/contract migrations.

Also gone earlier: the thread-pool / GIL slide, N+1 queries, deadlock lock
ordering, and sockets vs HTTP (that one survives in speaker notes as a Q&A
answer).
