# Advanced Database Systems: Implementation and Architecture

## Four-Week Intensive Course

**Institution:** Politeknik Informatika Indonesia (PII)  
**Course Code:** DBS-403  
**Delivery Mode:** Hybrid (In-person instruction with supervised laboratory sessions)  
**Prerequisites:** Data Structures, Systems Programming, Database Fundamentals

---

## Course Description

This intensive four-week course provides comprehensive instruction in relational database management system (RDBMS) architecture and implementation. Through a project-based learning approach, students will design and construct a complete database system from foundational components, developing both theoretical understanding and practical competency in database system engineering.

The course emphasizes active learning through systematic implementation of core database subsystems, including storage management, query processing, transaction control, and query optimization. Upon completion, students will have developed a fully functional relational database system demonstrating professional-level systems programming capabilities and comprehensive understanding of database internals.

---

## Pedagogical Framework

This course employs a **Problem-Centered Learning (PCL)** methodology grounded in constructivist educational theory and aligned with Bloom's Revised Taxonomy. The instructional design follows a systematic five-phase progression:

> **Problem Identification → Theoretical Foundation → Solution Design → Implementation → Evaluation and Enhancement**

**Instructional Philosophy:**

Each instructional module initiates with authentic database engineering challenges that establish immediate relevance and motivation for learning. Students progress through structured theoretical instruction, followed by guided implementation exercises, comprehensive testing protocols, and performance optimization activities. This pedagogical approach facilitates cognitive progression from foundational understanding (Remember, Understand) through application and analysis to higher-order thinking skills (Evaluate, Create).

**Alignment with Educational Standards:**

The curriculum design adheres to constructivist principles emphasizing active knowledge construction, scaffolded learning experiences, and authentic assessment through project-based evaluation. This approach ensures students develop both declarative knowledge (understanding concepts) and procedural knowledge (implementing solutions).

---

## Course Learning Outcomes

Upon successful completion of this course, students will demonstrate the following competencies mapped to Bloom's Revised Taxonomy cognitive levels:

| CLO | Learning Outcome Description                                                                                                         | Cognitive Domain Level | Assessment Method                     |
| --- | ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------- | ------------------------------------- |
| 1   | Explain the architectural principles and mechanisms underlying data storage, indexing, and retrieval in modern RDBMS implementations | Understand             | Written examination, Technical report |
| 2   | Implement functional storage engine components and query execution infrastructure using systems programming techniques               | Apply, Create          | Laboratory assignments, Code review   |
| 3   | Analyze and evaluate architectural trade-offs in database system design, including storage layouts and access methods                | Analyze, Evaluate      | Design documentation, Oral defense    |
| 4   | Design and construct concurrency control mechanisms implementing Multi-Version Concurrency Control (MVCC) principles                 | Create                 | Implementation project, Testing suite |
| 5   | Optimize query execution performance through implementation of join algorithms and cost-based query optimization strategies          | Evaluate, Create       | Performance benchmarking, Report      |
| 6   | Articulate and defend technical design decisions through formal documentation and professional presentation                          | Evaluate               | Project presentation, Documentation   |

---

## Capstone Project Specifications

The culminating deliverable for this course is a fully functional relational database management system demonstrating the integration of all major subsystems covered throughout the curriculum. The system must implement the following technical specifications:

### Required Functional Components

**Data Manipulation Layer:**

- Complete implementation of SQL data manipulation statements (INSERT, UPDATE, DELETE)
- Transaction-safe write operations with rollback capability
- Constraint validation and data integrity enforcement

**Query Processing Subsystem:**

- SQL query parser supporting SELECT statements with filtering predicates and projection operations
- Query execution engine implementing the iterator (Volcano) model
- Support for both sequential table scans and index-based access paths

**Join Processing Infrastructure:**

- Implementation of multiple join algorithms (nested loop join, hash join)
- Join predicate evaluation and result set generation
- Performance benchmarking across different join strategies

**Transaction Management System:**

- ACID-compliant transaction processing framework
- Concurrency control mechanism (MVCC or lock-based)
- Transaction isolation implementation (Read Committed or Snapshot Isolation)

**Storage Engine Architecture:**

- B+ tree index implementation supporting insertion, deletion, and range queries
- Page-based storage management with fixed-size page allocation
- Buffer pool manager with configurable replacement policy (LRU)
- Persistent storage through file-based data management

**Query Execution Infrastructure:**

