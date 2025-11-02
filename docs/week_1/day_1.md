# Day 1: Storage Fundamentals—Page-Based Storage Architecture

## Module Objectives

Upon completion of this instructional session, students will be able to:

1. Analyze the limitations of sequential file storage approaches and articulate the motivation for page-based architecture
2. Design and implement page structures supporting variable-length records through slotted page organization
3. Develop disk I/O primitives for persistent page storage and retrieval
4. Construct data type serialization mechanisms for multiple primitive types
5. Implement basic data manipulation operations (insert, update, delete, query) on page-based storage

## Instructional Content Coverage

This session addresses the following technical domains:

- Rationale for fixed-size page allocation in database systems
- Page layout and organizational architecture
- Slotted page design for variable-length record management
- Disk I/O fundamentals and file-based persistence
- Data type representation and binary serialization
- Storage design decisions and architectural trade-offs

---

## Problem Contextualization: From Physical Records to Digital Storage

### Motivating Scenario

Consider a growing manufacturing company that has historically maintained employee records, inventory data, and transaction logs using physical paper-based systems. Each department maintains filing cabinets with thousands of documents:

**Employee Records Department:**

- Personnel files containing: Employee ID, Full Name, Department, Hire Date, Salary, Address
- Approximately 10,000 employee records
- Records vary in length due to variable address lengths and name lengths
- Frequent queries: "Find employee with ID 5432", "List all employees in Engineering department"

**Inventory Management:**

- Product records: Product ID, Name, Description, Category, Quantity, Price
- Approximately 50,000 product entries
- Regular updates when inventory levels change
- Need to support: "Find product by ID", "Update quantity for product", "Delete discontinued products"

**Access Patterns:**

The company observes the following data access characteristics:

- **Point queries:** Locate specific records by identifier (e.g., employee ID, product ID)
- **Range queries:** Retrieve records within ranges (e.g., employees hired between dates)
- **Updates:** Modify existing records (e.g., salary adjustments, inventory updates)
- **Deletions:** Remove obsolete records (e.g., terminated employees)

### Digital Transformation Challenge

Management decides to digitize these records, storing them in computer files for programmatic access. The fundamental question emerges:

**How should structured data be organized in computer files to enable efficient access, modification, and persistence?**

### Initial Exploration: Sequential Append-Only Storage

**Approach 1: Simple Sequential File**

Students are encouraged to first consider a straightforward approach: storing records sequentially in a single file.

**Storage Specification:**

```
File Structure (sequential_storage.dat):
Record 1: [ID][Name Length][Name][Email Length][Email]
Record 2: [ID][Name Length][Name][Email Length][Email]
Record 3: [ID][Name Length][Name][Email Length][Email]
Record N: [ID][Name Length][Name][Email Length][Email]
```

**Example Record Encoding:**

```
Record Format:
- Employee ID: 4 bytes (unsigned 32-bit integer)
- Name Length: 2 bytes (unsigned 16-bit integer)
- Name Data: Variable bytes (UTF-8 encoded string)
- Department Length: 2 bytes
- Department Data: Variable bytes
- Salary: 4 bytes (32-bit float)
```

**Operations Specification:**

```
INSERT(record):
  Seek to end of file
  Serialize record to bytes
  Append bytes to file

FIND_BY_ID(id):
  Seek to beginning of file
  While not end of file:
    Read record
    If record.id == id:
      Return record
  Return NOT_FOUND

UPDATE_BY_ID(id, new_record):
  result = FIND_BY_ID(id)
  If result == NOT_FOUND:
    Return ERROR
  Mark old record as deleted
  INSERT(new_record)

DELETE_BY_ID(id):
  result = FIND_BY_ID(id)
  Mark record as deleted
```

> **Practicum 0 (Coding Time):** Implement the naïve sequential file store, including `INSERT`, `FIND_BY_ID`, `UPDATE_BY_ID`, `DELETE_BY_ID`, and a simple record serializer/deserializer.

---

### Analytical Exercise: Limitations of Sequential Storage

Students should implement the sequential storage specification and conduct performance analysis:

**Performance Characteristics:**

**Test Case 1: Insertion Performance**

- Insert 10,000 employee records
- Measure: Time per insertion, total time
- Expected: O(1) append operations, good performance

**Test Case 2: Point Query Performance**

- Insert 10,000 records
- Perform 1,000 random ID lookups
- Measure: Average query latency
- Expected: O(n) linear scan, degrading performance as dataset grows

**Test Case 3: Update Performance**

- Insert 10,000 records
- Update 1,000 random records
- Measure: Average update latency
- Expected: O(n) search + O(1) append = O(n), poor performance

**Test Case 4: Space Utilization**

- Perform 1,000 updates (each marking old record deleted)
- Measure: File size growth
- Expected: Substantial wasted space from deleted records

### Critical Limitations Analysis

Through implementation and testing, students should identify the following fundamental limitations:

**1. Linear Search Complexity:**

Point queries require exhaustive scanning of the entire file. For a file containing n records, average search requires examining n/2 records.

Time Complexity: O(n)

- 1,000 records: ~500 comparisons average
- 10,000 records: ~5,000 comparisons average
- 1,000,000 records: ~500,000 comparisons average

**2. Variable-Length Record Management:**

Without fixed record sizes, determining record boundaries requires:

- Reading length prefixes for each field
- Sequential scanning to skip over records
- Inability to calculate record positions arithmetically

**3. Update Inefficiency:**

In-place updates impossible when new record size differs from old record size. Current approach requires:

- Locating original record (O(n) search)
- Marking as deleted
- Appending new version (O(1) append)
- Result: Fragmentation and wasted space

**4. Space Reclamation Challenge:**

Deleted records occupy space but cannot be easily reclaimed without:

- Complete file rewriting (expensive)
- Complex free space tracking mechanism
- Compaction operations disrupting concurrent access

**5. Memory Management Problem:**

Loading entire file into memory becomes infeasible as data grows:

