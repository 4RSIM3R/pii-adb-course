# Advance Database Course

> **Note:** PII (Politeknik Informatika Indonesia) is a fictional placeholder name created for this course. It is not associated with any Indonesian educational institution. This is an independent, open-source educational project.

## Course Overview

This intensive four-week course provides a comprehensive, hands-on introduction to database management system (DBMS) implementation using the. The course employs a problem-centered pedagogical approach, emphasizing practical implementation of core database concepts through incremental development of a fully functional relational database management system (RDBMS).

### Learning Objectives

Upon successful completion of this course, students will be able to:

1. **Design and implement** a complete storage layer including page-based disk management, buffer pool mechanisms, and B+ tree indexing structures
2. **Develop** a query execution engine capable of parsing and executing SQL statements (SELECT, INSERT, UPDATE, DELETE, JOIN)
3. **Apply** transaction management principles through Multi-Version Concurrency Control (MVCC) implementation
4. **Analyze and optimize** query performance using cost-based optimization techniques and efficient join algorithms
5. **Demonstrate** proficiency in systems programming through a substantial, production-quality database implementation

### Course Outcomes

By the conclusion of this course, students will have developed:

- A working relational database system supporting standard SQL operations
- Deep understanding of storage engine architecture, indexing mechanisms, transaction processing, and query optimization
- A substantial portfolio project demonstrating advanced systems programming capabilities

## Course Structure and Content

### Week 1: Storage Foundation

**Objective:** Develop the foundational storage layer infrastructure.

**Topics Covered:**

- Page-based disk I/O management
- Buffer pool implementation with LRU eviction policy
- B+ tree index structure design and implementation

**Learning Outcomes:** Students will construct a storage engine capable of efficiently persisting and retrieving datasets exceeding 10,000 records.

**Module Deliverable:** Functional storage engine with benchmarked performance metrics.

### Week 2: Query Execution

**Objective:** Implement query parsing and execution infrastructure.

**Topics Covered:**

- SQL parsing fundamentals
- Query executor design using the iterator model
- Basic CRUD operation implementation (Create, Read, Update, Delete)

**Learning Outcomes:** Students will develop a query execution engine capable of processing standard SQL data manipulation statements.

**Module Deliverable:** Database system executing SELECT, INSERT, UPDATE, and DELETE operations with comprehensive test coverage.

### Week 3: Transactions and Concurrency Control

**Objective:** Implement transaction management and concurrency control mechanisms.

**Topics Covered:**

- ACID property implementation
- Multi-Version Concurrency Control (MVCC) architecture
- Snapshot isolation techniques
- Transaction visibility and versioning

**Learning Outcomes:** Students will construct a transactional database system supporting concurrent read and write operations without pessimistic locking.

**Module Deliverable:** ACID-compliant database with demonstrated concurrent transaction handling.

### Week 4: Query Optimization and Joins

**Objective:** Develop query optimization and multi-table join capabilities.

**Topics Covered:**

- Join algorithm implementation (nested loop, hash join)
- Cost-based query optimization
- Query plan generation and selection
- Index utilization strategies

**Learning Outcomes:** Students will complete a full-featured RDBMS with optimized multi-table join operations.

**Module Deliverable:** Complete relational database management system with query optimizer and comprehensive join support.

## Pedagogical Approach

This course employs a **problem-centered learning methodology** grounded in constructivist educational theory. The instructional design follows a systematic progression:

```
Problem Identification → Theoretical Foundation → Solution Design → Implementation → Evaluation and Enhancement
```

**Instructional Framework:**

Each module presents authentic database engineering challenges, establishes the theoretical and algorithmic foundations necessary for solution development, and provides structured guidance through implementation. The curriculum emphasizes active learning through hands-on practice, prioritizing practical competency development over passive knowledge acquisition.

**Learning Cycle:**

1. **Contextualization:** Introduction of realistic database system requirements and constraints
2. **Conceptual Development:** Presentation of relevant theoretical concepts, algorithms, and design patterns
3. **Guided Implementation:** Structured coding exercises with incremental complexity
4. **Assessment:** Testing and benchmarking of implemented solutions
5. **Reflection:** Analysis of design tradeoffs and optimization opportunities

## Prerequisites

Students enrolling in this course should possess the following foundational knowledge and skills:

### Required Technical Competencies

**Data Structures and Algorithms:**

- Theoretical and practical understanding of tree structures (binary trees, balanced trees)
- Knowledge of hash table implementation and collision resolution
- Familiarity with linked list structures and pointer manipulation
- Basic algorithmic complexity analysis (Big-O notation)

