# Week 1: Storage Foundation – Pages, Files, and B+ Trees

## Week Overview

Welcome to Week 1. This week focuses on building the **storage engine**—the foundational layer of any database system. By the end of the week, you will have implemented a functional storage subsystem capable of efficiently storing and retrieving large volumes of data using disk-based **B+ tree indexes**.

---

## From Information Storage to Modern Databases

Humanity’s progress has always been tied to how effectively it can record and retrieve information. From clay tablets and handwritten ledgers to digital spreadsheets, each generation has developed better ways to **structure** and **access** data.

When computers emerged, the same principle continued. Early digital systems stored data in **flat files**—simple text or binary files containing a sequence of records. Although straightforward, flat files had major drawbacks: searching for one record meant scanning the entire file, and making updates often required rewriting it from scratch.

As data grew, it became clear that a more structured approach was needed. The 1960s introduced **hierarchical** and **network** databases (e.g., IBM’s IMS and CODASYL systems), which arranged data in linked records, enabling faster access but limited flexibility.

A major conceptual leap occurred with **E. F. Codd’s relational model** (_“A Relational Model of Data for Large Shared Data Banks,”_ 1970). Codd proposed that data could be organized in **tables (relations)** governed by formal rules, separating **logical structure** from **physical storage**. This abstraction laid the foundation for modern relational databases.

Even so, storing large, structured datasets efficiently remained a challenge. Researchers such as **Rudolf Bayer and Edward McCreight (1972)** introduced the **B-Tree**, a self-balancing tree structure designed for disk-based indexing (_“Organization and Maintenance of Large Ordered Indexes,” Acta Informatica_). Its evolution, the **B+ Tree**, improved data range queries and remains the dominant indexing structure in databases today.

This historical journey—from unstructured flat files to structured, indexed systems—sets the foundation for your work this week: building the storage layer that enables efficient, structured data access.

---

## The Core Problem

**How can we store structured data on disk—and read or update it efficiently?**

This question lies at the heart of database systems. To answer it, we need to consider four essential aspects:

1. **Representation:** How can structured data (such as tables and records) be translated into bytes on disk?
2. **Organization:** How can these bytes be arranged so the database can quickly find what it needs?
3. **Access:** How can we locate a specific piece of data without reading the entire file?
4. **Persistence:** How can the system ensure data remains consistent and recoverable after crashes or restarts?

For example, imagine a student database with thousands of entries. When a user wants to retrieve information about a specific student (say, a student with ID number **42**), the database must avoid reading every record in order. Instead, it uses a **storage structure** that knows _where_ to find the relevant data quickly.

In this week’s module, you will design and implement the key mechanisms that make this possible: **page-based storage**, **in-memory buffering**, and **B+ tree indexing**.

---

## What You Will Build

By the end of Week 1, you will have implemented the following components:

1. **Page-Based Storage System:** Fixed-size pages (4KB) for storing and organizing records.
2. **Slotted Page Structure:** A layout that supports variable-length records efficiently.
3. **Buffer Pool Manager:** An in-memory cache that minimizes disk I/O using an LRU (Least Recently Used) policy.
4. **B+ Tree Index:** A balanced search tree that enables fast lookups for large datasets.
5. **Disk Manager:** An abstraction layer that handles reading and writing data to disk.

### End-of-Week Demonstration

By Friday, your system will:

- Insert and retrieve records efficiently using a key-based interface
- Manage thousands of records with minimal latency
- Persist data to disk and reload it after restart
- Demonstrate how B+ tree nodes split and remain balanced under growth

---

## Why These Concepts Matter

Every major database—**PostgreSQL**, **MySQL**, **SQLite**, **Oracle**—is built upon these foundational principles:

- **Pages:** The smallest unit of data read or written to disk (e.g., PostgreSQL uses 8KB, MySQL uses 16KB).
- **Buffer Pools:** Memory caches that store recently used pages to reduce disk reads.
- **B+ Trees:** The most common indexing structure for efficient data retrieval.

A strong grasp of these ideas will allow you to understand and optimize how real databases achieve high performance and reliability.

---

## Week 1 Architecture

```
┌─────────────────────────────────────────┐
│         Your Application                │
│    (Insert/Get operations)              │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│         B+ Tree Index                   │
│  (Find which page contains the data)    │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│        Buffer Pool Manager              │
│   (Cache pages in memory, LRU policy)   │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│          Disk Manager                   │
│    (Read/Write pages to disk file)      │
└──────────────┬──────────────────────────┘
               │
               ▼
          [disk file]
```

---

## Learning Progression

### Day 1 – Storage Fundamentals

**Problem:** How should structured data be physically organized on disk?

- Learn why databases use pages.
- Design a slotted page structure and metadata layout.
- Define how data is serialized into bytes.

### Day 2 – Buffer Pool Management

**Problem:** How can we reduce the cost of accessing disk data?

- Implement a memory cache for database pages.
- Apply an LRU (Least Recently Used) eviction policy.
- Manage page pinning, unpinning, and flushing.

### Day 3 – B+ Tree Fundamentals

**Problem:** How can we locate data efficiently?

- Understand B+ tree properties and structure.
- Differentiate between internal and leaf nodes.
- Implement search and traversal logic.

### Day 4 – B+ Tree Insertion and Maintenance

**Problem:** How do we maintain balance as data grows?

- Implement insertion and splitting logic.
- Handle propagation and rebalancing.
- Maintain B+ tree invariants.

### Day 5 – Integration and Testing

