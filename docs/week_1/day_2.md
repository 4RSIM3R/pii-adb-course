# Day 2: Buffer Pool Manager

## Title: Caching Pages in Memory with LRU Eviction

### What We Cover in This Section

- Buffer pool architecture and design
- LRU (Least Recently Used) eviction policy
- Page pinning and reference counting
- Dirty page tracking and flushing
- Page table (page ID → frame mapping)

---

## Description

### The Problem: Disk I/O is Expensive

**Reality Check:**

- **RAM access**: ~100 nanoseconds
- **SSD read**: ~100 microseconds (1000× slower than RAM)
- **HDD read**: ~10 milliseconds (100,000× slower than RAM)

If every database query required disk I/O, your database would be unbearably slow!

**Example Scenario:**

```
User queries: SELECT * FROM users WHERE id = 42

Without caching:
1. Read page 10 to find index entry → 10ms disk I/O
2. Follow pointer to page 57 for data → 10ms disk I/O
3. Return result
Total: 20ms per query

With caching (after first query):
1. Check cache, find page 10 → 100ns RAM access
2. Check cache, find page 57 → 100ns RAM access
3. Return result
Total: 0.0002ms per query (100,000× faster!)
```

### The Solution: Buffer Pool Manager

The **Buffer Pool** is an in-memory cache of disk pages:

```
┌─────────────────────────────────────────────────┐
│              Your Code                          │
│    (requests page 42)                           │
└─────────────┬───────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────┐
│         Buffer Pool Manager                     │
│                                                 │
│  Page 42 in cache? ──Yes──> Return from cache  │
│         │                                       │
│        No                                       │
│         │                                       │
│         └──> Evict old page (if full)          │
│         └──> Read from disk                     │
│         └──> Store in cache                     │
│         └──> Return                             │
└─────────────────────────────────────────────────┘
```

### Buffer Pool Architecture

**Key Components:**

```
Buffer Pool:
┌──────────────────────────────────────┐
│       Page Table (Hash Map)          │
│   page_id → frame_id mapping         │
│                                      │
│   {10 → 0, 57 → 1, 102 → 2, ...}    │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│       Frame Array (Fixed Size)       │
│                                      │
│  Frame 0: [Page 10 data][metadata]  │
│  Frame 1: [Page 57 data][metadata]  │
│  Frame 2: [Page 102 data][metadata] │
│  Frame 3: [Empty]                    │
│  ...                                 │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│       Replacer (LRU List)            │
│   Tracks which frames are evictable  │
│                                      │
│   Most recent ← [2, 1, 0] → Least    │
└──────────────────────────────────────┘
```

**Terminology:**

- **Page**: The disk data (identified by page_id)
- **Frame**: A slot in memory that holds a page
- **Page Table**: Maps page_id → frame_id (which frame holds this page?)
- **Replacer**: Decides which frame to evict when buffer pool is full

### Frame Metadata

Each frame tracks:

```
Frame {
    page: Page,              // The actual page data
    page_id: u32,           // Which page is this?
    pin_count: u32,         // How many threads are using it?
    is_dirty: bool,         // Has it been modified?
}
```

**Pin Count Rules:**

- `pin_count > 0`: Frame is in use, cannot be evicted
- `pin_count == 0`: Frame is evictable
- Increment: Call `pin_page()` before using
- Decrement: Call `unpin_page()` when done

**Dirty Bit Rules:**

- Set to `true` when page is modified
- Dirty pages must be flushed to disk before eviction
- Clean pages can be discarded (already on disk)

### LRU (Least Recently Used) Eviction Policy

**The Idea**: Evict the page that hasn't been used for the longest time

**Why LRU?**

- Temporal locality: Recently used pages likely to be used again
- Simple to implement
- Good performance in practice

**LRU Implementation**: Doubly-linked list

```
Most Recently Used                    Least Recently Used
      │                                      │
      ▼                                      ▼
    ┌───┐    ┌───┐    ┌───┐    ┌───┐    ┌───┐
    │ 2 │◄──►│ 5 │◄──►│ 1 │◄──►│ 8 │◄──►│ 3 │
    └───┘    └───┘    └───┘    └───┘    └───┘
      ▲                                      ▲
      │                                      │
    Insert here                        Evict from here
    when accessed                      when needed
```