- SQL lexical analyzer and parser
- Query plan generation and optimization framework
- Operator tree execution with appropriate iterator implementations

---

## Module Structure and Curriculum

### Module 1: Storage Foundations—Disk Management and Indexing Structures

**Duration:** Week 1 (5 instructional days)

**Module Learning Objectives:**

Upon completion of this module, students will be able to:

1. Explain the architectural principles of page-based storage systems and their role in database performance optimization
2. Implement a buffer pool manager incorporating Least Recently Used (LRU) page replacement policy
3. Design and construct B+ tree index structures supporting insertion, deletion, and search operations
4. Analyze the performance characteristics and trade-offs of different storage and indexing approaches

**Instructional Content:**

**Core Topics:**

- Page organization architecture and slotted page design patterns
- Buffer pool management strategies and cache replacement algorithms
- B+ tree structure: node organization, insertion algorithms, deletion with rebalancing
- Disk I/O optimization and access pattern analysis

**Laboratory Exercises:**

- Implementation of page management subsystem
- Development of buffer pool with eviction policy
- B+ tree construction with comprehensive test coverage

**Module Deliverable:**

Students will deliver a functional storage engine demonstrating the following capabilities:

- Persistent key-value storage with page-based organization
- Buffer pool management with configurable capacity
- B+ tree indexing supporting CRUD operations
- Performance benchmarks documenting insertion, search, and deletion latency

**Assessment Instruments:**

- Functionality testing: Comprehensive CRUD operation validation (40%)
- Performance benchmarking: Efficiency metrics compared against baseline (30%)
- Code quality review: Architecture, documentation, and idiomatic implementation (30%)

---

### Module 2: Query Execution—SQL Processing and Operator Implementation

**Duration:** Week 2 (5 instructional days)

**Module Learning Objectives:**

Upon completion of this module, students will be able to:

1. Construct a SQL parser capable of lexical analysis and syntactic parsing of data manipulation statements
2. Implement query execution operators using the iterator (Volcano) model architecture
3. Integrate query execution infrastructure with the storage layer developed in Module 1
4. Evaluate and select appropriate access paths for query execution (sequential scan vs. index scan)

**Instructional Content:**

**Core Topics:**

- SQL lexical analysis and token generation
- Context-free grammars and recursive descent parsing
- Operator tree construction and relational algebra representation
- Iterator model implementation: open, next, close protocol
- Access method selection: table scan and index scan operators
- Selection (filtering) and projection operators

**Laboratory Exercises:**

- SQL parser development with error handling
- Implementation of scan operators (sequential and index-based)
- Development of selection and projection operators
- Integration testing with storage subsystem

**Module Deliverable:**

Students will deliver a query execution engine with the following specifications:

- SQL parser supporting SELECT, INSERT, UPDATE, DELETE statements
- Complete integration with Module 1 storage engine
- Execution of queries with filtering predicates and column projection
- Comprehensive test suite validating correctness of query results

**Assessment Instruments:**

- Functional integration testing: Query execution correctness (50%)
- Parser robustness: Error handling and edge case management (20%)
- System integration: Seamless operation with storage layer (30%)

---

### Module 3: Transaction Management and Concurrency Control—Ensuring Data Integrity

**Duration:** Week 3 (5 instructional days)

**Module Learning Objectives:**

Upon completion of this module, students will be able to:

1. Explain ACID properties and their implementation mechanisms in transactional database systems
2. Implement Multi-Version Concurrency Control (MVCC) architecture supporting concurrent transaction execution
3. Design and construct transaction lifecycle management (BEGIN, COMMIT, ABORT/ROLLBACK)
4. Analyze transaction isolation levels and implement snapshot isolation semantics
5. Develop garbage collection mechanisms for obsolete tuple versions

**Instructional Content:**

**Core Topics:**

- ACID properties: Atomicity, Consistency, Isolation, Durability
- Transaction state management and lifecycle protocols
- Multi-Version Concurrency Control (MVCC) architecture
- Version chain management and tuple visibility determination
- Snapshot isolation implementation and transaction timestamp assignment
- Garbage collection strategies for version cleanup
- Concurrency anomalies: dirty reads, non-repeatable reads, phantom reads

**Laboratory Exercises:**

- Transaction manager implementation with state tracking
- MVCC version chain construction and management
- Visibility determination algorithms
- Concurrent workload simulation and testing
- Integration with query execution and storage subsystems