- 1,000,000 records × 200 bytes average = 200 MB (manageable)
- 100,000,000 records × 200 bytes average = 20 GB (problematic)

**6. No Indexing Support:**

Sequential storage provides no mechanism for:

- Building searchable indexes
- Maintaining sorted order
- Supporting efficient range queries

### Conceptual Bridge: Learning from Physical Archive Organization

The sequential storage approach essentially digitizes paper records by transcribing each line sequentially into a file—a direct but inefficient translation that inherits the limitations of unstructured sequential access. However, examination of physical document management reveals a more sophisticated organizational strategy.

**Physical Archive Department Practices:**

Consider how archive departments organize large document collections. Rather than storing all documents in a single undifferentiated pile, archivists employ a **systematic partitioning strategy**:

- Documents are distributed across multiple filing cabinets
- Each cabinet is labeled with a document range (e.g., "Records 1-100", "Records 101-200", "Records 201-300")
- Cabinet capacity is standardized (e.g., 100 documents per cabinet)
- Locating document #157 requires checking only the cabinet labeled "101-200"

**Key Organizational Principles:**

This physical organization strategy offers several advantages:

1. **Bounded Search Space:** Locating a specific document requires searching only one cabinet rather than the entire archive
2. **Predictable Location:** Document N's location is calculable: Cabinet = ⌊(N-1) / 100⌋
3. **Manageable Units:** Operations (retrieval, insertion, removal) occur within bounded context (single cabinet)
4. **Parallel Access:** Multiple archivists can access different cabinets simultaneously

**Digital Analogy:**

Could database systems adopt an analogous approach? Rather than treating the storage file as an undifferentiated sequence, partition it into fixed-size units (analogous to cabinets) containing bounded numbers of records. This organizational strategy forms the conceptual foundation for **page-based storage architecture**.

### Transition: Introducing Page-Based Storage Architecture

The limitations of sequential storage, combined with insights from physical document organization, motivate a fundamental architectural shift. The critical realization: **systematic partitioning into fixed-size units enables efficient access patterns impossible with undifferentiated sequential storage**.

### Solution: Page-Based Storage Architecture

Database systems address the limitations of sequential storage through **page-based storage architecture**. This approach divides the storage file into fixed-size units called **pages** (also referred to as **blocks** in some literature).

**Fundamental Concept:**

A page represents the minimum addressable unit of storage in the database system. All I/O operations transfer entire pages between persistent storage and memory, never partial pages.

**Standard Page Sizes:**

- 4 KB (4,096 bytes) - Common for alignment with OS page size
- 8 KB (8,192 bytes) - PostgreSQL default
- 16 KB (16,384 bytes) - MySQL InnoDB default
- 32 KB (32,768 bytes) - Some specialized systems

### Rationale for Fixed-Size Page Allocation

**1. Predictable I/O Characteristics:**

Operating systems and storage devices read and write data in fixed-size blocks. Database page sizes align with these underlying block sizes to optimize I/O efficiency. A 4KB page aligns with typical OS memory page sizes and disk sector sizes.

**2. Arithmetic Address Calculation:**

Fixed-size pages enable direct position calculation without scanning:

```
byte_offset(page_id) = page_id × PAGE_SIZE # page_id can be analogous to the document number in the physical archive
```

Locating Page 1,000 in a 4KB page file:

```
offset = 1,000 × 4,096 = 4,096,000 bytes # locate how much bytes we want to skip, no sequential scanning required
actual_bytes = read_from_file(offset, PAGE_SIZE) # read the actual bytes from the file
```

This O(1) calculation contrasts sharply with O(n) scanning required for variable-length records.

**3. Efficient Data Navigation:**

Fixed-size pages provide a foundation for efficient data access patterns:

- Page identifiers serve as stable, persistent references to data locations
- Direct page addressing enables rapid data location without sequential scanning
- Multiple pages can be accessed independently, supporting concurrent operations
- Navigation between related data becomes straightforward through page ID references

### Page Structure Fundamentals

Each page consists of distinct regions serving specific purposes:

```
Logical Page Structure:

┌─────────────────────────────────────────┐  Byte 0
│         PAGE HEADER                     │
│    Metadata describing page             │
│  (page_id, low, high, record_count)     │
├─────────────────────────────────────────┤
│                                         │
│         CENTRAL FREE SPACE              │
│      (available for growth)             │
│                                         │
├─────────────────────────────────────────┤
│         SLOT DIRECTORY                  │
│   (record location pointers)            │
│      (grows downward)                   │
├─────────────────────────────────────────┤
│         RECORD DATA                     │
│   (actual record storage)               │
│      (grows upward)                     │
└─────────────────────────────────────────┘  Byte PAGE_SIZE-1
```

**Page Header Components:**

- `page_id`: Unique page identifier for addressing and retrieval
- `low`: Lower bound of key range stored in this page (e.g., records 1-100)
- `high`: Upper bound of key range stored in this page (e.g., records 1-100)
- `record_count`: Number of records currently stored in the page

The `low` and `high` values facilitate efficient page selection. For example, when searching for record with ID 157, the system can determine that it resides in the page marked with range 101-200, eliminating the need to search pages marked 1-100 or 201-300.

**Design Rationale:**

This structure accommodates variable-length records efficiently while maintaining:

- Fast record access through slot indirection
- Flexible space utilization
- Ability to compact records without changing external identifiers

### Slotted Page Architecture: Learning from Production Systems

Having established the page-based storage concept, we now address a critical challenge: **How can fixed-size pages efficiently store variable-length records?**

**Design Challenge:**

Consider storing employee records where:

- Employee names vary: "Li" (2 characters) vs. "Konstantinopolous" (18 characters)
- Addresses vary substantially in length
- Record sizes range from 50 bytes to 500 bytes

Direct sequential packing within pages inherits the same problems as file-level sequential storage.

**Production System Inspiration:**

Major production database systems (PostgreSQL, MySQL InnoDB, Oracle) have converged on a similar solution: **slotted page architecture**. This design pattern represents decades of engineering refinement addressing real-world storage challenges.