**Operations:**

1. **Access page**: Move to front of list
2. **Need to evict**: Remove from back of list (if pin_count == 0)
3. **Pin page**: Remove from LRU list (not evictable)
4. **Unpin page**: Add to front of LRU list (evictable again)

### Buffer Pool Manager API

```
BufferPoolManager:
  - new(pool_size: usize, disk_manager: DiskManager) -> Self
  - fetch_page(page_id: u32) -> Result<&mut Page>
  - unpin_page(page_id: u32, is_dirty: bool) -> Result<()>
  - flush_page(page_id: u32) -> Result<()>
  - new_page() -> Result<&mut Page>
  - delete_page(page_id: u32) -> Result<()>
  - flush_all_pages() -> Result<()>
```

### Operation Flow Examples

#### Fetching a Page (Cache Hit)

```
User: fetch_page(42)

1. Check page table: Is page 42 in a frame?
   → Yes! Page 42 is in frame 3

2. Increment pin_count for frame 3
   pin_count: 0 → 1

3. Remove frame 3 from LRU list (now pinned)

4. Return mutable reference to page in frame 3
```

#### Fetching a Page (Cache Miss, Buffer Pool Not Full)

```
User: fetch_page(42)

1. Check page table: Is page 42 in a frame?
   → No!

2. Buffer pool has free frames?
   → Yes! Frame 5 is empty

3. Read page 42 from disk using DiskManager

4. Store page 42 in frame 5
   Update page table: {42 → 5}

5. Set pin_count = 1 for frame 5

6. Return mutable reference to page in frame 5
```

#### Fetching a Page (Cache Miss, Buffer Pool Full)

```
User: fetch_page(42)

1. Check page table: Is page 42 in a frame?
   → No!

2. Buffer pool has free frames?
   → No! All frames occupied

3. Ask LRU replacer for victim frame
   → Frame 2 (least recently used, pin_count == 0)

4. Is frame 2 dirty?
   → Yes! Flush page to disk first

5. Evict frame 2:
   - Remove old page_id from page table
   - Read new page 42 from disk
   - Store in frame 2
   - Update page table: {42 → 2}

6. Set pin_count = 1 for frame 2

7. Return mutable reference to page in frame 2
```

#### Unpinning a Page

```
User: unpin_page(42, is_dirty=true)

1. Find frame holding page 42
   → Frame 3

2. Decrement pin_count
   pin_count: 1 → 0

3. Mark as dirty if is_dirty=true
   is_dirty = true

4. Add frame 3 to LRU list (now evictable)
   LRU list: [3, 5, 7, 1, 2]
             ↑ most recent
```

### Concurrency Considerations (Week 3 Preview)

For now, we're single-threaded. In Week 3, we'll add:

- **Latches**: Locks protecting buffer pool data structures
- **Page Latches**: Locks protecting individual pages
- **Multiple Readers**: Share read access to pages
- **Single Writer**: Exclusive write access to pages

### Performance Optimization Techniques

#### 1. Pre-fetching

When reading page N, also read page N+1, N+2 (sequential scans benefit)

#### 2. Adaptive Replacement Cache (ARC)

Better than LRU, tracks both recency and frequency

#### 3. Clock Algorithm

Simpler than LRU, approximates LRU with circular buffer

#### 4. Multiple Buffer Pools

Reduce contention in multi-threaded scenarios

Week 1 uses simple LRU. Future optimizations come later!

### Real-World Examples

**PostgreSQL**:

- "Shared Buffers" configuration
- Default: 128MB (tiny!)
- Recommended: 25% of RAM
- Uses Clock-Sweep algorithm (LRU approximation)

**MySQL InnoDB**:

- "Buffer Pool" configuration
- Default: 128MB
- Recommended: 70-80% of RAM
- Uses LRU with midpoint insertion

**MongoDB**:

- Uses OS page cache instead of custom buffer pool
- Simpler but less control

---

## Outcome

### What You'll Have by End of Day 2

1. **LRU Replacer Implementation**:
   - Doubly-linked list or simpler approximation
   - Methods: `victim()`, `pin()`, `unpin()`