**Module Deliverable:**

Students will deliver a transaction processing subsystem with the following capabilities:

- Complete transaction lifecycle management (BEGIN, COMMIT, ROLLBACK)
- MVCC implementation supporting concurrent read and write transactions
- Snapshot isolation with correct visibility semantics
- Garbage collection mechanism for outdated tuple versions
- Demonstration of correct behavior under concurrent workload scenarios

**Assessment Instruments:**

- Correctness verification: Concurrent transaction execution validation (40%)
- Isolation testing: Verification of snapshot isolation semantics (30%)
- System integration: Compatibility with existing storage and execution layers (20%)
- Performance analysis: Transaction throughput under concurrent load (10%)

---

### Module 4: Query Optimization and Join Processing—Performance Enhancement

**Duration:** Week 4 (5 instructional days)

**Module Learning Objectives:**

Upon completion of this module, students will be able to:

1. Implement multiple join algorithms and analyze their computational complexity and memory requirements
2. Design and construct a cost-based query optimizer incorporating cardinality estimation
3. Evaluate query execution plans based on estimated cost metrics
4. Optimize query performance through appropriate join algorithm selection and access path determination
5. Synthesize all course components into an integrated, fully functional RDBMS

**Instructional Content:**

**Core Topics:**

- Join algorithms: nested loop join (simple and block-nested), hash join, sort-merge join
- Join ordering and optimization
- Cost model development: I/O cost, CPU cost, memory usage
- Cardinality estimation techniques
- Statistics collection and maintenance
- Query plan enumeration and selection algorithms
- Access path optimization and index selection
- Query rewriting and algebraic optimization

**Laboratory Exercises:**

- Implementation of nested loop join variants
- Development of hash join algorithm
- Cost estimation model construction
- Query optimizer framework development
- Comprehensive system integration and testing

**Module Deliverable:**

Students will deliver a complete database system incorporating all previous modules plus:

- Multiple join algorithm implementations (nested loop, hash join)
- Cost-based query optimizer with plan selection capability
- Query plan visualization and explanation functionality
- Comprehensive performance benchmarking comparing join strategies
- Final integrated system demonstration

**Assessment Instruments:**

- Technical report: Comparative analysis of join algorithm performance (25%)
- Query plan analysis: Demonstration of cost-based optimization (20%)
- Final project presentation: Oral defense of technical design decisions (25%)
- System demonstration: Complete RDBMS functionality showcase (30%)

---

## Core Technical Concepts and Theoretical Foundations

### Domain 1: Storage Layer Architecture

**Fundamental Principles:**

- Page-based storage organization: Fixed-size page allocation, page headers, and slot arrays
- Slotted page architecture: Variable-length record management and space utilization optimization
- B+ tree indexing structures: Node organization, key distribution, insertion with node splitting
- B+ tree deletion: Key removal, node merging, and tree rebalancing algorithms
- Buffer pool management: Page pinning, reference counting, and eviction strategies
- Least Recently Used (LRU) replacement policy: Implementation and performance characteristics
- Disk-based persistence: File organization, page addressing, and I/O optimization

**Learning Integration:** These concepts provide the foundation for understanding how databases bridge the performance gap between fast memory and slower persistent storage.

### Domain 2: Query Processing and Execution

**Fundamental Principles:**

- Lexical analysis: Tokenization of SQL statements and symbol recognition
- Syntactic parsing: Context-free grammar application and abstract syntax tree (AST) construction
- Relational algebra: Formal foundation for query operations (selection, projection, join)
- Iterator (Volcano) model: Pull-based query execution with open-next-close protocol
- Access methods: Sequential table scans versus index-based retrieval
- Operator implementation: Selection predicate evaluation, projection column filtering
- Query operator composition: Building execution pipelines through operator chaining

**Learning Integration:** Query processing bridges the gap between declarative SQL and imperative execution, requiring understanding of both formal language theory and practical systems implementation.

### Domain 3: Transaction Management and Concurrency

**Fundamental Principles:**

- ACID properties: Atomicity (all-or-nothing execution), Consistency (invariant preservation), Isolation (concurrent execution correctness), Durability (persistence guarantees)
- Transaction lifecycle: State transitions (BEGIN, ACTIVE, COMMITTED, ABORTED)
- Multi-Version Concurrency Control (MVCC): Version chain management and non-blocking read implementation
- Snapshot isolation: Transaction timestamp assignment and tuple visibility determination
- Visibility rules: Determining which tuple versions are visible to specific transactions
- Concurrency anomalies: Classification and prevention of dirty reads, non-repeatable reads, and phantom reads
- Garbage collection: Identification and removal of obsolete tuple versions