**Key Innovation: Indirection Through Slot Directory**

The fundamental insight: **separate logical record identifiers from physical storage locations**.

**Architectural Components:**

1. **Slot Directory:** Array of record descriptors (offset, length pairs)
2. **Record Storage Area:** Actual record data
3. **Free Space:** Unallocated space between directory and records

```
Page Layout (4096 bytes):

Byte 0:
┌─────────────────────────────────────────┐
│ Page Header (24 bytes)                  │
│  - page_id: u32                         │
│  - low: u32                             │
│  - high: u32                            │
│  - num_slots: u16                       │
│  - free_space_offset: u16               │
│  - free_space_end: u16                  │
└─────────────────────────────────────────┘
│ Slot 0: [offset: 4000, length: 32]      │
│ Slot 1: [offset: 3960, length: 40]      │
│ Slot 2: [offset: 3920, length: 40]      │
│              ...                        │
│         (Free Space)                    │
│              ...                        │
│         Tuple 2 (40 bytes)              │ ← offset 3920
│         Tuple 1 (40 bytes)              │ ← offset 3960
│         Tuple 0 (32 bytes)              │ ← offset 4000
└─────────────────────────────────────────┘
Byte 4095

Example: Page storing records with IDs 101-200
  - page_id: 5
  - low: 101 (minimum key/ID in this page)
  - high: 200 (maximum key/ID in this page)
  - num_slots: 3 (three records currently stored)
```

**Bidirectional Growth Mechanism:**

**Slot Directory Growth (Downward from Header):**

- Each new record allocation adds one slot entry to the directory
- Slot entries maintain fixed size (8 bytes: offset + length)
- Downward growth from fixed position enables arithmetic slot addressing
- Slot N located at byte position: `header_size + (N × slot_entry_size)`

**Record Storage Growth (Upward from Page End):**

- New records allocated from end of page, growing towards beginning
- Variable-length records packed consecutively
- Current allocation position tracked by `free_space_end` pointer
- Each allocation decrements `free_space_end` by record length

**Free Space Management:**

Free space exists between slot directory end (`free_space_start`) and record storage start (`free_space_end`).

Page capacity exhausted when: `free_space_start >= free_space_end`

**Operational Advantages:**

1. **Stable External References:** Slot IDs remain constant regardless of physical record relocation during compaction
2. **Efficient Defragmentation:** Records reorganized without updating external pointers; only internal slot entries modified
3. **Flexible Update Operations:** Record growth accommodated through relocation with slot pointer update, maintaining external identifier stability
4. **Space Reclamation:** Deletion marks slot invalid; space reclaimed during periodic compaction
5. **Direct Access:** Record N accessed via single indirection: read slot[N], then read record at slot[N].offset

### Record (Tuple) Structure and Data Type Representation

Having established page-level organization through slotted pages, we now address record-level structure. A **record** (also termed **tuple** or **row** in relational database literature) represents a single logical data entity containing multiple typed attributes (columns).

**Design Requirements:**

Record structures must satisfy several critical requirements:

1. **Multi-Column Support:** Records contain multiple attributes with distinct data types
2. **Variable-Length Handling:** Support for variable-length types (strings, binary data)
3. **NULL Value Representation:** Efficient encoding of absent values
4. **Self-Describing Format:** Records contain metadata enabling deserialization without external schema
5. **Space Efficiency:** Minimal overhead for metadata
6. **Random Access:** Direct access to specific columns without scanning entire record

**Record Architecture:**

```
Record Layout Structure:

┌────────────────────────────────────────────────┐
│ Record Header                                  │
│  record_size:        2 bytes (u16)             │
│  column_count:       2 bytes (u16)             │
│  null_bitmap:        ceil(column_count / 8)    │
├────────────────────────────────────────────────┤
│ Column Offset Table                            │
│  offset[0]:          2 bytes (u16)             │
│  offset[1]:          2 bytes (u16)             │
│  offset[2]:          2 bytes (u16)             │
│  ...                                           │
│  offset[n-1]:        2 bytes (u16)             │
├────────────────────────────────────────────────┤
│ Column Data Region                             │
│  Column 0 serialized data                      │
│  Column 1 serialized data                      │
│  Column 2 serialized data                      │
│  ...                                           │
│  Column n-1 serialized data                    │
└────────────────────────────────────────────────┘
```

**Component Rationale:**

**Record Header Components:**

- `record_size`: Total record byte length, enabling efficient record skipping during sequential scans
- `column_count`: Number of columns in record, supporting schema validation and evolution
- `null_bitmap`: Bit array where bit N indicates NULL status of column N (1 = NULL, 0 = present value)

**Column Offset Table:**

The offset table enables O(1) column access without scanning previous columns. For record with N columns, offset[K] contains byte offset (from record start) to column K's data.

This indirection overhead (2 bytes per column) trades space for access efficiency—critical for queries accessing subset of columns (projection operations).

**Column Data Region:**

Actual column values serialized according to type-specific encoding rules. Variable-length types store length prefixes; fixed-length types serialize directly.

### Data Type System and Binary Serialization

Day 1 focuses on implementing a foundational type system supporting common data types. We adopt a simplified type system inspired by SQLite's storage classes:

**Supported Data Types:**

1. **NULL:** Absent value
2. **INTEGER:** 32-bit signed integer
3. **REAL:** 64-bit IEEE floating-point
4. **TEXT:** UTF-8 encoded string
5. **BLOB:** Binary large object (arbitrary byte sequence)

**Type Encoding Specification:**

Each column value encodes as: `[type_tag (1 byte)][type-specific data]`

**Type Tag Values:**

```
NULL_TYPE    = 0x00
INTEGER_TYPE = 0x01
REAL_TYPE    = 0x02
TEXT_TYPE    = 0x03
BLOB_TYPE    = 0x04
```

**NULL Encoding:**

```
Format: [0x00]
Size: 1 byte total
Note: NULL values also recorded in record header null_bitmap for efficiency
```

