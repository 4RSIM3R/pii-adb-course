# Building a Database System in Rust

> **Note:** PII (Politeknik Informatika Indonesia) is a fictional placeholder name created for this course. It is not associated with any Indonesian educational institution. This is an independent, open-source educational project.

## Overview

This is a **4-week intensive course** where you'll build a complete, working relational database management system (RDBMS) from scratch using Rust. Designed for self-study and small group learning, this course takes a hands-on, problem-centered approach to understanding how modern databases work internally.

By the end of this course, you'll have:

- A working database supporting **SELECT**, **INSERT**, **UPDATE**, **DELETE**, and **JOIN** operations
- Deep understanding of storage engines, indexing, transactions, and query optimization
- A portfolio project demonstrating systems programming expertise

## What You'll Build

### Week 1: Storage Foundation

Build the storage layer with page-based disk I/O, buffer pool management, and B+ tree indexes.
**Deliverable:** A storage engine that can efficiently store and retrieve 10,000+ records.

### Week 2: Query Execution

Implement a SQL parser and query executor supporting basic CRUD operations on tables.
**Deliverable:** A database that executes SELECT, INSERT, UPDATE, and DELETE statements.

### Week 3: Transactions and MVCC

Add Multi-Version Concurrency Control (MVCC) for ACID transactions without locks.
**Deliverable:** A transactional database supporting concurrent reads and writes.

### Week 4: Query Optimization and Joins

Implement join algorithms (nested loop, hash join) and a cost-based query optimizer.
**Deliverable:** A complete RDBMS with multi-table joins and query optimization.

## Learning Philosophy

This course follows a **problem-centered approach**:

```
Problem → Theory → Solution → Implementation → Enhancement
```

Each day presents real-world database challenges, teaches the theoretical foundations to solve them, and guides you through practical implementation. No excessive history or theory-only lectures—just hands-on learning.

## Prerequisites

- **Rust fundamentals:** Ownership, borrowing, traits, error handling
- **Data structures:** Trees, hash tables, linked lists
- **Basic SQL:** SELECT, INSERT, UPDATE, DELETE syntax
- **Systems concepts:** Files, memory, basic I/O operations

## Getting Started

### Course Structure

```
docs/
├── README.md          # Course overview and philosophy
├── week_1/            # Storage Foundation
│   ├── README.md      # Week overview
│   ├── day_1.md       # Storage Fundamentals
│   ├── day_2.md       # Buffer Pool Manager
│   ├── day_3.md       # B+ Tree Foundation
│   ├── day_4.md       # B+ Tree Insertion
│   └── day_5.md       # Integration & Testing
├── week_2/            # Query Execution (coming soon)
├── week_3/            # Transactions and MVCC (coming soon)
└── week_4/            # Query Optimization (coming soon)
```

### How to Use This Course

1. **Start with the course overview:** Read [`docs/README.md`](docs/README.md)
2. **Follow week by week:** Each week builds on previous weeks
3. **Complete daily exercises:** Implement each component before moving forward
4. **Test thoroughly:** Use provided test cases and write your own
5. **Review and refactor:** Code quality matters

### Implementation

The `src/` directory contains final code examples. Students are encouraged to:

- Follow along with the course materials
- Implement each component as described
- Test incrementally
- Experiment and optimize

## Course Inspiration

This course is inspired by world-class database courses:

- **Carnegie Mellon University** (CMU 15-445/645: Database Systems)
- **MIT** (6.830: Database Systems)
- **Stanford** (CS 346: Database System Implementation)

Adapted for a compressed 4-week format with maximum hands-on learning.

## Key Concepts Covered

### Storage Layer

- Page-based storage and slotted pages
- Buffer pool management with LRU eviction
- B+ tree indexing (insertion, deletion, splits)
- Disk I/O optimization

### Query Processing

- SQL parsing fundamentals
- Query execution using the iterator model
- Table scans and index scans
- Selection, projection, and join operators

### Transaction Management

- ACID properties
- Multi-Version Concurrency Control (MVCC)
- Snapshot isolation
- Transaction visibility rules

### Query Optimization

- Join algorithms (nested loop, hash join)
- Cost-based optimization
- Query plan selection
- Index selection strategies

## Project Status

- ✅ **Week 1:** Complete course materials
- 🚧 **Week 2:** In development
- 📋 **Week 3:** Planned
- 📋 **Week 4:** Planned

## Contributing

This course is open source and welcomes contributions:

- **Report issues:** Found a bug or unclear explanation? Open an issue
- **Suggest improvements:** Have ideas for better explanations or examples? Submit a PR
- **Share feedback:** Completed the course? Let us know your experience
- **Extend the course:** Want to add advanced topics? Contribute new modules

### Contribution Guidelines

1. Keep explanations clear and practical
2. Include working code examples
3. Add tests for new features
4. Follow the problem-centered learning approach
5. Maintain consistent formatting and style

## Resources

### Recommended Reading

- **Papers:**

  - Codd, E. F. (1970). _A Relational Model of Data for Large Shared Data Banks_
  - Bayer, R., & McCreight, E. M. (1972). _Organization and Maintenance of Large Ordered Indexes_

- **Books:**

  - _Database Internals_ by Alex Petrov (O'Reilly, 2019)
  - _Designing Data-Intensive Applications_ by Martin Kleppmann (O'Reilly, 2017)
  - _Database Systems: The Complete Book_ by Garcia-Molina, Ullman, Widom

- **Online:**
  - [CMU 15-445 Database Systems Course](https://15445.courses.cs.cmu.edu/)
  - [PostgreSQL Internals Documentation](https://www.postgresql.org/docs/current/internals.html)
  - [SQLite Documentation](https://www.sqlite.org/docs.html)

### Rust Resources

- [The Rust Programming Language Book](https://doc.rust-lang.org/book/)
- [Rust By Example](https://doc.rust-lang.org/rust-by-example/)
- [Rustlings (Interactive Exercises)](https://github.com/rust-lang/rustlings)

## License

This course is released under the **MIT License** - feel free to use, modify, and share for educational purposes.

## Acknowledgments

Created as a recreational and educational project to teach database internals to friends and the broader community. Special thanks to the database systems research community and the educators who have made their course materials publicly available.

## Get Started

Ready to build a database? Start here:

- **[→ Course Overview](docs/README.md)**
- **[→ Week 1: Storage Foundation](docs/week_1/README.md)**

---

**Note:** This course is updated frequently. Feel free to open issues to suggest additional topics or improvements.

## Contact

For questions, suggestions, or discussions:

- **Open an issue** in this repository