**Learning Integration:** Transaction management requires synthesizing concepts from concurrent systems, distributed systems, and data structure design to ensure correctness under concurrent access.

### Domain 4: Query Optimization and Join Processing

**Fundamental Principles:**

- Join algorithms: Nested loop join (simple, block-nested, index-nested), hash join, sort-merge join
- Algorithmic complexity: Time and space analysis for different join strategies
- Cost modeling: I/O cost estimation, CPU cost calculation, memory footprint analysis
- Cardinality estimation: Predicting result set sizes for cost calculation
- Query plan enumeration: Systematic exploration of execution alternatives
- Plan selection: Choosing optimal execution strategy based on estimated costs
- Access path optimization: Determining when to use indexes versus sequential scans
- Algebraic optimization: Query rewriting using equivalence rules

**Learning Integration:** Query optimization represents the culmination of the course, requiring integration of algorithm analysis, cost modeling, and empirical performance evaluation.

---

## Technical Implementation Stack

**Primary Programming Language:**

- Rust (recommended for its memory safety guarantees and systems programming capabilities)
- Alternative languages may be approved with instructor consent (C++, Go)

**Development Philosophy:**

- First-principles implementation: Most components constructed without external database libraries
- Minimal external dependencies: Only essential libraries for I/O and testing infrastructure permitted
- Educational focus: Implementation clarity prioritized over production optimization

**Required Development Tools:**

- Version control system (Git)
- Unit testing framework appropriate for chosen language
- Performance profiling and benchmarking tools
- Documentation generation utilities

---

## Course Prerequisites and Required Background

Students entering this course must demonstrate proficiency in the following areas:

### Programming Competency

**Systems Programming:**

- Advanced proficiency in at least one systems programming language (C, C++, Rust, Go)
- Understanding of manual memory management or ownership systems
- Experience with pointer manipulation and low-level data structure implementation
- Competency in debugging complex systems-level programs

**Software Engineering:**

- Experience with version control systems (Git)
- Unit testing and test-driven development practices
- Code documentation and technical writing skills

### Computer Science Fundamentals

**Data Structures (Required):**

- Comprehensive understanding of tree structures (binary search trees, AVL trees, B-trees)
- Hash table implementation including collision resolution strategies
- Linked lists, arrays, and dynamic data structures
- Algorithmic complexity analysis (Big-O notation)

**Database Fundamentals (Required):**

- Relational data model and relational algebra concepts
- SQL proficiency: SELECT, INSERT, UPDATE, DELETE statements
- Understanding of basic query execution and table operations
- Familiarity with primary keys, foreign keys, and basic constraints

**Operating Systems Concepts (Required):**

- File system operations and I/O mechanisms
- Memory management principles (virtual memory, paging)
- Process and thread concepts
- Basic understanding of concurrency primitives

### Recommended Preparation

**Suggested Preparatory Courses:**

- CS 201: Data Structures and Algorithms
- CS 301: Operating Systems
- CS 320: Database Systems (introductory)
- CS 250: Systems Programming

**Preparatory Reading:**

- Review of chosen programming language fundamentals
- Introduction to database system concepts
- Operating systems principles refresher

---

## Pedagogical Rationale and Educational Value

### Theoretical Foundation

This course implements a **constructivist learning paradigm** emphasizing knowledge construction through active implementation rather than passive knowledge acquisition. The pedagogical design recognizes that deep understanding of complex systems emerges through the process of building, testing, and refining implementations.

### Learning Through Construction

**Experiential Learning Model:**

The course follows Kolb's Experiential Learning Cycle, wherein students:

1. **Concrete Experience:** Implement database components hands-on
2. **Reflective Observation:** Analyze implementation behavior and performance
3. **Abstract Conceptualization:** Connect implementation experiences to theoretical principles
4. **Active Experimentation:** Optimize and enhance implementations based on insights

**Cognitive Load Management:**

The curriculum employs scaffolding techniques to manage cognitive complexity:

- Gradual progression from foundational to advanced concepts
- Incremental system construction reducing simultaneous cognitive demands
- Iterative refinement allowing mastery at each level before progression

### Academic Alignment