**INTEGER Encoding:**

```
Format: [0x01][value (4 bytes, little-endian)]
Size: 5 bytes total
Example: Integer 42 → [0x01, 0x2A, 0x00, 0x00, 0x00]
```

**REAL Encoding:**

```
Format: [0x02][value (8 bytes, IEEE 754 double, little-endian)]
Size: 9 bytes total
Example: 3.14159 → [0x02, followed by 8-byte IEEE 754 encoding]
```

**TEXT Encoding:**

```
Format: [0x03][length (2 bytes)][UTF-8 data (variable)]
Size: 3 + length bytes
Example: "Hello" → [0x03, 0x05, 0x00, 'H', 'e', 'l', 'l', 'o']
Length limit: 65,535 bytes (u16 maximum)
```

**BLOB Encoding:**

```
Format: [0x04][length (2 bytes)][binary data (variable)]
Size: 3 + length bytes
Length limit: 65,535 bytes (u16 maximum)
```

**Example: Complete Record Serialization**

Consider employee record: `(id: 101, name: "Alice", salary: 75000.50, department: "Engineering")`

```
Record Structure:

Header:
  record_size:    94 bytes (total)
  column_count:   4
  null_bitmap:    0x00 (no nulls, 1 byte for 4 columns)

Offset Table:
  offset[0]:      11  (to id field)
  offset[1]:      16  (to name field)
  offset[2]:      25  (to salary field)
  offset[3]:      34  (to department field)

Data:
  Column 0 (id):         [0x01, 0x65, 0x00, 0x00, 0x00]  (INTEGER: 101)
  Column 1 (name):       [0x03, 0x05, 0x00, 'A','l','i','c','e']  (TEXT: "Alice")
  Column 2 (salary):     [0x02, <8 bytes IEEE 754>]  (REAL: 75000.50)
  Column 3 (department): [0x03, 0x0B, 0x00, 'E','n','g','i','n','e','e','r','i','n','g']
```

**Serialization and Deserialization Algorithms:**

```
SERIALIZE_RECORD(columns):
  // Calculate sizes
  header_size = 4 + ceil(column_count / 8)
  offset_table_size = column_count × 2

  // Serialize columns to determine offsets and sizes
  serialized_columns = []
  current_offset = header_size + offset_table_size
  offsets = []

  For each column in columns:
    serialized = SERIALIZE_COLUMN(column)
    serialized_columns.append(serialized)
    offsets.append(current_offset)
    current_offset += sizeof(serialized)

  record_size = current_offset

  // Build record
  buffer = new byte array[record_size]

  // Write header
  write_u16(buffer, 0, record_size)
  write_u16(buffer, 2, column_count)
  write_null_bitmap(buffer, 4, null_bitmap)

  // Write offset table
  offset_pos = header_size
  For each offset in offsets:
    write_u16(buffer, offset_pos, offset)
    offset_pos += 2

  // Write column data
  data_pos = header_size + offset_table_size
  For each serialized_col in serialized_columns:
    write_bytes(buffer, data_pos, serialized_col)
    data_pos += sizeof(serialized_col)

  Return buffer

DESERIALIZE_RECORD(buffer):
  // Read header
  record_size = read_u16(buffer, 0)
  column_count = read_u16(buffer, 2)
  null_bitmap = read_null_bitmap(buffer, 4, column_count)

  header_size = 4 + ceil(column_count / 8)

  // Read offset table
  offsets = []
  For i = 0 to column_count-1:
    offset = read_u16(buffer, header_size + i × 2)
    offsets.append(offset)

  // Read columns
  columns = []
  For i = 0 to column_count-1:
    If null_bitmap[i]:
      columns.append(NULL)
    Else:
      offset = offsets[i]
      column_data = DESERIALIZE_COLUMN(buffer, offset)
      columns.append(column_data)

  Return columns

SERIALIZE_COLUMN(column):
  Match column.type:
    NULL:
      Return [0x00]
    INTEGER:
      Return [0x01] + integer_to_le_bytes(column.value)
    REAL:
      Return [0x02] + double_to_le_bytes(column.value)
    TEXT:
      utf8_bytes = encode_utf8(column.value)
      length = sizeof(utf8_bytes)
      Return [0x03] + u16_to_le_bytes(length) + utf8_bytes
    BLOB:
      length = sizeof(column.value)
      Return [0x04] + u16_to_le_bytes(length) + column.value

DESERIALIZE_COLUMN(buffer, offset):
  type_tag = buffer[offset]

  Match type_tag:
    0x00:  // NULL
      Return NULL
    0x01:  // INTEGER
      value = read_i32_le(buffer, offset + 1)
      Return INTEGER(value)
    0x02:  // REAL
      value = read_f64_le(buffer, offset + 1)
      Return REAL(value)
    0x03:  // TEXT
      length = read_u16_le(buffer, offset + 1)
      utf8_bytes = read_bytes(buffer, offset + 3, length)
      text = decode_utf8(utf8_bytes)
      Return TEXT(text)
    0x04:  // BLOB
      length = read_u16_le(buffer, offset + 1)
      blob_data = read_bytes(buffer, offset + 3, length)
      Return BLOB(blob_data)
```

### Implementation Sequence: Building from Bottom Up

Before proceeding to the persistence layer, students should first establish the foundational data structures and serialization mechanisms. The implementation follows a logical progression from record-level operations to page-level management, and finally to disk-based persistence.

**Recommended Implementation Order:**

1. **Data Type System and Serialization:** Implement the type enumeration (NULL, INTEGER, REAL, TEXT, BLOB) along with serialization and deserialization functions for each type. This provides the basic building blocks for record construction.

2. **Record Structure:** Develop the record serialization mechanism, including header construction, offset table generation, and column data encoding. Verify correct operation through serialization round-trip tests.

3. **Page Structure:** Implement the slotted page architecture with slot directory management, free space tracking, and record insertion/retrieval operations. Ensure proper handling of variable-length records and space allocation.

