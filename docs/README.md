# PII – Advanced Database Systems (4-Week Intensive Course)

**Institution:** Politeknik Informatika Indonesia (PII)  
**Delivery Mode:** In-person / Hybrid (includes coding labs)

---

## Course Overview

This intensive 4-week course provides a hands-on exploration of relational database management systems (RDBMS).  
Students will construct a functioning database system from the ground up, gaining a deep understanding of how databases store, process, and manage data.  
By the end of the course, participants will possess both theoretical knowledge and a complete, demonstrable project that highlights their systems programming competencies.

---

## Pedagogical Approach

The course applies a **Problem-Centered Learning** model structured as follows:

> **Problem → Theory → Solution → Implementation → Enhancement**

Each week begins with a real-world problem that motivates the learning objectives.  
Students are guided through the necessary theoretical foundations, followed by implementation, testing, and optimization.  
This structure aligns with **constructivist learning principles** and **Bloom’s Taxonomy**, supporting progression from understanding to creation and evaluation.

---

## Course Learning Outcomes

| #   | Learning Outcome                                                     | Bloom’s Level     |
| --- | -------------------------------------------------------------------- | ----------------- |
| 1   | Explain how data is stored, indexed, and retrieved in modern RDBMSs  | Understand        |
| 2   | Implement the core components of a storage engine and query executor | Apply / Create    |
| 3   | Analyze trade-offs in database design (e.g., storage layout, MVCC)   | Analyze           |
| 4   | Design and implement a simple concurrency control mechanism (MVCC)   | Create            |
| 5   | Optimize query performance through joins and cost-based estimation   | Evaluate / Create |
| 6   | Communicate technical design decisions effectively                   | Evaluate / Create |

---

## Project Deliverables

By the end of the course, students will have developed a basic RDBMS supporting the following features:

- **Data Manipulation:** `INSERT`, `UPDATE`, `DELETE` operations
- **Data Retrieval:** `SELECT` queries with filtering and projection
- **Join Operations:** Basic join algorithms (nested loop, hash join)
- **ACID Transactions:** Multi-Version Concurrency Control (MVCC)
- **Storage Engine:** B+ tree indexing, page-based storage, and buffer pool management
- **Query Execution:** A simple SQL parser and executor

---

## Weekly Structure

### Week 1 – Storage Foundations: Pages, Files, and B+ Trees

**Learning Objectives**

- Understand how databases manage disk storage and buffering.
- Implement a page-based file manager and a B+ tree index.

**Key Topics**

- Page layout and slotted pages
- Buffer pool management (LRU policy)
- B+ tree insertion, deletion, and search

**Deliverable**

- A functioning storage engine capable of storing and retrieving key-value pairs.

**Assessment**

- Code review (20%)
- Quiz on storage concepts
- Performance test on index lookups

---

### Week 2 – Query Execution: From SQL to Data

**Learning Objectives**

- Parse SQL queries into executable plans.
- Implement core query operators using the iterator model.

**Key Topics**

- SQL parsing fundamentals
- Operator trees, selection, projection
- Table scans and index scans

**Deliverable**

- Execution of basic `SELECT`, `INSERT`, `UPDATE`, and `DELETE` queries.

**Assessment**

- Parser correctness tests
- Integration testing for operator pipelines
- Unit test coverage report

---

### Week 3 – Transactions and MVCC: Ensuring Reliability

**Learning Objectives**

- Explain the ACID model and transaction isolation levels.
- Implement Multi-Version Concurrency Control (MVCC) for concurrent transactions.

**Key Topics**

- Transaction lifecycle (BEGIN, COMMIT, ABORT)
- Version chains and snapshot isolation
- Garbage collection of outdated versions

**Deliverable**

- A functioning MVCC system that supports concurrent reads and writes.

**Assessment**

- Simulation of concurrent workloads
- Debug log analysis
- Written explanation of visibility rules

---

### Week 4 – Query Optimization and Joins: Improving Performance

**Learning Objectives**

- Design and evaluate efficient join algorithms.
- Implement a simple cost-based query optimizer.

**Key Topics**

- Nested loop and hash join algorithms
- Query planning and cost estimation
- Optimization heuristics

**Deliverable**

- A working multi-table join engine and basic query optimizer.

**Assessment**

- Benchmark report comparing join algorithms
- Query plan visualization and oral defense
- Final project demonstration

---

## Core Concepts

### Storage Layer

- Page layout and slotted pages
- B+ tree implementation (insertion, deletion, splits, merges)
- Buffer pool management and LRU eviction
- Disk-based storage and file organization

### Query Processing

- SQL parsing and abstract syntax trees
- Operator trees and the iterator model
- Table scans and index scans
- Projection, selection, and join operators

### Transaction Management

- ACID properties and transaction lifecycle
- Multi-Version Concurrency Control (MVCC)
- Isolation levels and visibility rules
- Garbage collection mechanisms

### Indexing and Optimization

- B+ tree indexing
- Hash indexing (basic)
- Join algorithms (nested loop, hash join)
- Cost-based query estimation and optimization heuristics

---

## Technical Stack

- **Language:** Rust (or other approved programming languages)
- **Dependencies:** Minimal; most components are implemented from first principles

---

## Prerequisites

- Fundamental programming skills
- Knowledge of data structures (trees, hash tables)
- Basic SQL proficiency (`SELECT`, `INSERT`, `UPDATE`, `DELETE`)
- Familiarity with operating system concepts (files, memory)

---

## Rationale and Educational Value

This course emphasizes _learning by building_.  
Rather than relying solely on theoretical study, students gain an applied understanding of database systems by implementing all major subsystems themselves.  
The approach mirrors the structure of systems courses at **Carnegie Mellon University**, **MIT**, and **Stanford**, adapted into an accelerated four-week program.

---

## Weekly Learning Pattern

Each week follows a consistent instructional cycle:

- **Day 1:** Introduction to the problem and theoretical discussion
- **Day 2:** Core implementation
- **Day 3:** Integration and testing
- **Day 4:** Optimization and enhancement
- **Day 5:** Review, refactoring, and preparation for the next topic

---

## Assessment and Competencies

Upon completion, students will be able to:

1. Explain how data is stored and retrieved in relational databases.
2. Implement core database components, including storage, execution, and transaction systems.
3. Analyze and justify design trade-offs in system architecture.
4. Optimize query performance using indexing and cost-based techniques.
5. Debug concurrent and transactional systems effectively.
6. Demonstrate systems programming proficiency through a capstone project.

---

## Project Continuity

The course follows a cumulative project structure:

> **Week 1 (Storage)** → **Week 2 (Execution)** → **Week 3 (Transactions)** → **Week 4 (Optimization)**

Each component extends and integrates with the previous week’s implementation, culminating in a complete working database system.

---

## Course Distinctives

- **Comprehensive Implementation:** All major database components are built from scratch.
- **Industry Relevance:** Concepts mirror real-world database architectures (e.g., PostgreSQL MVCC).
- **Portfolio Value:** Students conclude with a tangible, demonstrable system.
- **Problem-Driven Learning:** Each topic begins with a practical, motivating challenge.

---

**Begin with:** [Week 1: Storage Foundations](week_1/README.md)
