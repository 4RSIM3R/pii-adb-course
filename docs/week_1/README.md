# Module 1: Storage Foundation—Disk Management and Indexing Structures

## Module Overview

This module establishes the foundational storage layer infrastructure essential to all database system implementations. Students will design and construct a complete storage subsystem incorporating page-based disk management, buffer pool caching, and B+ tree indexing structures. Upon module completion, students will possess a functional storage engine demonstrating efficient data persistence and retrieval for large-scale datasets through disk-based indexing mechanisms.

---

## Historical Context: Evolution of Data Storage Systems

### From Physical Records to Digital Storage

The evolution of information storage systems reflects humanity's continuous pursuit of more efficient methods for recording and retrieving knowledge. This progression—from physical artifacts to sophisticated digital systems—provides essential context for understanding modern database architecture.

**Pre-Digital Era:**
Historical information storage mechanisms (clay tablets, paper ledgers, card catalogs) established fundamental organizational principles that persist in digital systems: structured records, indexing for rapid retrieval, and hierarchical categorization.

**Early Digital Systems:**
The advent of electronic computing introduced **flat file storage**—sequential arrangements of fixed or variable-length records stored in text or binary format. While conceptually straightforward, flat file systems exhibited significant performance limitations:

- Linear search complexity (O(n)) requiring complete file traversal for single-record retrieval
- Update operations necessitating file rewriting, creating performance bottlenecks
- Absence of concurrent access mechanisms
- Limited data integrity guarantees

**Structured Database Era:**
The 1960s witnessed the emergence of early database management systems addressing flat file limitations:

- **Hierarchical databases** (IBM's Information Management System) organized data in tree structures
- **Network databases** (CODASYL specification) implemented graph-based data models
- Both provided improved access performance but constrained flexibility through rigid structural requirements

### Theoretical Foundation: The Relational Model

A fundamental paradigm shift occurred with E. F. Codd's seminal 1970 paper, "A Relational Model of Data for Large Shared Data Banks" (Communications of the ACM, 13(6), 377-387). Codd's relational model introduced critical conceptual innovations:

**Logical-Physical Separation:**
The relational model established clear separation between logical data representation (relations, tuples, attributes) and physical storage implementation, enabling optimization independence.

**Mathematical Foundation:**
Relational algebra provided formal theoretical basis for data manipulation operations, enabling query optimization and correctness verification.

**Data Independence:**
Applications interface with logical schema rather than physical storage details, allowing storage reorganization without application modification.

### Indexing Structures: The B-Tree Innovation

Despite the relational model's conceptual elegance, efficient implementation of large-scale persistent storage required algorithmic innovation. Rudolf Bayer and Edward McCreight's 1972 paper, "Organization and Maintenance of Large Ordered Indexes" (Acta Informatica, 1(3), 173-189), introduced the **B-tree** structure specifically designed for disk-based systems.

**B-Tree Design Principles:**

- Self-balancing structure maintaining logarithmic operation complexity
- High fanout minimizing tree height and disk accesses
- Optimized for block-oriented storage devices
- Guaranteed performance bounds for search, insertion, and deletion

**B+ Tree Evolution:**
The B+ tree variant emerged as the dominant database indexing structure through key enhancements:

- Data storage exclusively in leaf nodes
- Linked leaf nodes enabling efficient range queries
- Increased internal node fanout through key-only storage
- Sequential access optimization for both point and range queries

This historical progression—from unstructured sequential files through hierarchical organization to mathematically grounded relational systems with efficient indexing—establishes the theoretical and practical foundation for this module's implementation objectives.

---

## Fundamental Problem Statement

### Central Challenge

**How can database systems efficiently store structured data on persistent storage media while providing rapid read and write access patterns?**

This question represents the foundational challenge in database system implementation. Effective solutions must address multiple interrelated concerns:

### Problem Decomposition

**1. Data Representation Challenge:**
How can abstract logical data structures (relations, tuples, attributes) be mapped to concrete byte sequences suitable for persistent storage on block-oriented devices?

**2. Physical Organization Challenge:**
How should byte sequences be arranged on persistent storage to minimize access latency and maximize throughput for common access patterns?

**3. Efficient Access Challenge:**
How can specific data elements be located without exhaustive linear search through complete datasets?

**4. Persistence and Consistency Challenge:**
How can systems guarantee data durability and maintain consistency invariants across system failures, crashes, and restarts?

### Illustrative Example

Consider a student information system managing records for 100,000 students. When querying information for a specific student (e.g., student_id = 42), the system faces several options:

**Naive Approach (Linear Scan):**

- Read entire dataset sequentially until target record located
- Time complexity: O(n) where n = total records
- For 100,000 records: Average 50,000 reads per query
- Performance: Unacceptable for production systems

**Indexed Approach (B+ Tree):**

- Traverse logarithmic-height tree structure to locate target
- Time complexity: O(log n)
- For 100,000 records: Approximately 5-7 node accesses
- Performance: Acceptable for production deployment

This dramatic performance differential motivates the storage structures implemented in this module.

### Implementation Objectives

To address these challenges, this module develops three integrated mechanisms:

1. **Page-Based Storage Architecture:** Fixed-size block organization optimized for disk I/O characteristics
2. **Buffer Pool Management:** In-memory caching layer minimizing expensive disk accesses
3. **B+ Tree Indexing:** Logarithmic-complexity search structure enabling efficient data location

---

## Module Components and Implementation Specifications

Upon module completion, students will have designed and implemented the following integrated subsystems:

### Component Specifications

**1. Page-Based Storage System**

- Fixed-size page allocation (4KB page size standard)
- Page header metadata including page type, free space tracking, and slot count
- Slotted page architecture supporting variable-length record storage
- Page serialization and deserialization mechanisms

**2. Slotted Page Structure**

- Dynamic slot directory management
- Variable-length record support through offset-based addressing
- Space utilization optimization through compaction mechanisms
- Free space tracking and allocation algorithms

**3. Buffer Pool Manager**

- In-memory page caching with configurable capacity
- Least Recently Used (LRU) replacement policy implementation
- Page pinning and unpinning protocol for safe concurrent access
- Dirty page tracking and write-back mechanisms
- Frame management and page-to-frame mapping

**4. B+ Tree Index Structure**

- Internal node implementation with key-pointer pairs
- Leaf node implementation with key-value storage
- Node splitting algorithms maintaining balance invariants
- Search, insertion, and traversal operations
- Leaf node chaining for range query support

**5. Disk Manager Abstraction**

- File-based persistent storage interface
- Page-aligned I/O operations
- Page identifier (PageID) addressing scheme
- Read and write primitives for page-level operations

### Module Deliverable: Complete Storage Engine

**Functional Requirements:**

Students will demonstrate a fully integrated storage engine supporting complete CRUD (Create, Read, Update, Delete) operations through the following capabilities:

**Create Operations:**

- Insert new key-value pairs into B+ tree index
- Automatic page allocation for new data
- Node splitting when capacity thresholds exceeded
- Maintenance of tree balance invariants

**Read Operations:**

- Efficient point queries through B+ tree traversal (O(log n) complexity)
- Range query support through leaf node traversal
- Buffer pool integration minimizing disk I/O

**Update Operations:**

- In-place value modification for existing keys
- Atomic update semantics
- Dirty page marking for write-back

**Delete Operations:**

- Key removal from B+ tree structure
- Space reclamation within pages
- Tree rebalancing as necessary

**System Performance Characteristics:**

- **Scalability:** Support for datasets exceeding 10,000 records
- **Latency:** Sub-millisecond response time for indexed lookups
- **Persistence:** Data durability across system restarts
- **Correctness:** Maintenance of B+ tree structural invariants under all operations

**Demonstration Requirements:**

By module conclusion (Day 5), students must demonstrate:

1. **Complete CRUD Functionality:** All four operations (Create, Read, Update, Delete) functioning correctly through key-based interface
2. **Large Dataset Management:** Successful operation with datasets containing thousands of records maintaining acceptable latency characteristics
3. **Data Persistence:** Ability to persist data to disk and successfully reload complete dataset after system restart
4. **B+ Tree Visualization:** Demonstration of node splitting behavior and tree rebalancing maintaining structural invariants during growth
5. **Performance Metrics:** Documented performance measurements including operation latency and I/O statistics

---

## Relevance to Production Database Systems

### Industry Standard Architecture

The storage layer concepts implemented in this module constitute fundamental architectural components of all major production database management systems. Understanding these mechanisms provides essential foundation for database engineering practice.

### Production System Implementations

**PostgreSQL:**

- Page size: 8KB blocks (configurable at compile time)
- Shared buffer pool with clock-sweep replacement variant
- B+ tree indexes (btree access method) as primary indexing structure
- TOAST (The Oversized-Attribute Storage Technique) for large values

**MySQL/InnoDB:**

- Page size: 16KB (configurable: 4KB, 8KB, 16KB)
- Buffer pool with adaptive hash indexing and sophisticated prefetching
- Clustered B+ tree organization with data stored in leaf nodes
- Doublewrite buffer for crash recovery

**SQLite:**

- Page size: Configurable (512 bytes to 64KB, default 4KB)
- Simple page cache with LRU eviction
- B+ tree for both tables (rowid-based) and indexes
- Single-file database design

**Oracle Database:**

- Block size: 8KB default (configurable: 2KB to 32KB)
- Buffer cache in System Global Area (SGA)
- B+ tree indexes with advanced compression techniques
- Automated Storage Management (ASM)

### Transferable Knowledge

**Understanding Developed Through This Module:**

1. **Performance Analysis:** Ability to reason about I/O costs and cache hit ratios in production systems
2. **Tuning Capabilities:** Understanding buffer pool sizing and page allocation impacts on performance
3. **Architecture Comprehension:** Recognition of storage layer design patterns across different DBMS implementations
4. **Debugging Skills:** Capacity to diagnose storage-related performance issues in production environments

**Career Applications:**

- Database administration and performance tuning
- Storage engine development and optimization
- Distributed database system design
- Data infrastructure engineering

---

## System Architecture

### Layered Architecture Model

The storage engine implements a layered architecture with clear separation of concerns, following established database system design patterns. Each layer provides well-defined abstractions to upper layers while hiding implementation complexity.

```
┌────────────────────────────────────────────────┐
│         Application Layer                      │
│    (CRUD operations: Insert, Get,              │
│     Update, Delete)                            │
└────────────────┬───────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────┐
│         Index Layer (B+ Tree)                  │
│    - Key-based data location                   │
│    - Logarithmic search complexity             │
│    - Automatic balancing and splits            │
└────────────────┬───────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────┐
│         Buffer Pool Manager                    │
│    - Page caching and memory management        │
│    - LRU replacement policy                    │
│    - Pin/unpin protocol                        │
└────────────────┬───────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────┐
│         Disk Manager                           │
│    - Page-level I/O abstraction                │
│    - File management                           │
│    - Page addressing (PageID)                  │
└────────────────┬───────────────────────────────┘
                 │
                 ▼
          [Persistent Storage]
```

### Layer Responsibilities

**Application Layer:**

- Issues CRUD operations to storage engine
- Operates with key-value abstraction
- No knowledge of physical storage details

**Index Layer (B+ Tree):**

- Maps keys to physical page locations
- Maintains sorted order for efficient search
- Handles tree rebalancing and node splits
- Provides O(log n) search complexity guarantee

**Buffer Pool Manager:**

- Caches frequently accessed pages in memory
- Implements replacement policy (LRU) when capacity exceeded
- Manages page lifecycle (fetch, pin, unpin, flush)
- Tracks dirty pages requiring write-back
- Reduces expensive disk I/O operations

**Disk Manager:**

- Provides page-level abstraction over file I/O
- Handles page serialization and deserialization
- Manages page allocation and addressing
- Ensures atomic page writes

**Persistent Storage:**

- File-based storage on disk
- Maintains durability guarantees
- Survives system restarts and crashes

---

## Daily Learning Progression

### Day 1: Storage Fundamentals and Page Architecture

**Module Problem:** How should structured data be physically organized on persistent block-oriented storage devices?

**Learning Objectives:**

- Analyze the rationale for page-based storage organization in database systems
- Design slotted page structure supporting variable-length records
- Implement page header metadata and slot directory mechanisms
- Develop serialization and deserialization functions for page persistence

**Key Concepts:**

- Page as fundamental storage unit
- Fixed-size allocation and alignment requirements
- Slotted page architecture for flexibility
- Binary encoding and byte-level representation

**Deliverable:** Page structure specification and basic implementation with serialization capability

### Day 2: Buffer Pool Management and Caching Strategy

**Module Problem:** How can database systems minimize expensive disk I/O operations through intelligent memory management?

**Learning Objectives:**

- Implement memory-based page caching layer
- Design and construct Least Recently Used (LRU) replacement policy
- Develop page pinning and unpinning protocol for safe access
- Implement dirty page tracking and write-back mechanisms

**Key Concepts:**

- Memory hierarchy and I/O cost analysis
- Cache replacement algorithms and policies
- Page lifecycle management
- Concurrency considerations in buffer pool access

**Deliverable:** Functional buffer pool manager with LRU eviction and page lifecycle management

### Day 3: B+ Tree Structure and Search Operations

**Module Problem:** How can database systems locate specific data elements without exhaustive linear search?

**Learning Objectives:**

- Analyze B+ tree structural properties and invariants
- Differentiate internal node (routing) and leaf node (data) responsibilities
- Implement B+ tree search algorithm with logarithmic complexity
- Develop tree traversal logic navigating internal to leaf nodes

**Key Concepts:**

- B+ tree structural invariants
- Node types and their distinct roles
- Search algorithm implementation
- Tree traversal and navigation

**Deliverable:** B+ tree structure with functional search and traversal operations

### Day 4: B+ Tree Insertion and Rebalancing

**Module Problem:** How do database systems maintain balanced tree structures as data volume increases?

**Learning Objectives:**

- Implement key insertion algorithms for both leaf and internal nodes
- Design and construct node splitting logic maintaining tree invariants
- Develop split propagation mechanism for multi-level splits
- Ensure maintenance of all B+ tree structural properties

**Key Concepts:**

- Insertion algorithms for different node types
- Node splitting and key redistribution
- Parent update and propagation logic
- Balance maintenance under growth

**Deliverable:** Complete B+ tree implementation supporting insertion with automatic rebalancing

### Day 5: System Integration, Testing, and Performance Evaluation

**Module Problem:** How do independently developed components integrate into a cohesive, functional storage engine?

**Learning Objectives:**

- Integrate disk manager, buffer pool, and B+ tree index layers
- Develop comprehensive test suite validating correctness of all CRUD operations
- Conduct performance benchmarking measuring latency and throughput
- Verify data persistence across system restarts

**Key Concepts:**

- System integration patterns
- Testing strategies for storage systems
- Performance measurement and analysis
- Correctness validation under various workloads

**Deliverable:** Complete, tested storage engine with documented performance characteristics

---

## Theoretical Foundations and Core Concepts

### Domain 1: Page-Oriented Storage Architecture

**Fundamental Principles:**

**Page as Storage Quantum:**
The page (also termed "block" in some systems) represents the fundamental unit of data transfer between persistent storage and memory. Database systems adopt fixed-size page allocation (typically 4KB, 8KB, or 16KB) to align with operating system page sizes and storage device block sizes, optimizing I/O efficiency.

**Slotted Page Organization:**
To accommodate variable-length records within fixed-size pages, database systems employ slotted page architecture:

- Page header: Metadata including page type, free space pointer, slot count
- Slot directory: Array mapping slot IDs to record offsets within page
- Record storage: Actual record data stored from end of page backwards
- Free space: Grows from middle as slots and records compete for space

**Design Rationale:**
This indirection through slot directory enables:

- Efficient variable-length record storage
- Record compaction without changing external identifiers
- Space reuse after deletions
- Support for record growth through updates

### Domain 2: Buffer Pool Management

**Cache Hierarchy and I/O Cost:**

Modern computer systems exhibit dramatic performance differences across storage hierarchy:

- Memory access: ~100 nanoseconds
- SSD random access: ~100 microseconds (1000× slower than memory)
- HDD random access: ~10 milliseconds (100,000× slower than memory)

Buffer pool management addresses this disparity through memory-based page caching.

**Least Recently Used (LRU) Replacement Policy:**

When buffer pool reaches capacity and requires frame for new page:

1. Identify least recently accessed page among unpinned pages
2. If dirty, write back to persistent storage
3. Evict page from buffer pool
4. Load new page into freed frame

**Page Lifecycle Management:**

**Pinning Protocol:**
Pages under active use are "pinned" (reference count > 0) and protected from eviction. Callers must unpin pages after use, enabling eviction eligibility.

**Dirty Bit Tracking:**
Modified pages are marked dirty, requiring write-back before eviction to maintain persistence.

### Domain 3: B+ Tree Indexing Structure

**Structural Properties:**

B+ trees maintain following invariants:

- All leaf nodes at same depth (perfect balance)
- Internal nodes contain only keys and child pointers
- Leaf nodes contain keys and associated values (or record pointers)
- Each node contains between ⌈m/2⌉ and m children (except root)
- Keys within nodes maintained in sorted order
- Leaf nodes form linked list enabling efficient range queries

**Search Algorithm Complexity:**

For tree with n keys and fanout f:

- Tree height h = O(log_f(n))
- Search complexity: O(log_f(n)) node accesses
- Typical fanout: 100-200, enabling millions of keys with 2-3 levels

**Insertion and Splitting:**

When inserting key into full node:

1. Split node into two nodes at median key
2. Promote median key to parent
3. If parent full, recursively split parent
4. Splitting may propagate to root, increasing tree height by one

**Advantages Over Binary Search Trees:**

- High fanout minimizes tree height
- Optimized for disk-based systems (amortizes seek cost)
- Excellent cache locality within nodes
- Guaranteed logarithmic complexity (unlike unbalanced BST)

### Domain 4: Disk I/O and File Management

**Access Pattern Characteristics:**

**Sequential I/O:**
Reading consecutive blocks achieves high throughput through:

- Prefetching and read-ahead optimizations
- Amortized seek cost across multiple blocks
- Typical throughput: 100-500 MB/s (SSD/HDD)

**Random I/O:**
Non-sequential access patterns suffer from:

- Individual seek cost per operation
- No prefetching benefit
- Dramatically reduced throughput

**Page Alignment:**

Database files align pages to storage device block boundaries:

- Ensures atomic page writes (single device block update)
- Prevents torn pages (partial writes during crashes)
- Optimizes I/O performance through aligned access

**Durability Considerations:**

Introduction to write-ahead logging (WAL) principle:

- Log changes before applying to data pages
- Enables crash recovery and transaction atomicity
- Foundation for Module 3 (Transaction Management)

---

## Required Prerequisite Knowledge

Students beginning this module must possess foundational competencies in the following areas:

### Data Structures and Algorithms

**Essential Knowledge:**

- **Array structures:** Fixed-size sequential allocation, index-based access
- **Linked lists:** Pointer-based structures, node insertion and deletion
- **Tree structures:** Binary trees, tree traversal algorithms (in-order, pre-order, post-order)
- **Complexity analysis:** Big-O notation, analyzing algorithm time and space requirements

**Application in Module:**
Students will extend tree structure knowledge to implement B+ trees, apply complexity analysis to evaluate indexing performance, and utilize arrays for page slot directories.

### File I/O and Binary Operations

**Essential Knowledge:**

- **File operations:** Opening, reading, writing, and closing files
- **Binary data manipulation:** Reading and writing raw byte sequences
- **Seek operations:** Random access within files using offset-based positioning
- **Buffer management:** Understanding of buffered versus unbuffered I/O

**Application in Module:**
File I/O capabilities are fundamental to disk manager implementation, enabling page-level read and write operations.

### Binary Encoding and Serialization

**Essential Knowledge:**

- **Binary representation:** Converting integers, strings, and structures to byte sequences
- **Endianness:** Understanding byte order in multi-byte values
- **Alignment:** Data structure padding and alignment requirements
- **Fixed versus variable-length encoding:** Trade-offs in representation strategies

**Application in Module:**
Serialization knowledge enables implementation of page persistence mechanisms, converting in-memory structures to disk-storable byte sequences.

### Programming Language Proficiency

**Rust-Specific Requirements:**

- Ownership and borrowing principles
- Lifetime management
- Trait implementation
- Result and Option types for error handling
- Smart pointers (Box, Rc, RefCell)

---

## Module Learning Outcomes

Upon successful completion of this module, students will demonstrate the following competencies:

### Conceptual Understanding

1. **Explain** the rationale for page-based storage organization in database systems and analyze the trade-offs in page size selection
2. **Describe** buffer pool operation mechanisms and articulate how caching reduces disk I/O costs
3. **Analyze** B+ tree structural properties and explain how these properties enable efficient indexing
4. **Compare** different storage and indexing strategies, evaluating trade-offs in performance, space utilization, and implementation complexity

### Implementation Competencies

5. **Implement** complete page-based storage system with slotted page architecture supporting variable-length records
6. **Construct** functional buffer pool manager with LRU replacement policy and page lifecycle management
7. **Develop** B+ tree index structure supporting insertion, search, and automatic rebalancing
8. **Integrate** multiple storage subsystems into cohesive storage engine

### Analytical and Evaluation Skills

9. **Measure and analyze** storage system performance through benchmarking, interpreting latency and throughput metrics
10. **Evaluate** correctness of CRUD operations through comprehensive testing across various workload patterns
11. **Reason** about storage performance characteristics, predicting behavior under different access patterns and data volumes

---

## Common Implementation Challenges and Solutions

Students typically encounter the following technical challenges during module implementation:

### Challenge 1: Memory Lifecycle Management

**Problem Description:**
Buffer pool implementation requires careful management of page lifetimes, particularly in Rust's ownership system. Pages may be simultaneously referenced by buffer pool, B+ tree nodes, and application code.

**Common Issues:**

- Premature deallocation of pages still in use
- Reference cycles preventing proper cleanup
- Violation of Rust's borrowing rules in concurrent access patterns

**Recommended Approach:**

- Utilize smart pointers (Rc, Arc) for shared ownership
- Implement reference counting for pin/unpin protocol
- Design clear ownership boundaries between subsystems

### Challenge 2: Pointer Abstraction in Persistent Structures

**Problem Description:**
In-memory pointers (memory addresses) cannot be directly persisted to disk. Database systems require stable identifiers surviving system restarts.

**Common Issues:**

- Attempting to store raw pointer values in persistent pages
- Confusion between in-memory and on-disk representations
- Invalid pointers after deserialization

**Recommended Approach:**

- Use PageID (integer page numbers) instead of raw pointers
- Implement translation layer converting PageID to memory addresses through buffer pool
- Maintain clear separation between logical (PageID) and physical (pointer) addressing

### Challenge 3: Binary Serialization and Deserialization

**Problem Description:**
Converting complex data structures to flat byte sequences requires attention to encoding details, alignment requirements, and cross-platform compatibility.

**Common Issues:**

- Endianness problems causing incorrect value interpretation
- Alignment violations triggering panics or undefined behavior
- Variable-length field handling complexity
- Version incompatibility across serialization format changes

**Recommended Approach:**

- Define explicit binary format specifications with fixed endianness
- Use binary encoding libraries when appropriate (e.g., bincode for Rust)
- Document page format with clear diagrams
- Implement validation during deserialization

### Challenge 4: B+ Tree Debugging and Visualization

**Problem Description:**
B+ tree behavior, particularly during splitting and rebalancing, can be difficult to understand without visualization. Bugs may manifest only with specific insertion sequences.

**Common Issues:**

- Incorrect split key selection causing imbalanced trees
- Parent update logic errors during propagation
- Leaf chain breakage during node splits
- Difficult-to-reproduce bugs in edge cases

**Recommended Approach:**

- Implement tree visualization functions (console output or graphical)
- Create comprehensive test suite with known insertion sequences
- Add assertions verifying invariants after each operation
- Use step-by-step debugging for complex split scenarios
- Draw tree structures on paper before coding

### Challenge 5: Testing Persistence and Recovery

**Problem Description:**
Verifying that data persists correctly across restarts requires systematic testing approach.

**Common Issues:**

- Incomplete page write-back leaving data in memory only
- Buffer pool flush logic errors
- File synchronization issues
- Test cleanup problems affecting subsequent test runs

**Recommended Approach:**

- Implement explicit flush operations for testing
- Create dedicated test files, cleaning up after each test
- Verify data by reloading from clean buffer pool
- Test crash scenarios (simulated) with partial writes

---

## Module Schedule and Deliverables

| Day   | Focus Area                                | Primary Deliverable                                       | Assessment Weight |
| ----- | ----------------------------------------- | --------------------------------------------------------- | ----------------- |
| Day 1 | Storage architecture and page design      | Page structure implementation with serialization          | 15%               |
| Day 2 | Buffer pool and disk manager              | Functional buffer pool with LRU replacement policy        | 20%               |
| Day 3 | B+ tree structure and search operations   | B+ tree with working search and traversal functionality   | 20%               |
| Day 4 | B+ tree insertion and rebalancing         | Complete B+ tree supporting insertion with node splitting | 25%               |
| Day 5 | System integration, testing, benchmarking | Integrated storage engine with complete CRUD operations   | 20%               |

**Note:** Daily deliverables build incrementally. Each day's work extends previous implementations rather than creating isolated components.

---

## Required Development Tools and Materials

### Software Requirements

**Development Environment:**

- Integrated Development Environment (IDE) or text editor with Rust support
  - Recommended: Visual Studio Code with rust-analyzer extension
  - Alternative: IntelliJ IDEA with Rust plugin, Vim with Rust plugins
- Rust toolchain (rustc, cargo) - latest stable version
- Version control system (Git)

**Testing and Analysis Tools:**

- Unit testing framework (built-in Rust test framework)
- Performance profiling tools (e.g., perf, Flamegraph)
- Memory debugging tools (e.g., Valgrind for checking memory issues)

**Optional but Recommended:**

- Hex editor for binary file inspection (e.g., hexdump, HxD, Hex Fiend)
- Database visualization tools for understanding production systems
- Drawing/diagramming tools for architecture visualization

### Physical Materials

**Recommended for Learning:**

- Whiteboard or large paper for tree structure visualization
- Graph paper for page layout diagram sketching
- Notebook for algorithm design and debugging notes

---

## Recommended Study and Development Practices

### Design-First Approach

**1. Conceptualize Before Implementing:**

- Sketch data structure layouts on paper before coding
- Draw B+ tree state transitions for insertion scenarios
- Design binary formats with explicit byte-level specifications
- Document interface contracts between subsystems

**Rationale:** Upfront design reduces implementation errors and clarifies requirements before committing to code.

### Incremental Development and Testing

**2. Test-Driven Development:**

- Write unit tests before implementing functionality
- Verify small components independently before integration
- Maintain comprehensive test suite throughout development
- Add regression tests when bugs are discovered

**Rationale:** Early error detection reduces debugging time and ensures correctness at each development stage.

### Systematic Debugging

**3. Instrumentation and Logging:**

- Implement detailed logging for page operations
- Track buffer pool state transitions
- Visualize B+ tree structure after each operation
- Log I/O operations for understanding access patterns

**Rationale:** Observable system behavior enables rapid bug identification and performance analysis.

### Quality-First Implementation

**4. Correctness Before Performance:**

- Prioritize correct implementation over optimization
- Establish correctness through testing before profiling
- Optimize only after identifying actual bottlenecks through measurement
- Maintain readability and documentation throughout optimization

**Rationale:** Premature optimization introduces complexity without guaranteed benefit. Correct, measurable code enables effective optimization.

### Collaborative Learning

**5. Peer Engagement:**

- Discuss design approaches with fellow students
- Conduct code reviews to identify potential issues
- Explain implementation decisions to reinforce understanding
- Seek assistance when blocked rather than struggling in isolation

**Rationale:** Collaborative learning reinforces understanding and exposes alternative approaches.

---

## Technical Terminology Reference

| Term                          | Definition                                                                                                                                  |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **Record**                    | A single data tuple stored within a database table, consisting of one or more attribute values.                                             |
| **Page (Block)**              | Fixed-size storage unit representing the fundamental quantum of data transfer between persistent storage and memory (typically 4KB-16KB).   |
| **Buffer Pool**               | In-memory cache managing frequently accessed pages, reducing expensive disk I/O operations through intelligent replacement policies.        |
| **Index**                     | Auxiliary data structure mapping keys to physical data locations, enabling efficient data retrieval without exhaustive scanning.            |
| **B+ Tree**                   | Self-balancing tree structure optimized for disk-based storage, maintaining sorted order and guaranteeing logarithmic operation complexity. |
| **LRU (Least Recently Used)** | Cache replacement policy selecting victim page based on temporal access patterns, evicting least recently accessed unpinned page.           |
| **Disk I/O**                  | Input/output operations transferring data between volatile memory and persistent storage media (SSD, HDD).                                  |
| **Serialization**             | Process of converting structured in-memory data representations to sequential byte streams suitable for persistent storage.                 |
| **Deserialization**           | Reverse process reconstructing structured in-memory data from persistent byte sequences.                                                    |
| **PageID**                    | Logical page identifier (typically integer) providing stable, persistent reference to pages across system restarts.                         |
| **Slotted Page**              | Page organization strategy using indirection through slot directory, enabling efficient variable-length record management.                  |
| **Pinning**                   | Buffer pool operation incrementing page reference count, protecting page from eviction while in active use.                                 |
| **Dirty Page**                | Modified page requiring write-back to persistent storage before eviction to maintain durability.                                            |
| **Node Splitting**            | B+ tree rebalancing operation dividing overflowing node into two nodes, maintaining structural invariants.                                  |
| **Fanout**                    | Maximum number of child pointers in B+ tree internal node, determining tree height and search performance.                                  |
| **Leaf Node Chain**           | Linked list connecting B+ tree leaf nodes in sorted order, enabling efficient range query traversal.                                        |

---

## Module Integration with Subsequent Courses

### Forward Dependencies

The storage engine implementation developed in this module provides essential foundation for all subsequent course modules:

**Module 2: Query Execution Engine**

- Query operators interface with B+ tree index for data access
- Sequential and index scan operators utilize storage layer
- Page-based record retrieval through buffer pool interface

**Module 3: Transaction Management and MVCC**

- Transaction versioning implemented within page structures
- Buffer pool extended with transaction-aware page management
- Dirty page tracking integrated with transaction commit protocol

**Module 4: Query Optimization**

- Cost model incorporates page access costs and I/O estimates
- Index statistics derived from B+ tree structure
- Physical plan selection based on storage layer characteristics

### Architectural Continuity

The layered architecture established in this module persists throughout course:

```
Module 4: Query Optimization Layer
           ↓
Module 3: Transaction Management Layer
           ↓
Module 2: Query Execution Layer
           ↓
Module 1: Storage Layer (this module)
           ↓
       Persistent Storage
```

Each subsequent module extends and builds upon this module's implementations while maintaining clear abstraction boundaries.

---

## Academic and Professional References

### Foundational Papers

- Codd, E. F. (1970). "A Relational Model of Data for Large Shared Data Banks." _Communications of the ACM_, 13(6), 377-387.
- Bayer, R., & McCreight, E. M. (1972). "Organization and Maintenance of Large Ordered Indexes." _Acta Informatica_, 1(3), 173-189.
- Comer, D. (1979). "The Ubiquitous B-Tree." _ACM Computing Surveys_, 11(2), 121-137.

### Textbooks

- Garcia-Molina, H., Ullman, J. D., & Widom, J. (2008). _Database Systems: The Complete Book_ (2nd ed.). Pearson Education.
- Ramakrishnan, R., & Gehrke, J. (2003). _Database Management Systems_ (3rd ed.). McGraw-Hill.
- Petrov, A. (2019). _Database Internals: A Deep Dive into How Distributed Data Systems Work_. O'Reilly Media.
- Kleppmann, M. (2017). _Designing Data-Intensive Applications: The Big Ideas Behind Reliable, Scalable, and Maintainable Systems_. O'Reilly Media.

### Academic Course Materials

- Carnegie Mellon University. "15-445/645: Database Systems." Available: https://15445.courses.cs.cmu.edu/
  - Recommended lectures: Storage Models, Buffer Pool Manager, B+ Tree Indexes
- MIT. "6.830/6.814: Database Systems." Available: http://db.csail.mit.edu/6.830/
- Stanford University. "CS 346: Database System Implementation."

### Production System Documentation

- PostgreSQL Global Development Group. "PostgreSQL Internals Documentation: Database Physical Storage." Available: https://www.postgresql.org/docs/current/storage.html
- MySQL. "InnoDB Storage Engine Architecture." Available: https://dev.mysql.com/doc/refman/8.0/en/innodb-architecture.html
- SQLite. "Database File Format." Available: https://www.sqlite.org/fileformat.html

---

## Module Commencement

Students should proceed to the first instructional day:

**[→ Day 1: Storage Fundamentals](day_1.md)**

---

**Note on Academic Integrity:** This module requires substantial independent implementation. While conceptual discussions with peers are encouraged, all submitted code must represent individual work. Copying implementations from external sources or other students constitutes academic misconduct.