2. **Buffer Pool Manager**:
   - Fixed-size array of frames
   - Page table (HashMap)
   - Integration with disk manager
3. **Page Lifecycle Management**:
   - Fetching pages from disk or cache
   - Pinning/unpinning mechanism
   - Dirty page tracking
4. **Testing Suite**:

   - Cache hit/miss scenarios
   - Eviction when buffer pool is full
   - Dirty page flushing
   - Concurrent pin/unpin (multiple references)

5. **Performance Awareness**:
   - Understand cache hit ratio importance
   - Measure disk I/O vs cache access

---

## The Project

### What We'll Build Today

**Project: Buffer Pool Manager with LRU Eviction**

#### Components:

**1. Replacer Interface (LRU)**

```
LRUReplacer:
  - frames: VecDeque<FrameId>  // Ordered by recency

Methods:
  - new(num_frames: usize) -> Self
  - victim() -> Option<FrameId>      // Get least recently used
  - pin(frame_id: FrameId)           // Remove from eviction list
  - unpin(frame_id: FrameId)         // Add to eviction list
  - size() -> usize                  // Number of evictable frames
```

**2. Frame Metadata**

```
Frame:
  - page: Option<Page>
  - page_id: Option<u32>
  - pin_count: u32
  - is_dirty: bool
```

**3. Buffer Pool Manager**

```
BufferPoolManager:
  - pool_size: usize
  - frames: Vec<Frame>
  - page_table: HashMap<u32, usize>  // page_id → frame_id
  - free_list: VecDeque<usize>       // Free frame IDs
  - replacer: LRUReplacer
  - disk_manager: DiskManager

Methods:
  - new(pool_size: usize, disk_manager: DiskManager) -> Self
  - fetch_page(page_id: u32) -> Result<&mut Page>
  - unpin_page(page_id: u32, is_dirty: bool) -> Result<()>
  - flush_page(page_id: u32) -> Result<()>
  - new_page() -> Result<(u32, &mut Page)>
  - delete_page(page_id: u32) -> Result<()>
  - flush_all_pages() -> Result<()>
```

#### File Structure

```
src/
  storage/
    mod.rs
    page.rs
    disk_manager.rs
    replacer.rs        ← NEW: LRU replacer
    buffer_pool.rs     ← NEW: Buffer pool manager

tests/
  test_replacer.rs   ← NEW: LRU replacer tests
  test_buffer_pool.rs ← NEW: Buffer pool tests
```

### Testing Strategy

**Test 1: LRU Replacer Basic Operations**

```
Test: Create replacer, unpin frames 1,2,3, victim should return 1 (FIFO)
```

**Test 2: LRU Replacer Pin/Unpin**

```
Test: Unpin 1,2,3, pin 2, victim should return 1, next victim 3 (skip pinned)
```

**Test 3: Buffer Pool Fetch New Page**

```
Test: Fetch page not in buffer pool, should read from disk
```

**Test 4: Buffer Pool Fetch Cached Page**

```
Test: Fetch same page twice, second fetch should not read from disk
```

**Test 5: Buffer Pool Eviction**

```
Test: Fill buffer pool, fetch one more page, should evict LRU page
```

**Test 6: Dirty Page Flush on Eviction**

```
Test: Modify page, unpin as dirty, evict, verify written to disk
```

**Test 7: Pin Count Prevents Eviction**

```
Test: Pin all pages, attempt new fetch, should fail (no evictable frames)
```

**Test 8: Multiple Pin/Unpin**

```
Test: Pin same page twice, unpin once, should still be pinned
```

### Key Implementation Details

#### LRU Replacer (Pseudo-code)

```
struct LRUReplacer {
    frames: VecDeque<FrameId>,  // Back = LRU, Front = MRU
}

impl LRUReplacer {
    fn victim(&mut self) -> Option<FrameId> {
        // Remove least recently used (back of deque)
        self.frames.pop_back()
    }

    fn pin(&mut self, frame_id: FrameId) {
        // Remove from eviction list
        self.frames.retain(|&fid| fid != frame_id);
    }

    fn unpin(&mut self, frame_id: FrameId) {
        // Add to front (most recently used)
        if !self.frames.contains(&frame_id) {
            self.frames.push_front(frame_id);
        }
    }
}
```