This course adapts pedagogical approaches and curricular content from premier academic database systems courses:

**Institutional Models:**

- Carnegie Mellon University (CMU 15-445/645): Database Systems implementation methodology
- Massachusetts Institute of Technology (6.830): Database Systems theoretical framework
- Stanford University (CS 346): Database System Implementation project structure

**Adaptation Strategy:**

The curriculum compresses semester-long content into an intensive four-week format through:

- Focused scope on core subsystems (storage, execution, transactions, optimization)
- Elimination of peripheral topics to maintain depth in essential areas
- Intensive laboratory sessions replacing distributed homework assignments
- Continuous integration of components rather than isolated module development

### Career Relevance and Professional Development

**Industry Applications:**

Skills developed in this course directly translate to professional database engineering roles:

- Understanding of production database architecture (PostgreSQL, MySQL, SQL Server)
- Systems programming competency applicable to infrastructure development
- Performance optimization skills transferable to data-intensive applications
- Deep understanding of concurrency and transaction processing

**Portfolio Development:**

The capstone project serves as a substantial portfolio artifact demonstrating:

- Large-scale systems programming capability
- Understanding of complex algorithmic systems
- Software engineering discipline and testing rigor
- Technical communication through documentation and presentation

---

## Daily Instructional Cycle

Each module follows a consistent five-day instructional pattern designed to optimize learning progression and skill development:

### Day 1: Problem Contextualization and Theoretical Foundation

**Instructional Activities:**

- Introduction of authentic database system challenge motivating the module content
- Presentation of theoretical foundations and algorithmic principles
- Analysis of real-world database system implementations addressing similar challenges
- Discussion of design trade-offs and alternative approaches

**Learning Mode:** Lecture with guided discussion, conceptual exercises

**Student Deliverable:** Conceptual understanding assessment, design proposal outline

### Day 2: Core Implementation Development

**Instructional Activities:**

- Detailed specification of implementation requirements
- Guided coding session addressing primary module functionality
- Code walkthrough and peer review activities
- Initial unit test development

**Learning Mode:** Laboratory session with instructor guidance

**Student Deliverable:** Functional implementation of core components with basic test coverage

### Day 3: System Integration and Comprehensive Testing

**Instructional Activities:**

- Integration of new components with existing system modules
- Development of integration test suite
- Debugging session addressing interface mismatches
- Performance baseline establishment

**Learning Mode:** Laboratory session with peer collaboration

**Student Deliverable:** Integrated system with passing test suite

### Day 4: Optimization and Performance Enhancement

**Instructional Activities:**

- Performance profiling and bottleneck identification
- Implementation of optimization strategies
- Comparative performance analysis
- Code refactoring for clarity and efficiency

**Learning Mode:** Independent laboratory work with consultation

**Student Deliverable:** Optimized implementation with performance documentation

### Day 5: Review, Assessment, and Forward Preparation

**Instructional Activities:**

- Comprehensive module review and concept synthesis
- Code quality assessment and refactoring
- Documentation completion and technical writing
- Preview of subsequent module requirements and connections

**Learning Mode:** Mixed (review session, individual work, assessment)

**Student Deliverable:** Completed module deliverable with documentation, preparation for next module

---

## Assessment Framework and Demonstrated Competencies

### Assessment Philosophy

This course employs **authentic assessment** methodologies emphasizing demonstration of competency through practical implementation rather than traditional examination. Assessment is continuous throughout the course, with formative feedback provided at each stage to support learning progression.

### Assessment Components and Weighting

| Assessment Component               | Weight | Assessment Type        | Timing        |
| ---------------------------------- | ------ | ---------------------- | ------------- |
| Module 1: Storage Engine           | 20%    | Implementation Project | End of Week 1 |
| Module 2: Query Execution Engine   | 20%    | Implementation Project | End of Week 2 |
| Module 3: Transaction Management   | 20%    | Implementation Project | End of Week 3 |
| Module 4: Query Optimization       | 20%    | Implementation Project | End of Week 4 |
| Final System Demonstration         | 10%    | Practical Assessment   | Week 4, Day 5 |
| Technical Presentation and Defense | 10%    | Oral Presentation      | Week 4, Day 5 |

---

#### Technical Implementation Competencies