4. **Disk Manager:** With in-memory page operations verified, proceed to implement the persistence layer for durable storage.

> **Practicum 1 (Implementation Exercise):** Implement the data type system, record serialization, and page structure components. Develop comprehensive unit tests verifying serialization correctness, page space management, and record operations before proceeding to disk I/O.

--- LUNCH BREAK ---

### Disk Manager: File-Based Persistence Layer

[
   introduce first disk manager as more high level manager, eg: how if page is full, how we can coordinate to create another page?
   who store / bookeeping the all pages data, yes we already grouping data info 1 - 100, 101 - 200 but there is a space where we store that
   information, and that's it disk manager, the layer that will coordinate on multi page such as, allocating page, delete page where empty, etc
]

The **Disk Manager** provides the lowest-level abstraction over file I/O operations, managing page-level read and write operations to persistent storage.

**Architectural Responsibilities:**

1. **Page Allocation:** Assigning unique page identifiers for new pages
2. **Page I/O:** Reading pages from disk into memory and writing pages from memory to disk
3. **File Management:** Creating, opening, and maintaining database files
4. **Address Translation:** Converting page IDs to file byte offsets

**Disk Manager Interface Specification:**

```
DiskManager Operations:

CREATE_DATABASE(filepath):
  Create new database file
  Initialize file with header page (Page 0)
  Return success/error

OPEN_DATABASE(filepath):
  Open existing database file
  Verify file integrity
  Return DiskManager instance

ALLOCATE_PAGE():
  Generate new unique PageID
  Extend file if necessary
  Return PageID

READ_PAGE(page_id):
  Calculate offset = page_id × PAGE_SIZE
  Seek to offset in file
  Read PAGE_SIZE bytes
  Deserialize into Page structure
  Return Page

WRITE_PAGE(page_id, page):
  Calculate offset = page_id × PAGE_SIZE
  Seek to offset in file
  Serialize page to bytes
  Write PAGE_SIZE bytes
  Sync to disk (flush)
  Return success/error

GET_PAGE_COUNT():
  Return total number of allocated pages
```

**File Organization:**

Database stored as single file with pages sequentially arranged:

```
File Structure:

Byte Offset 0:        Page 0 (Header Page - Database Metadata)
Byte Offset 4,096:    Page 1 (Data Page)
Byte Offset 8,192:    Page 2 (Data Page)
Byte Offset 12,288:   Page 3 (Data Page)
...
Byte Offset N×4,096:  Page N (Data Page)
```

**Page Addressing:**

Page ID N located at byte offset: `N × PAGE_SIZE`

This arithmetic addressing enables O(1) seek operations without maintaining separate index structures.

**Header Page (Page 0) Structure:**

```
Header Page Layout:

┌────────────────────────────────────────┐
│ Magic Number (4 bytes)                 │  File format identifier
│ Page Size (4 bytes)                    │  Configured page size
│ Page Count (4 bytes)                   │  Total allocated pages
│ Next Page ID (4 bytes)                 │  Next available page ID
│ Reserved (4080 bytes)                  │  Future metadata
└────────────────────────────────────────┘
```

### Critical Storage Concepts

**Page Identifiers as Persistent Pointers:**

Unlike in-memory pointers (which contain transient memory addresses), database systems require stable, persistent references to data locations. **Page IDs** serve as persistent pointers:

```
Record Pointer Structure:

page_id:  4 bytes (u32) - Which page contains the record
slot_id:  2 bytes (u16) - Which slot within the page
```

This tuple uniquely identifies any record in the database, remaining valid across system restarts.

**Dirty Page Tracking:**

Modified pages in memory must eventually persist to disk. The system marks modified pages as **dirty**:

```
Page Metadata:

is_dirty:  boolean flag
  - Set to true when page modified in memory
  - Cleared after successful write to disk
  - Only dirty pages require write-back
```

This optimization avoids unnecessary disk writes for read-only pages.

### Implementation Design Decisions for Day 1

**Scope Constraints:**

1. **Page Size:** 4,096 bytes (4KB) - Standard OS page size alignment
2. **File Organization:** Single file per database for simplicity
3. **Record Structure:** Multi-column records with type system (INTEGER, REAL, TEXT, BLOB, NULL)
4. **Concurrency:** Single-threaded operation (concurrency addressed in Module 3)
5. **Durability:** Synchronous writes (write-ahead logging addressed in Module 3)
6. **Focus:** Page I/O, serialization, basic CRUD operations on single-page storage

### Real-World Examples

**PostgreSQL**:

- 8KB pages (called "blocks")
- Slotted page layout with line pointers
- Page header includes LSN for crash recovery

**MySQL InnoDB**:

- 16KB pages
- B+ tree clustered index (data in leaf pages)
- Doublewrite buffer for atomic page writes

**SQLite**:

- Configurable page size (512B to 64KB, default 4KB)
- B-tree for tables and indexes
- Single file database

---

## Day 1 Learning Outcomes and Deliverables

Upon successful completion of Day 1 exercises, students will have developed:

### Technical Deliverables

**1. Complete Page Structure Implementation**

- Slotted page structure with header, slot directory, and record storage
- Page serialization functions converting in-memory structures to byte arrays
- Page deserialization functions reconstructing structures from byte arrays
- Space management algorithms (allocation, compaction)

**2. Data Type System**

- Type enumeration supporting NULL, INTEGER, REAL, TEXT, BLOB
- Column serialization functions for each supported type
- Column deserialization functions with type-safe parsing
- Record serialization combining multiple columns with header and offset table

**3. Disk Manager Implementation**

- Database file creation and initialization
- Page allocation with unique ID generation
- Page read operations with proper file seeking and deserialization
- Page write operations with serialization and disk synchronization
- Header page management for database metadata

**4. CRUD Operations on Page-Based Storage**

- **INSERT:** Add new records to pages with automatic slot allocation
- **SELECT:** Retrieve records by key, supporting projection (column subset selection) and filtering (predicate evaluation)
- **UPDATE:** Modify existing records, handling size changes through relocation
- **DELETE:** Mark records deleted and support space reclamation