#### Buffer Pool Manager Fetch (Pseudo-code)

```
impl BufferPoolManager {
    fn fetch_page(&mut self, page_id: u32) -> Result<&mut Page> {
        // 1. Check if already in buffer pool
        if let Some(&frame_id) = self.page_table.get(&page_id) {
            // Cache hit!
            let frame = &mut self.frames[frame_id];
            frame.pin_count += 1;
            if frame.pin_count == 1 {
                self.replacer.pin(frame_id);  // Remove from LRU
            }
            return Ok(&mut frame.page);
        }

        // 2. Cache miss - need a frame
        let frame_id = if let Some(fid) = self.free_list.pop_front() {
            // Use free frame
            fid
        } else {
            // Evict a page
            let victim_id = self.replacer.victim()
                .ok_or("No evictable frames")?;

            let victim_frame = &mut self.frames[victim_id];

            // Flush if dirty
            if victim_frame.is_dirty {
                self.disk_manager.write_page(&victim_frame.page)?;
            }

            // Remove from page table
            if let Some(old_page_id) = victim_frame.page_id {
                self.page_table.remove(&old_page_id);
            }

            victim_id
        };

        // 3. Read page from disk
        let page = self.disk_manager.read_page(page_id)?;

        // 4. Store in frame
        let frame = &mut self.frames[frame_id];
        frame.page = Some(page);
        frame.page_id = Some(page_id);
        frame.pin_count = 1;
        frame.is_dirty = false;

        // 5. Update page table
        self.page_table.insert(page_id, frame_id);

        Ok(&mut frame.page.as_mut().unwrap())
    }

    fn unpin_page(&mut self, page_id: u32, is_dirty: bool) -> Result<()> {
        let frame_id = self.page_table.get(&page_id)
            .ok_or("Page not in buffer pool")?;

        let frame = &mut self.frames[*frame_id];

        if frame.pin_count == 0 {
            return Err("Page already unpinned");
        }

        frame.pin_count -= 1;

        if is_dirty {
            frame.is_dirty = true;
        }

        if frame.pin_count == 0 {
            self.replacer.unpin(*frame_id);  // Now evictable
        }

        Ok(())
    }
}
```

### Common Pitfalls

1. **Forgetting to Unpin**:

   - Always unpin after using a page
   - Otherwise buffer pool fills with pinned pages

2. **Not Marking Dirty**:

   - If you modify a page, mark it dirty
   - Otherwise changes are lost on eviction

3. **Lifetime Issues**:

   - Can't return reference to page while holding mutable reference to buffer pool
   - Solution: Use interior mutability (RefCell) or redesign API

4. **Double-Free**:
   - Don't add same frame to free list twice
   - Don't add pinned frame to LRU list

### Discussion Questions

1. **Why not cache all pages in memory?**

   - Databases can be terabytes, RAM is limited
   - Need eviction policy for working set

2. **What if we have multiple unpins with different dirty flags?**

   - Once dirty, always dirty (until flushed)
   - `is_dirty = is_dirty || incoming_dirty`

3. **How do we measure buffer pool effectiveness?**

   - **Hit ratio**: (cache hits) / (total fetches)
   - Target: > 95% hit ratio for good performance

4. **What if eviction is slow (dirty pages)?**
   - Background writer thread (Week 3)
   - Write dirty pages proactively

---

## Summary

Today you built the critical caching layer:

✅ **Buffer Pool** caches disk pages in memory  
✅ **LRU eviction** intelligently chooses victims  
✅ **Pin/unpin** prevents in-use pages from eviction  
✅ **Dirty tracking** ensures modified pages are saved  
✅ **Page table** provides fast page lookups

Tomorrow, we'll build **B+ Tree indexes** on top of this buffer pool!

---

## Homework / Extended Learning

1. **Implement Clock algorithm** instead of LRU - simpler and faster
2. **Add buffer pool statistics** - track hit ratio, evictions, flushes
3. **Benchmark different pool sizes** - measure query performance
4. **Read about ARC (Adaptive Replacement Cache)** - used by ZFS

---

**[← Day 1: Storage Fundamentals](day_1.md)** | **[Day 3: B+ Tree Foundation →](day_3.md)**