1. **Storage System Design:** Ability to design and implement page-based storage systems with buffer pool management and B+ tree indexing
2. **Query Processing:** Competency in constructing SQL parsers and implementing query execution engines using iterator-based architectures
3. **Transaction Management:** Proficiency in implementing ACID-compliant transaction systems with concurrency control mechanisms
4. **Performance Optimization:** Capability to analyze, measure, and optimize database system performance through appropriate algorithm selection and cost modeling
5. **Architectural Analysis:** Ability to analyze and articulate design trade-offs in database system architecture, including storage layouts, indexing strategies, and concurrency control mechanisms
6. **Performance Analysis:** Competency in conducting empirical performance evaluation, interpreting profiling data, and identifying optimization opportunities
7. **Comparative Evaluation:** Ability to compare alternative implementation approaches using appropriate metrics and theoretical analysis

---

## Cumulative Project Architecture

### Integration Model

This course implements a **cumulative integration model** wherein each module builds upon and extends the previous module's implementation. This approach ensures:

- **Continuous Integration:** Regular verification that new components integrate correctly with existing functionality
- **Architectural Consistency:** Maintenance of coherent system architecture across modules
- **Incremental Complexity:** Gradual increase in system sophistication aligned with learning progression
- **Real-World Simulation:** Experience with evolution and maintenance of large codebases

### Module Integration Sequence

```
Module 1: Storage Layer Foundation
         ↓
Module 2: Query Execution Layer (integrated with Storage)
         ↓
Module 3: Transaction Management (integrated with Storage + Execution)
         ↓
Module 4: Query Optimization (complete system integration)
```

**Integration Points:**

- **Module 1→2:** Query execution operators interface with storage layer for data access
- **Module 2→3:** Transaction management wraps query execution with ACID guarantees
- **Module 3→4:** Query optimizer generates plans executed by transaction-aware query engine
- **Final System:** Complete vertical integration from SQL input through optimized, transactional execution to persistent storage

### Continuous Validation

Each integration point includes comprehensive testing:

- Interface contract validation
- Regression testing of previous functionality
- Performance regression detection
- Integration test expansion

---

## Distinctive Pedagogical Features

### Comprehensive Systems Implementation

Unlike survey courses focusing on theoretical understanding, this course requires **complete implementation** of all major database subsystems. Students gain deep understanding through construction rather than observation.

**Implementation Depth:**

- Storage layer: Complete page manager, buffer pool, and B+ tree from first principles
- Query processing: Full parser and execution engine without external SQL libraries
- Transaction management: MVCC implementation including visibility determination and garbage collection
- Query optimization: Cost model and plan selection with empirical validation

### Authentic Engineering Experience

**Real-World Architectural Alignment:**

Implementation patterns and algorithms mirror production database systems:

- PostgreSQL's MVCC and snapshot isolation approach
- Volcano iterator model used in many commercial databases
- B+ tree implementation following established database textbook algorithms
- Cost-based optimization reflecting real query optimizer design

**Professional Development:**

Students develop competencies directly applicable to database engineering careers:

- Large-scale systems programming in production-quality language (Rust)
- Performance optimization through profiling and algorithmic analysis
- Concurrent systems debugging and correctness verification
- Technical documentation and presentation skills

### Portfolio and Career Value

**Substantial Capstone Project:**

Upon completion, students possess a demonstrable, functional database system suitable for:

- Portfolio demonstration in technical interviews
- GitHub repository showcasing systems programming competency
- Foundation for graduate research or advanced independent study
- Basis for conference presentations or blog posts on database internals

**Differentiation in Technical Interviews:**

Experience implementing database internals provides significant advantage in interviews for:

- Database engineering positions (PostgreSQL, MySQL, MongoDB, etc.)
- Infrastructure and systems programming roles
- Distributed systems engineering
- Data platform and storage systems development

### Problem-Centered Pedagogical Approach

**Motivation-First Instruction:**

Each module begins with authentic database system challenges establishing clear motivation for learning:

- Why do databases use page-based storage? (Performance and organization)
- How do databases execute complex queries efficiently? (Optimization necessity)
- How do databases support concurrent users? (MVCC and isolation)
- How do databases choose execution strategies? (Cost-based optimization)

**Active Learning Emphasis:**

Learning occurs through active engagement rather than passive consumption:

- Implementation exercises rather than multiple-choice assessment
- Debugging challenges developing troubleshooting skills
- Performance optimization requiring analytical thinking
- Design decisions requiring evaluation of trade-offs

---

## Commencement

Students should begin with the first module:

**[→ Module 1: Storage Foundations](week_1/README.md)**