**5. Comprehensive Test Suite**

- Page serialization round-trip tests
- Multi-type record serialization tests
- Disk persistence tests (write, close, reopen, read)
- CRUD operation correctness tests
- Edge case handling (page full, record not found, etc.)

### Conceptual Understanding

Students will demonstrate comprehension of:

1. **Page-Based Storage Rationale:** Articulate performance and organizational advantages of fixed-size pages over sequential storage
2. **Slotted Page Architecture:** Explain indirection benefits and bidirectional growth strategy
3. **Binary Serialization:** Describe type encoding, endianness considerations, and deserialization challenges
4. **Persistent Identifiers:** Distinguish between transient memory pointers and stable page IDs
5. **Storage Trade-offs:** Analyze space overhead versus access efficiency in slot directory design

---

## Implementation Project: Page-Based Storage with CRUD Operations

### Project Objectives

Students will implement a complete page-based storage system supporting full CRUD operations (Create, Read, Update, Delete) on multi-column records with type support.

### System Components Architecture

The implementation consists of four primary components organized in a layered architecture:

```
Component Hierarchy:

┌─────────────────────────────────────────┐
│   Storage API (CRUD Operations)        │
│   insert, select, update, delete       │
├─────────────────────────────────────────┤
│   Page Manager                          │
│   record allocation, slot management   │
├─────────────────────────────────────────┤
│   Serialization Layer                   │
│   record/column encoding/decoding       │
├─────────────────────────────────────────┤
│   Disk Manager                          │
│   page I/O, file management             │
└─────────────────────────────────────────┘
```

### Component Specifications

**Component 1: Data Type System**

```
DataType Enumeration:
  NULL
  INTEGER(value: i32)
  REAL(value: f64)
  TEXT(value: String)
  BLOB(value: Vec<u8>)

Functions:
  serialize_column(column: DataType) -> Vec<u8>
  deserialize_column(bytes: &[u8], offset: usize) -> (DataType, bytes_read)
  get_type_tag(column: &DataType) -> u8
  calculate_size(column: &DataType) -> usize
```

**Component 2: Record Structure**

```
Record Structure:
  columns: Vec<DataType>

Functions:
  serialize_record(columns: &[DataType]) -> Vec<u8>
    // Returns complete record bytes with header, offsets, and data

  deserialize_record(bytes: &[u8]) -> Record
    // Reconstructs Record from byte sequence

  get_column(record: &Record, index: usize) -> Option<&DataType>
    // Retrieves specific column by index

  record_size(columns: &[DataType]) -> usize
    // Calculates total serialized size
```

**Component 3: Page Structure (Slotted Page)**

```
Page Structure:
  page_id: PageId (u32)
  header: PageHeader
  slots: Vec<Slot>
  data: [u8; PAGE_SIZE]
  is_dirty: bool

PageHeader:
  page_id: u32
  num_records: u16
  free_space_start: u16
  free_space_end: u16

Slot:
  offset: u16
  length: u16

Functions:
  new_page(page_id: PageId) -> Page

  insert_record(page: &mut Page, record_bytes: &[u8]) -> Result<SlotId>
    // Allocates slot and space, returns slot ID

  get_record(page: &Page, slot_id: SlotId) -> Result<Vec<u8>>
    // Retrieves record bytes by slot ID

  update_record(page: &mut Page, slot_id: SlotId, new_record: &[u8]) -> Result<()>
    // Updates record, may relocate if size changed

  delete_record(page: &mut Page, slot_id: SlotId) -> Result<()>
    // Marks slot as deleted

  compact_page(page: &mut Page) -> Result<()>
    // Reclaims space from deleted records

  has_space(page: &Page, required_size: usize) -> bool
    // Checks if page can accommodate new record

  serialize_page(page: &Page) -> [u8; PAGE_SIZE]
    // Converts page to byte array for disk storage

  deserialize_page(bytes: &[u8; PAGE_SIZE]) -> Page
    // Reconstructs page from byte array
```

**Component 4: Disk Manager**

```
DiskManager Structure:
  file_handle: File
  file_path: String
  page_count: u32

Functions:
  create(path: &str) -> Result<DiskManager>
    // Creates new database file with header page

  open(path: &str) -> Result<DiskManager>
    // Opens existing database file

  allocate_page() -> PageId
    // Returns new unique page ID

  read_page(page_id: PageId) -> Result<Page>
    // Reads page from disk, deserializes

  write_page(page: &Page) -> Result<()>
    // Serializes and writes page to disk

  flush() -> Result<()>
    // Forces all buffered writes to disk

  close() -> Result<()>
    // Closes file handle
```

**Component 5: Storage API (CRUD Operations)**

```
Storage Structure:
  disk_manager: DiskManager
  pages: HashMap<PageId, Page>  // Simple in-memory cache
  current_page: PageId          // Page for new insertions

Functions:
  create_storage(path: &str) -> Result<Storage>
    // Initializes new storage

  insert(record: &[DataType]) -> Result<RecordId>
    // Inserts record, returns (page_id, slot_id)
    // Algorithm:
    //   1. Serialize record
    //   2. Find page with sufficient space
    //   3. Insert into page
    //   4. Mark page dirty
    //   5. Return RecordId

  select(record_id: RecordId) -> Result<Vec<DataType>>
    // Retrieves record by ID
    // Algorithm:
    //   1. Load page (from cache or disk)
    //   2. Get record bytes from slot
    //   3. Deserialize record
    //   4. Return columns

  select_where(predicate: Fn(&Record) -> bool) -> Vec<Record>
    // Scans pages, returns matching records

  project(record_id: RecordId, column_indices: &[usize]) -> Result<Vec<DataType>>
    // Returns subset of columns

  update(record_id: RecordId, new_record: &[DataType]) -> Result<()>
    // Updates existing record
    // Algorithm:
    //   1. Load page
    //   2. Serialize new record
    //   3. Update in page (may relocate)
    //   4. Mark page dirty

  delete(record_id: RecordId) -> Result<()>
    // Deletes record
    // Algorithm:
    //   1. Load page
    //   2. Delete slot
    //   3. Mark page dirty

  flush_all() -> Result<()>
    // Writes all dirty pages to disk
```

