# Day 1: Storage Fundamentals

## Title: Page-Based Storage Architecture

### What We Cover in This Section

- Why databases use fixed-size pages
- Page layout and structure design
- Slotted page architecture for variable-length records
- Disk I/O fundamentals
- Storage design decisions and trade-offs

---

## Description

### The Problem: How Do We Store Data on Disk?

Imagine you need to store user records like this:

```
User { id: 1, name: "Alice", email: "alice@example.com" }
User { id: 2, name: "Bob", email: "bob@example.com" }
...millions more...
```

**Naive Approach Problems:**

1. If we just append records, how do we find User #42? (scan everything? slow!)
2. If records have different sizes, how do we track where each record starts?
3. How do we update a record if the new version is bigger?
4. How do we avoid reading the entire file into memory?

**The Solution: Page-Based Storage**

Databases divide storage into fixed-size **pages** (typically 4KB, 8KB, or 16KB). Think of pages as "blocks" of storage—the smallest unit the database reads or writes.

### Why Fixed-Size Pages?

1. **Predictable I/O**: OS reads from disk in blocks. Pages align with disk blocks
2. **Easy Address Calculation**: Page 5 starts at byte `5 × PAGE_SIZE`
3. **Memory Management**: Easy to cache fixed-size pages in memory
4. **Atomic Writes**: Most filesystems guarantee atomic writes for 4KB blocks

### Page Structure Overview

Every page has two main parts:

```
┌────────────────────────────────────┐
│         PAGE HEADER                │ ← Metadata about the page
│  (page_id, page_type, num_slots)  │
├────────────────────────────────────┤
│                                    │
│         FREE SPACE                 │
│                                    │
├────────────────────────────────────┤
│         SLOT ARRAY                 │ ← Pointers to records
│  (grows downward)                  │
├────────────────────────────────────┤
│         TUPLE DATA                 │ ← Actual records
│  (grows upward)                    │
└────────────────────────────────────┘
```

### Slotted Page Architecture (PostgreSQL's Approach)

The **slotted page** solves variable-length record storage:

**Key Idea**: Separate the record pointers from the record data

```
Page Layout (4096 bytes):

Byte 0:
┌─────────────────────────────────────────┐
│ Page Header (24 bytes)                  │
│  - page_id: u32                         │
│  - page_type: u8                        │
│  - num_slots: u16                       │
│  - free_space_offset: u16               │
│  - free_space_end: u16                  │
└─────────────────────────────────────────┘
│ Slot 0: [offset: 4000, length: 32]     │
│ Slot 1: [offset: 3960, length: 40]     │
│ Slot 2: [offset: 3920, length: 40]     │
│              ...                         │
│         (Free Space)                    │
│              ...                         │
│         Tuple 2 (40 bytes)              │ ← offset 3920
│         Tuple 1 (40 bytes)              │ ← offset 3960
│         Tuple 0 (32 bytes)              │ ← offset 4000
└─────────────────────────────────────────┘
Byte 4095
```

**How It Works:**

1. Slot array grows **downward** from the header
2. Tuple data grows **upward** from the end
3. Free space is in the middle
4. Each slot contains: `(offset, length)` pointing to the tuple
5. Tuples can be reordered without changing slot numbers

**Benefits:**

- Fast lookup: `tuple = page.get_tuple(slot_id)` → just read `slots[slot_id]`
- Defragmentation: Can compact tuples without changing slot IDs
- Updates: If tuple grows, move it but keep the slot pointing to it
- Deletes: Mark slot as invalid, reclaim space later

### Record (Tuple) Structure

Each record stored in a page has:

```
Tuple Structure:
┌─────────────────────────────────────┐
│ Tuple Header (8 bytes)              │
│  - tuple_size: u16                  │
│  - null_bitmap: variable            │
└─────────────────────────────────────┘
│ Field 1 Data                        │
│ Field 2 Data                        │
│ Field 3 Data                        │
│      ...                            │
└─────────────────────────────────────┘
```

For Week 1, we'll keep it simple: just store `(key: u32, value: Vec<u8>)` pairs.

### Page Types

Different page types for different purposes:

1. **Data Pages**: Store actual tuples/records
2. **Index Pages**: Store B+ tree nodes (internal or leaf)
3. **Header Page**: Metadata about the database file
4. **Free Space Map**: Track which pages have free space

Week 1 focuses on **Index Pages** for B+ tree nodes.

### Disk Manager Responsibilities

The **Disk Manager** is the lowest layer:

```
Disk Manager API:
- allocate_page() -> PageId
- deallocate_page(page_id)
- read_page(page_id) -> Page
- write_page(page_id, page)
```

**Design Decision**: One database = one file (for simplicity)

The file structure:

```
Byte 0:       [Page 0: Header Page]
Byte 4096:    [Page 1: Data/Index Page]
Byte 8192:    [Page 2: Data/Index Page]
...
```

