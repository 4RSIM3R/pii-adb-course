# Week 1: Storage Foundation - Pages, Files, and B+ Trees

## Week Overview

Welcome to Week 1! This week, you'll build the **storage engine**—the foundational layer of any database system. By the end of this week, you'll have a working storage system that can efficiently store and retrieve data using disk-based B+ tree indexes.

### The Core Problem

**How do databases store millions of records on disk and retrieve them in milliseconds?**

When you execute `SELECT * FROM users WHERE id = 42`, the database doesn't scan through millions of records. Instead, it uses sophisticated data structures and storage techniques to find exactly what you need, fast. This week, you'll implement these techniques yourself.

## What You'll Build This Week

By Friday, you'll have a working storage engine with:

1. **Page-Based Storage System**: Fixed-size pages (4KB) that store multiple records
2. **Slotted Page Structure**: Efficient way to pack variable-length records into pages
3. **Buffer Pool Manager**: In-memory cache for disk pages with LRU eviction
4. **B+ Tree Index**: Self-balancing tree structure for fast lookups
5. **Disk Manager**: Interface for reading/writing pages to disk files

### End-of-Week Demonstration

You'll be able to:

- Insert key-value pairs: `storage.insert(key, value)`
- Retrieve values by key: `storage.get(key) → value`
- Handle thousands of records efficiently
- Persist data to disk and reload it
- See B+ tree splits and node management in action

## Why These Concepts Matter

Every major database (PostgreSQL, MySQL, SQLite, Oracle) uses these exact concepts:

- **Pages**: The fundamental unit of storage (PostgreSQL uses 8KB pages, MySQL uses 16KB)
- **Buffer Pool**: Keeps frequently used pages in memory
- **B+ Trees**: Most common index structure in production databases

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

## Learning Progression

### Day 1: Storage Fundamentals

**Problem**: How do we organize data on disk?

- Understand why databases use pages
- Learn about slotted page architecture
- Design the disk layout

### Day 2: Buffer Pool Management

**Problem**: How do we avoid expensive disk I/O?

- Implement page caching in memory
- Build LRU eviction policy
- Handle page pinning and unpinning

### Day 3: B+ Tree Foundation

**Problem**: How do we find data quickly?

- Understand B+ tree structure and properties
- Design internal vs leaf nodes
- Implement tree traversal (search)

### Day 4: B+ Tree Modifications

**Problem**: How do we handle insertions and maintain balance?

- Implement B+ tree insertion
- Handle node splits
- Maintain tree properties

### Day 5: Integration & Testing

**Problem**: Does everything work together?

- Integration testing
- Performance benchmarking
- Code review and refactoring

## Key Concepts You'll Master

### 1. Page-Oriented Architecture

- Fixed-size pages (typically 4KB or 8KB)
- Page headers and metadata
- Slotted pages for variable-length records
- Page layout optimization

### 2. Buffer Pool Management

- Page replacement policies (LRU)
- Pin/unpin mechanism to prevent eviction
- Dirty page tracking and flushing
- Page table (maps page IDs to frames)

### 3. B+ Tree Structure

- Internal nodes (routing/navigation)
- Leaf nodes (actual data storage)
- Order/fanout of the tree
- Invariants and properties

### 4. B+ Tree Operations

- Search: O(log n) lookup time
- Insertion: Maintaining balance through splits
- Node structure: Keys and pointers
- Leaf node chaining for range scans

### 5. Disk I/O Fundamentals

- Sequential vs random I/O
- Page alignment
- File-based storage
- Crash recovery basics (write-ahead logging preview)

## Prerequisites Refresher

Before diving in, make sure you're comfortable with:

- **Rust Basics**: Ownership, borrowing, traits, Result types
- **Data Structures**: Arrays, linked lists, trees
- **File I/O**: Reading and writing binary data
- **Binary Representations**: How data is stored in bytes

## Success Metrics

By the end of Week 1, you should be able to:

✅ Explain why databases use page-based storage
✅ Describe how a buffer pool reduces disk I/O
✅ Draw a B+ tree structure on paper
✅ Implement B+ tree insertion with node splits
✅ Store and retrieve 10,000+ records efficiently
✅ Measure and optimize storage performance

## Common Challenges This Week

1. **Managing Lifetimes**: Buffer pool requires careful lifetime management
2. **Understanding Pointers**: B+ tree uses page IDs as "pointers"
3. **Binary Serialization**: Converting structs to/from bytes
4. **Debugging Tree Splits**: Visualizing what happens during insertions

Don't worry—we'll tackle each of these systematically!

## Real-World Context

**PostgreSQL** uses this exact architecture:

- Pages called "blocks" (8KB)
- Buffer pool called "shared buffers"
- B+ tree indexes (called "btree" in PostgreSQL)

**MySQL/InnoDB**:

- 16KB pages
- Buffer pool with LRU eviction
- Clustered B+ tree indexes (data stored in leaf nodes)

You're learning production-grade concepts!

## Week 1 Schedule

| Day       | Focus                              | Deliverable                    |
| --------- | ---------------------------------- | ------------------------------ |
| **Day 1** | Storage architecture & page design | Page structure design document |
| **Day 2** | Buffer pool & disk manager         | Working buffer pool with LRU   |
| **Day 3** | B+ tree structure & search         | B+ tree that can search        |
| **Day 4** | B+ tree insertion & splits         | B+ tree that can insert        |
| **Day 5** | Testing & integration              | Complete storage engine        |

## Materials Needed

- Rust development environment
- Text editor/IDE
- Hex editor (optional, for inspecting disk files)
- Paper and pencil (for drawing B+ trees!)

## Tips for Success

1. **Draw First**: Sketch out page layouts and tree structures on paper
2. **Test Incrementally**: Test each function as you write it
3. **Use Debug Prints**: Visualize your B+ tree structure
4. **Start Simple**: Get basic functionality working before optimizing
5. **Ask Questions**: These concepts are challenging—that's normal!

## Looking Ahead

Week 1's storage engine becomes the foundation for:

- **Week 2**: Tables and records built on top of pages
- **Week 3**: Transaction log stored in pages
- **Week 4**: Join algorithms using buffer pool

Every line of code you write this week will be used and extended in future weeks.

---

## Ready to Start?

Each day builds on the previous day. Start with Day 1 and work through systematically. By Friday, you'll have built something impressive!

**[→ Day 1: Storage Fundamentals](day_1.md)**

---

## Additional Resources

- [CMU 15-445 Storage Lecture](https://15445.courses.cs.cmu.edu/)
- [Database Internals Book](https://www.databass.dev/) - Chapter 3 on B-Trees
- [PostgreSQL Internals](https://www.postgresql.org/docs/current/storage.html)
- [Rust Binary Serialization](https://doc.rust-lang.org/std/io/)