### Project Directory Structure

```
project/
├── src/
│   ├── storage/
│   │   ├── mod.rs              # Module declarations
│   │   ├── types.rs            # DataType enum and serialization
│   │   ├── record.rs           # Record structure and operations
│   │   ├── page.rs             # Slotted page implementation
│   │   ├── disk_manager.rs     # Disk I/O layer
│   │   └── storage.rs          # CRUD API
│   └── main.rs                 # Example usage
├── tests/
│   ├── test_types.rs           # Data type serialization tests
│   ├── test_record.rs          # Record operation tests
│   ├── test_page.rs            # Page operation tests
│   ├── test_disk.rs            # Disk I/O tests
│   └── test_crud.rs            # Integration tests for CRUD
└── Cargo.toml                  # Dependencies
```

### Constants and Configuration

```
Global Constants:
  PAGE_SIZE: usize = 4096
  PAGE_HEADER_SIZE: usize = 32
  SLOT_SIZE: usize = 8
  MAX_RECORD_SIZE: usize = PAGE_SIZE - PAGE_HEADER_SIZE - SLOT_SIZE
  MAGIC_NUMBER: u32 = 0xDEADBEEF
  INVALID_PAGE_ID: PageId = u32::MAX
  INVALID_SLOT_ID: SlotId = u16::MAX
```

### Comprehensive Testing Strategy

Testing follows a bottom-up approach, verifying each component independently before integration testing.

**Test Suite 1: Data Type Serialization**

```
TEST: NULL_serialization
  column = NULL
  bytes = serialize_column(column)
  ASSERT bytes == [0x00]
  restored = deserialize_column(bytes)
  ASSERT restored == NULL

TEST: INTEGER_serialization
  column = INTEGER(42)
  bytes = serialize_column(column)
  ASSERT bytes[0] == 0x01
  ASSERT bytes[1..5] == [42, 0, 0, 0]  // little-endian
  restored = deserialize_column(bytes)
  ASSERT restored == INTEGER(42)

TEST: TEXT_serialization
  column = TEXT("Hello")
  bytes = serialize_column(column)
  ASSERT bytes[0] == 0x03
  ASSERT bytes[1..3] == [5, 0]  // length = 5
  ASSERT bytes[3..8] == ['H', 'e', 'l', 'l', 'o']
  restored = deserialize_column(bytes)
  ASSERT restored == TEXT("Hello")

TEST: multi_type_record_serialization
  record = [INTEGER(101), TEXT("Alice"), REAL(75000.50)]
  bytes = serialize_record(record)
  restored = deserialize_record(bytes)
  ASSERT restored[0] == INTEGER(101)
  ASSERT restored[1] == TEXT("Alice")
  ASSERT restored[2] == REAL(75000.50)
```

**Test Suite 2: Page Operations**

```
TEST: page_initialization
  page = new_page(1)
  ASSERT page.page_id == 1
  ASSERT page.num_records == 0
  ASSERT page.has_space(1000) == true

TEST: insert_single_record
  page = new_page(1)
  record_bytes = create_test_record()
  slot_id = insert_record(page, record_bytes)
  ASSERT slot_id == 0
  ASSERT page.num_records == 1
  retrieved = get_record(page, slot_id)
  ASSERT retrieved == record_bytes

TEST: insert_multiple_records
  page = new_page(1)
  For i in 0..10:
    record = create_test_record(i)
    slot_id = insert_record(page, record)
    ASSERT slot_id == i
  ASSERT page.num_records == 10
  For i in 0..10:
    retrieved = get_record(page, i)
    ASSERT retrieved == create_test_record(i)

TEST: update_record
  page = new_page(1)
  original_record = create_test_record("original")
  slot_id = insert_record(page, original_record)
  new_record = create_test_record("updated")
  update_record(page, slot_id, new_record)
  retrieved = get_record(page, slot_id)
  ASSERT retrieved == new_record

TEST: delete_record
  page = new_page(1)
  record = create_test_record()
  slot_id = insert_record(page, record)
  delete_record(page, slot_id)
  result = get_record(page, slot_id)
  ASSERT result == ERROR_RECORD_DELETED

TEST: page_capacity_limit
  page = new_page(1)
  large_record = create_large_record(3000)
  insert_record(page, large_record)
  result = insert_record(page, large_record)
  ASSERT result == ERROR_PAGE_FULL
```

**Test Suite 3: Disk I/O**

```
TEST: create_database
  Remove test file if exists
  disk_mgr = DiskManager::create("test.db")
  ASSERT file "test.db" exists
  ASSERT disk_mgr.page_count >= 1  // At least header page

TEST: page_write_and_read
  disk_mgr = DiskManager::create("test.db")
  page = create_test_page(data: "test data")
  write_page(disk_mgr, page)
  flush(disk_mgr)
  close(disk_mgr)

  disk_mgr = DiskManager::open("test.db")
  retrieved = read_page(disk_mgr, page.page_id)
  ASSERT retrieved.page_id == page.page_id
  ASSERT retrieved.data == page.data

TEST: multiple_page_persistence
  disk_mgr = DiskManager::create("test.db")
  page_ids = []
  For i in 0..100:
    page = create_test_page(i)
    write_page(disk_mgr, page)
    page_ids.append(page.page_id)
  flush(disk_mgr)
  close(disk_mgr)

  disk_mgr = DiskManager::open("test.db")
  For i in 0..100:
    page = read_page(disk_mgr, page_ids[i])
    ASSERT page contains expected data for i
```

**Test Suite 4: CRUD Integration Tests**