**Problem:** How do all components work together as a system?

- Integrate the disk manager, buffer pool, and index layers.
- Test data persistence and performance.
- Conduct benchmarking and review.

---

## Core Concepts

### 1. Page-Oriented Architecture

- Pages as fixed-size storage units (typically 4KB or 8KB).
- Page headers, slots, and record offsets.
- Efficient handling of variable-length records.
- Optimizing page layouts for I/O efficiency.

### 2. Buffer Pool Management

- Caching pages in memory to reduce I/O cost.
- LRU page replacement policy.
- Pin/unpin operations for page usage control.
- Dirty page tracking and write-back mechanisms.

### 3. B+ Tree Structure

- Internal nodes guide searches; leaf nodes hold data.
- Keys and pointers for navigation.
- Leaf node chaining for efficient range queries.
- Logarithmic search and insertion complexity.

### 4. Disk I/O and File Management

- Sequential vs. random I/O operations.
- Page alignment and file organization.
- Introduction to write-ahead logging and crash recovery.

---

## Prerequisite Knowledge

Before beginning this module, you should be comfortable with:

- **Data Structures:** Arrays, linked lists, and trees.
- **File I/O:** Reading and writing binary data.
- **Binary Encoding:** Representing structured data as bytes.

---

## Expected Outcomes

By the end of Week 1, you will be able to:

- Explain how structured data is represented and stored on disk.
- Describe how a buffer pool reduces disk I/O.
- Implement a B+ tree and explain its role in indexing.
- Efficiently store and retrieve large datasets.
- Measure and reason about storage performance.

---

## Common Technical Challenges

1. **Memory Management:** Handling lifetimes in the buffer pool safely.
2. **Pointer Abstraction:** Using page IDs instead of raw pointers for tree navigation.
3. **Serialization:** Converting data structures to and from binary format.
4. **B+ Tree Debugging:** Visualizing insertions and node splits.

---

## Real-World Parallels

- **PostgreSQL:**

  - 8KB “blocks” as storage pages.
  - Shared buffer cache for performance.
  - B+ tree indexes for most query plans.

- **MySQL/InnoDB:**
  - 16KB pages and clustered B+ tree indexes.
  - Sophisticated buffer pool with adaptive flushing.
  - Integrated logging and recovery mechanisms.

The concepts you learn here are directly applied in these production systems.

---

## Weekly Schedule

| Day       | Focus Area                           | Deliverable                       |
| --------- | ------------------------------------ | --------------------------------- |
| **Day 1** | Storage architecture and page design | Page layout specification         |
| **Day 2** | Buffer pool and disk manager         | Functional buffer pool with LRU   |
| **Day 3** | B+ tree structure and search         | Working search and traversal code |
| **Day 4** | B+ tree insertion and balancing      | Insert with node-split support    |
| **Day 5** | Testing and integration              | Complete working storage engine   |

---

## Materials Required

- Text editor or IDE (e.g., VS Code, JetBrains)
- Hex editor (optional, for inspecting binary files)
- Paper or whiteboard (for sketching B+ trees and layouts)

---

## Study and Development Practices

1. **Visualize Before You Code:** Draw diagrams of page layouts and trees.
2. **Test Incrementally:** Verify small components before integration.
3. **Use Logs and Debug Output:** Track how pages and trees change.
4. **Prioritize Correctness Before Optimization:** Functionality first, speed later.
5. **Collaborate and Discuss:** Engage with peers to reinforce understanding.

---

## Glossary of Key Terms

| Term                          | Definition                                                                             |
| ----------------------------- | -------------------------------------------------------------------------------------- |
| **Record**                    | A single piece of data stored in a table (e.g., one student’s information).            |
| **Page**                      | A fixed-size block of data that the database reads or writes to disk in one operation. |
| **Buffer Pool**               | A memory cache that holds recently used pages to reduce disk access time.              |
| **Index**                     | A data structure that accelerates lookups by mapping keys to data locations.           |
| **B+ Tree**                   | A balanced search tree used in databases for efficient lookup and range queries.       |
| **LRU (Least Recently Used)** | A caching policy that evicts the least recently accessed item first.                   |
| **Disk I/O**                  | Input/output operations that read from or write to disk storage.                       |
| **Serialization**             | The process of converting structured data into a byte stream for storage.              |

---

## Looking Ahead

The storage engine you build this week will serve as the foundation for later modules:

- **Week 2:** Query execution built atop pages and indexes.
- **Week 3:** Transaction management and concurrency.
- **Week 4:** Query optimization and cost-based planning.

Each new layer builds upon the previous one, forming a complete, functioning database system.

---

## Additional References

- Codd, E. F. (1970). _A Relational Model of Data for Large Shared Data Banks._ _Communications of the ACM._
- Bayer, R., & McCreight, E. M. (1972). _Organization and Maintenance of Large Ordered Indexes._ _Acta Informatica._
- Garcia-Molina, H., Ullman, J. D., & Widom, J. (2008). _Database Systems: The Complete Book._ Pearson.
- Hellerstein, J. M., & Stonebraker, M. (2005). _Readings in Database Systems (The Red Book)._ MIT Press.
- Kleppmann, M. (2017). _Designing Data-Intensive Applications._ O’Reilly Media.
- Petrov, A. (2020). _Database Internals._ O’Reilly Media.
- Carnegie Mellon University, _15-445/645: Database Systems_ (Lecture Notes on Storage).

---

**Next Step:** [→ Day 1: Storage Fundamentals](day_1.md)