**Database Fundamentals:**

- Working knowledge of SQL syntax and semantics
- Understanding of basic relational operations (selection, projection, join)
- Familiarity with data manipulation language (INSERT, UPDATE, DELETE, SELECT)

**Systems Programming Concepts:**

- Understanding of file system operations and I/O mechanisms
- Knowledge of memory management principles
- Familiarity with process and thread concepts
- Basic understanding of operating system abstractions

## Course Navigation and Structure

### Repository Organization

The course materials are organized in a hierarchical structure designed to support progressive learning:

```
docs/
├── README.md          # Course overview and pedagogical framework
├── week_1/            # Module 1: Storage Foundation
│   ├── README.md      # Module overview and learning objectives
│   ├── day_1.md       # Storage Fundamentals
│   ├── day_2.md       # Buffer Pool Manager
│   ├── day_3.md       # B+ Tree Foundation
│   ├── day_4.md       # B+ Tree Insertion
│   └── day_5.md       # Integration and Testing
├── week_2/            # Module 2: Query Execution (in development)
├── week_3/            # Module 3: Transactions and MVCC (in development)
└── week_4/            # Module 4: Query Optimization (in development)
```

### Recommended Study Approach

Students should follow this structured learning path:

1. **Foundational Review:** Begin with the comprehensive course overview in `docs/README.md` to understand learning objectives, scope, and expectations
2. **Sequential Progression:** Complete modules in sequential order, as each module builds upon concepts and implementations from previous modules
3. **Active Implementation:** Implement each component as specified before proceeding to subsequent lessons
4. **Rigorous Testing:** Execute all provided test cases and develop additional tests to verify correctness and robustness
5. **Code Review and Refinement:** Regularly review and refactor implemented code to maintain quality standards and incorporate optimization opportunities

### Implementation Guidelines

**Reference Implementation:**

The `src/` directory contains reference implementations and code examples. Students are expected to:

- Study the course materials thoroughly before consulting reference code
- Implement each component independently as described in the course materials
- Use incremental testing throughout development
- Compare implementations with reference code for validation and learning
- Experiment with alternative approaches and optimizations

## Academic Foundations

This course draws upon pedagogical approaches and curricular content from leading academic database systems courses:

- **Carnegie Mellon University:** CMU 15-445/645 (Database Systems)
- **Massachusetts Institute of Technology:** 6.830 (Database Systems)
- **Stanford University:** CS 346 (Database System Implementation)

The curriculum has been adapted and restructured for an intensive four-week format, emphasizing practical implementation skills while maintaining rigorous coverage of fundamental database system concepts.

## Core Technical Topics

### Module 1: Storage Layer Architecture

**Fundamental Concepts:**

- Page-based storage organization and slotted page architecture
- Buffer pool management implementing Least Recently Used (LRU) eviction policy
- B+ tree index structure: insertion, deletion, node splitting, and rebalancing algorithms
- Disk I/O optimization strategies and performance considerations

**Skills Developed:** Low-level storage management, memory-efficient data structures, understanding of disk-memory hierarchy

### Module 2: Query Processing and Execution

**Fundamental Concepts:**

- SQL lexical analysis and syntactic parsing
- Query execution engine design using the Volcano iterator model
- Access path selection: table scans versus index scans
- Relational algebra operators: selection, projection, and join implementation

**Skills Developed:** Parser construction, iterator pattern implementation, query execution pipeline design

### Module 3: Transaction Management and Concurrency

**Fundamental Concepts:**

- ACID properties (Atomicity, Consistency, Isolation, Durability) and their implementation
- Multi-Version Concurrency Control (MVCC) architecture and versioning strategies
- Snapshot isolation levels and semantics
- Transaction visibility determination and version management

**Skills Developed:** Concurrent system design, transaction processing, isolation level implementation

### Module 4: Query Optimization and Join Processing

**Fundamental Concepts:**

- Join algorithm implementation: nested loop join, hash join, and merge join
- Cost-based query optimization framework
- Query plan enumeration and selection algorithms
- Index utilization and access path optimization

**Skills Developed:** Performance optimization, cost model development, algorithmic complexity analysis

## Course Development Status

The course materials are under active development with the following completion status:

- **Module 1 (Week 1 - Storage Foundation):** Currently in development
- **Module 2 (Week 2 - Query Execution):** Planned for future release
- **Module 3 (Week 3 - Transactions and Concurrency):** Planned for future release
- **Module 4 (Week 4 - Query Optimization):** Planned for future release

## Contributing to Course Development