Page ID `N` is at byte offset `N * PAGE_SIZE`.

### Important Concepts

#### 1. Page ID as "Pointer"

Instead of memory pointers, we use Page IDs:

```
struct PagePointer {
    page_id: PageId,  // Which page?
    slot_id: SlotId   // Which slot in that page?
}
```

This allows data to persist on disk!

#### 2. Page Pinning

When reading a page from disk to memory:

- **Pin** the page (increment reference count)
- Use the page
- **Unpin** the page when done

Pinned pages cannot be evicted from memory.

#### 3. Dirty Pages

When you modify a page in memory:

- Mark it as **dirty**
- Eventually write it back to disk
- Only dirty pages need to be flushed

### Design Decisions for Our Implementation

For Week 1, we'll make these choices:

1. **Page Size**: 4096 bytes (4KB) - matches OS page size
2. **Storage**: Single file per database
3. **Simple Records**: Just `(key: u32, value: Vec<u8>)` pairs
4. **B+ Tree Order**: 4 or 8 (small for easier debugging)
5. **No Concurrency**: Single-threaded for now (Week 3 adds this)

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

## Outcome

### What You'll Have by End of Day 1

1. **Page Structure Definition**: Rust structs for pages
   - `Page` struct with header and data
   - Methods for serializing/deserializing
2. **Disk Manager Implementation**:
   - Can create a database file
   - Can allocate new pages
   - Can read/write pages by ID
3. **Basic Testing**:

   - Write a page to disk
   - Read it back
   - Verify data integrity

4. **Conceptual Understanding**:
   - Why pages are the foundation
   - How slotted pages work
   - The role of the disk manager

---

## The Project

### What We'll Build Today

**Project: Disk Manager and Page Abstraction**

#### Components:

**1. Page Struct**

```
Page:
  - page_id: u32
  - data: [u8; 4096]
  - is_dirty: bool

Methods:
  - new() -> Page
  - get_page_id() -> u32
  - get_data() -> &[u8]
  - get_data_mut() -> &mut [u8]
  - to_bytes() -> [u8; 4096]
  - from_bytes(bytes: [u8; 4096]) -> Page
```

**2. Disk Manager Struct**

```
DiskManager:
  - file: File
  - num_pages: u32

Methods:
  - new(path: &str) -> Result<DiskManager>
  - allocate_page() -> u32
  - deallocate_page(page_id: u32)
  - read_page(page_id: u32) -> Result<Page>
  - write_page(page: &Page) -> Result<()>
  - flush() -> Result<()>
```

**3. Constants**

```
PAGE_SIZE = 4096 bytes
INVALID_PAGE_ID = u32::MAX
```

#### File Structure

```
src/
  storage/
    mod.rs         ← Module declarations
    page.rs        ← Page struct and methods
    disk_manager.rs ← DiskManager implementation

tests/
  test_page.rs     ← Page tests
  test_disk.rs     ← Disk manager tests
```

### Testing Strategy

**Test 1: Page Serialization**

```
Test: Create a page, write data, serialize, deserialize, verify data
```

**Test 2: Disk Manager Creation**

```
Test: Create new database file, verify header page
```

**Test 3: Page Allocation**

```
Test: Allocate 100 pages, verify unique IDs
```

**Test 4: Read/Write Round-Trip**

```
Test: Write page with data, read it back, verify content matches
```

**Test 5: Multiple Pages**

```
Test: Write 1000 pages with unique data, read random pages, verify
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

### Discussion Questions

Think about these as you implement:

1. **What happens if we crash while writing a page?**

   - Partial writes can corrupt data
   - Solution preview: Write-Ahead Logging (Week 3)

2. **Why use page IDs instead of memory pointers?**

   - Memory addresses change between runs
   - Page IDs are stable references to disk locations

3. **What if a record is larger than a page?**

   - Week 2: Toast storage (The Oversized-Attribute Storage Technique)
   - For now: Limit record size to < PAGE_SIZE

4. **How do we track which pages are free?**
   - Simple approach: Keep free list in memory
   - Better approach: Free space map (bitmap of free pages)

---

## Summary

Today you learned the foundation of database storage:

✅ **Pages** are the fundamental unit of storage
✅ **Slotted pages** handle variable-length records elegantly  
✅ **Disk Manager** provides low-level page read/write  
✅ **Page IDs** act as persistent pointers  
✅ **Serialization** converts structs to bytes and back

Tomorrow, we'll build the **Buffer Pool Manager** to cache pages in memory and avoid expensive disk I/O.

---

## Homework / Extended Learning

1. **Read the PostgreSQL page layout documentation** - see how production systems do it
2. **Experiment with page sizes** - try 8KB or 16KB and measure performance
3. **Add page checksums** - detect corrupted pages
4. **Implement a free page list** - track deallocated pages for reuse

---

**[← Back to Week 1 Overview](README.md)** | **[Day 2: Buffer Pool Manager →](day_2.md)**