```
TEST: insert_and_select
  storage = create_storage("test.db")
  record = [INTEGER(1), TEXT("Alice"), REAL(50000.0)]
  record_id = insert(storage, record)
  retrieved = select(storage, record_id)
  ASSERT retrieved == record

TEST: update_record_crud
  storage = create_storage("test.db")
  original = [INTEGER(1), TEXT("Alice"), REAL(50000.0)]
  record_id = insert(storage, original)
  updated = [INTEGER(1), TEXT("Alice"), REAL(60000.0)]
  update(storage, record_id, updated)
  retrieved = select(storage, record_id)
  ASSERT retrieved == updated

TEST: delete_record_crud
  storage = create_storage("test.db")
  record = [INTEGER(1), TEXT("Alice")]
  record_id = insert(storage, record)
  delete(storage, record_id)
  result = select(storage, record_id)
  ASSERT result == ERROR_NOT_FOUND

TEST: projection
  storage = create_storage("test.db")
  record = [INTEGER(1), TEXT("Alice"), REAL(50000.0), TEXT("Engineering")]
  record_id = insert(storage, record)
  columns = project(storage, record_id, [0, 2])  // ID and salary only
  ASSERT columns == [INTEGER(1), REAL(50000.0)]

TEST: filtering_scan
  storage = create_storage("test.db")
  insert_multiple_employee_records(storage)
  results = select_where(storage, |r| r[2].as_real() > 60000.0)
  For record in results:
    ASSERT record[2].as_real() > 60000.0

TEST: persistence_across_restarts
  storage = create_storage("test.db")
  record = [INTEGER(42), TEXT("Test")]
  record_id = insert(storage, record)
  flush_all(storage)
  close(storage)

  storage = open_storage("test.db")
  retrieved = select(storage, record_id)
  ASSERT retrieved == record
```

### Key Implementation Details

#### Serialization Example (Pseudo-code)

```
impl Page {
    fn to_bytes(&self) -> [u8; PAGE_SIZE] {
        let mut buffer = [0u8; PAGE_SIZE];

        // Write page ID (4 bytes)
        buffer[0..4].copy_from_slice(&self.page_id.to_le_bytes());

        // Write rest of data
        buffer[4..].copy_from_slice(&self.data);

        buffer
    }

    fn from_bytes(bytes: &[u8; PAGE_SIZE]) -> Page {
        // Read page ID
        let page_id = u32::from_le_bytes([bytes[0], bytes[1], bytes[2], bytes[3]]);

        // Read rest of data
        let mut data = [0u8; PAGE_SIZE - 4];
        data.copy_from_slice(&bytes[4..]);

        Page { page_id, data, is_dirty: false }
    }
}
```

#### Disk Manager File Operations (Pseudo-code)

```
impl DiskManager {
    fn read_page(&mut self, page_id: u32) -> Result<Page> {
        // Seek to correct position
        let offset = page_id as u64 * PAGE_SIZE as u64;
        self.file.seek(SeekFrom::Start(offset))?;

        // Read exactly PAGE_SIZE bytes
        let mut buffer = [0u8; PAGE_SIZE];
        self.file.read_exact(&mut buffer)?;

        // Deserialize into Page
        Ok(Page::from_bytes(&buffer))
    }

    fn write_page(&mut self, page: &Page) -> Result<()> {
        // Seek to correct position
        let offset = page.page_id as u64 * PAGE_SIZE as u64;
        self.file.seek(SeekFrom::Start(offset))?;

        // Serialize and write
        let bytes = page.to_bytes();
        self.file.write_all(&bytes)?;
        self.file.sync_all()?;  // Force flush to disk

        Ok(())
    }
}
```

---

## Additional Knowledge: Storage Reliability Considerations

### Atomic Write Guarantees and Data Integrity

While Day 1 focuses on fundamental storage operations, understanding the reliability properties of page-based storage provides important context for future modules on transaction management and crash recovery.

**Page-Level Atomicity:**

Most modern filesystems and storage devices provide atomic write guarantees for specific block sizes, typically 4KB or 512 bytes. An atomic write operation exhibits the following property:

- The write operation either completes entirely, persisting all bytes to stable storage
- Or the write operation fails completely, leaving the previous page contents intact
- Partial writes (where only some bytes of the page are modified) do not occur

This atomicity property becomes critical for database correctness. Consider a scenario where a page write operation is interrupted by a power failure. Without atomic writes, the page might contain a mixture of old and new data, creating an inconsistent state that corrupts data structures.

**Alignment with Page Size Selection:**

The choice of 4KB as a standard database page size aligns strategically with filesystem and hardware guarantees:

- Traditional hard disk drives (HDDs) organize data in 512-byte sectors
- Solid-state drives (SSDs) typically use 4KB pages internally
- Operating system virtual memory systems employ 4KB pages
- Many filesystems guarantee atomic writes for 4KB-aligned blocks

By selecting 4KB pages and ensuring proper alignment, database systems leverage underlying hardware atomicity guarantees without requiring additional protection mechanisms.

**Implications for Database Design:**

This atomic write property influences several design decisions explored in subsequent modules:

1. **Write-Ahead Logging (Module 3):** Transaction logs can rely on page-level atomicity for certain recovery protocols
2. **Checkpointing:** Periodic page flushes benefit from atomic write guarantees
3. **Metadata Updates:** Critical metadata structures sized to fit within single pages gain atomicity

**Limitations and Advanced Techniques:**

It is important to note that atomic write guarantees are not universal:

- Larger pages (8KB, 16KB) may not have atomic write guarantees on all systems
- Some filesystems and storage configurations do not provide atomicity even for 4KB blocks
- Network-attached storage and distributed systems introduce additional complexity

Production database systems employ sophisticated techniques to ensure correctness regardless of underlying atomicity guarantees:

- **Doublewrite Buffers (MySQL InnoDB):** Write pages to a sequential log before updating in-place locations
- **Checksums:** Detect partial writes through page-level integrity verification
- **Write-Ahead Logging:** Ensure recoverability independent of page write atomicity

These advanced reliability mechanisms will be addressed comprehensively in Module 3 when implementing transaction support and crash recovery.