This open-source educational project welcomes contributions from the academic and professional community. Contributors may participate in the following ways:

### Types of Contributions

**Issue Reporting:**
Report technical errors, conceptual inaccuracies, or unclear explanations through the issue tracking system.

**Content Enhancement:**
Submit pull requests proposing improvements to explanations, additional examples, or alternative implementation approaches.

**Pedagogical Feedback:**
Share learning experiences, difficulty assessments, and suggestions for improving instructional effectiveness.

**Course Extension:**
Propose and develop additional modules covering advanced database topics or alternative implementation strategies.

### Contribution Standards

Contributors should adhere to the following guidelines:

1. **Clarity and Accessibility:** Maintain clear, precise explanations appropriate for the target audience
2. **Working Implementations:** Ensure all code examples compile and execute correctly
3. **Comprehensive Testing:** Include unit tests and integration tests for all new features
4. **Pedagogical Consistency:** Follow the established problem-centered learning methodology
5. **Code Quality:** Maintain consistent formatting, documentation standards, and idiomatic style
6. **Academic Integrity:** Properly cite sources and attribute referenced materials

## Supplementary Resources

### Academic Literature

**Foundational Papers:**

- Codd, E. F. (1970). "A Relational Model of Data for Large Shared Data Banks." _Communications of the ACM_, 13(6), 377-387.
- Bayer, R., & McCreight, E. M. (1972). "Organization and Maintenance of Large Ordered Indexes." _Acta Informatica_, 1(3), 173-189.
- Bernstein, P. A., & Goodman, N. (1983). "Multiversion Concurrency Control—Theory and Algorithms." _ACM Transactions on Database Systems_, 8(4), 465-483.

**Textbooks:**

- Petrov, A. (2019). _Database Internals: A Deep Dive into How Distributed Data Systems Work_. O'Reilly Media.
- Kleppmann, M. (2017). _Designing Data-Intensive Applications: The Big Ideas Behind Reliable, Scalable, and Maintainable Systems_. O'Reilly Media.
- Garcia-Molina, H., Ullman, J. D., & Widom, J. (2008). _Database Systems: The Complete Book_ (2nd ed.). Pearson.
- Ramakrishnan, R., & Gehrke, J. (2003). _Database Management Systems_ (3rd ed.). McGraw-Hill.

**Online Resources:**

- Carnegie Mellon University: [CMU 15-445/645 Database Systems](https://15445.courses.cs.cmu.edu/)
- PostgreSQL: [Internals Documentation](https://www.postgresql.org/docs/current/internals.html)
- SQLite: [Architecture and Source Code Documentation](https://www.sqlite.org/docs.html)

### Rust Programming Resources

**Official Documentation:**

- Klabnik, S., & Nichols, C. _The Rust Programming Language_. Available at: https://doc.rust-lang.org/book/
- Rust Team. _Rust By Example_. Available at: https://doc.rust-lang.org/rust-by-example/
- Rust Team. _The Rustonomicon: The Dark Arts of Unsafe Rust_. Available at: https://doc.rust-lang.org/nomicon/

**Interactive Learning:**

- Rust Team. _Rustlings: Small Exercises to Get You Used to Reading and Writing Rust Code_. Available at: https://github.com/rust-lang/rustlings

## License

This educational resource is distributed under the MIT License, permitting unrestricted use, modification, and distribution for educational and commercial purposes, subject to the terms specified in the license agreement.

## Acknowledgments

This course represents an independent educational initiative developed to provide accessible, high-quality instruction in database system implementation. The project acknowledges the significant contributions of the database systems research community and particularly those academic institutions that have made their instructional materials publicly available.

**Institutional Acknowledgments:**

- Carnegie Mellon University Database Group
- MIT Computer Science and Artificial Intelligence Laboratory
- Stanford InfoLab

The open educational resources provided by these institutions have been instrumental in shaping modern database systems education.

## Course Entry Points

Students should begin their study with the following materials:

- **[Course Overview and Pedagogical Framework](docs/README.md)**
- **[Module 1: Storage Foundation](docs/week_1/README.md)**

---

## Updates and Maintenance

This course undergoes continuous development and refinement. The community is encouraged to participate in course improvement through issue reporting and pull request submission.

**Version Information:** Course materials are updated regularly to reflect evolving best practices in database system implementation and Rust programming.

## Communication Channels

For academic inquiries, technical questions, or collaborative opportunities:

- **Issue Tracking System:** Submit questions and report issues through the repository's issue tracker
- **Pull Requests:** Propose improvements and contributions through pull requests
- **Discussions:** Engage in technical discussions through the repository discussion forum
