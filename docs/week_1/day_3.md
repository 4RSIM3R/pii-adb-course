# Day 3: B+ Tree Foundation

## Title: B+ Tree Structure and Search Operations

### What We Cover in This Section

- B+ Tree fundamentals and properties
- Why B+ Trees are perfect for databases
- Internal nodes vs leaf nodes
- Tree traversal and search algorithm
- B+ Tree invariants and structure

---

## Description

### The Problem: How Do We Find Data Quickly?

**Scenario**: You have 1 million user records stored in pages. How do you find User #42?

**Bad Solutions:**

1. **Linear Scan**: Check every page

   - Time: O(n) = 1,000,000 comparisons
   - Disk I/O: Read all pages (slow!)

2. **Hash Table**: Fast lookups O(1)

   - But: No range queries
   - Can't do: `SELECT * FROM users WHERE id BETWEEN 10 AND 50`

3. **Binary Search Tree**: O(log n) lookups
   - Problem: Trees can become unbalanced
   - Worst case: O(n) like a linked list

**The Solution: B+ Tree**

B+ Trees give us:

- **Fast lookups**: O(log n)
- **Range queries**: Leaf nodes are linked
- **Always balanced**: Guaranteed O(log n) height
- **Disk-friendly**: High fanout reduces tree height

### What is a B+ Tree?

A **B+ Tree** is a self-balancing tree optimized for systems that read/write large blocks of data (like databases).

**Key Properties:**

1. **All data is in leaf nodes** (internal nodes only guide search)
2. **Leaf nodes are linked** (enables efficient range scans)
3. **Always balanced** (all paths from root to leaf have same length)
4. **High fanout** (each node has many children, tree stays short)
5. **Sorted order** (enables binary search within nodes)

### B+ Tree Structure

```
Example B+ Tree (order 4, max 4 children per node):

                    [13|  |  ]            ← Root (Internal Node)
                   /          \
                  /            \
          [4|7|10]              [16|19|  ] ← Internal Nodes
         /   |   \              /    |    \
        /    |    \            /     |     \
    [1,2] [4,5,6] [7,8,9] [13,14,15] [16,17,18] [19,20,21] ← Leaf Nodes
      ↕       ↕       ↕        ↕         ↕          ↕
    (data) (data)  (data)   (data)    (data)     (data)

    ←→  ←→  ←→  ←→  ←→  ←→  (Leaf nodes linked)
```

**Key Observations:**

- Internal nodes contain **keys** (routing values) and **page IDs** (pointers to children)
- Leaf nodes contain **keys** and **values** (actual data or row IDs)
- Leaf nodes are linked like a linked list (for range scans)

### Internal vs Leaf Nodes

#### Internal Node (Index Node)

```
Internal Node Structure:
┌──────────────────────────────────────────┐
│ Node Header                              │
│  - is_leaf: false                        │
│  - num_keys: 3                           │
│  - parent_page_id: ...                   │
└──────────────────────────────────────────┘
│ Keys: [10, 20, 30]                       │
│ Children: [page_5, page_7, page_9, page_11] │
└──────────────────────────────────────────┘

Meaning:
  - Values < 10: Go to page_5
  - Values 10-19: Go to page_7
  - Values 20-29: Go to page_9
  - Values ≥ 30: Go to page_11

Note: n keys → n+1 children
```

**Purpose**: Route searches to correct child node

#### Leaf Node (Data Node)

```
Leaf Node Structure:
┌──────────────────────────────────────────┐
│ Node Header                              │
│  - is_leaf: true                         │
│  - num_keys: 4                           │
│  - next_leaf_page_id: page_8             │
└──────────────────────────────────────────┘
│ Keys: [10, 15, 18, 20]                   │
│ Values: [data_10, data_15, data_18, data_20] │
└──────────────────────────────────────────┘

Note: n keys → n values (one-to-one)
```

**Purpose**: Store actual key-value pairs

### B+ Tree Parameters

**Order (or Fanout)**: Maximum number of children per internal node

```
For order d:
  - Internal node: max d children, max d-1 keys
  - Leaf node: max d-1 key-value pairs
  - Minimum occupancy: ⌈d/2⌉ (except root)

Example: Order 4 B+ Tree
  - Internal: 2-4 children, 1-3 keys
  - Leaf: 1-3 key-value pairs
  - Root: 1-3 keys (special case, can have fewer)
```

**Height vs Number of Records:**

```
For order d and n records:
Height ≈ log_d(n)

Examples (order 100):
  - 100 records: height 1 (just root leaf)
  - 10,000 records: height 2
  - 1,000,000 records: height 3
  - 100,000,000 records: height 4

High fanout = short tree = fewer disk I/Os!
```

### B+ Tree Invariants (Rules)

These rules are **always** maintained:

1. **Balanced**: All leaf nodes at same depth
2. **Sorted**: Keys in each node are sorted
3. **Min Occupancy**: Each node (except root) is at least half full
4. **Parent Keys**: Parent key separates child key ranges
5. **Leaf Links**: All leaf nodes form a linked list

**Why These Rules Matter:**

- Balanced → Guaranteed O(log n) search
- Sorted → Binary search within nodes
- Min occupancy → Tree stays bushy (doesn't degenerate)
- Leaf links → Fast range scans

### Search Algorithm

**Goal**: Find value for a given key

```
Algorithm: Search(key, root_page_id)

1. Load root page from buffer pool
2. If root is leaf:
     - Binary search for key in leaf
     - Return associated value

3. If root is internal:
     - Find correct child pointer:
       * If key < keys[0]: child = children[0]
       * If key ≥ keys[i] and key < keys[i+1]: child = children[i+1]
       * If key ≥ keys[last]: child = children[last+1]
     - Recursively search(key, child_page_id)

Time Complexity: O(log_d n) where d is order
```

**Example Search for key=17:**

```
Step 1: Start at root
        [13]
       /    \
      ↓      ↓
   [4|7]  [16|19]

   17 > 13, go right to [16|19]

Step 2: At internal node [16|19]
   /    |    \
 [13] [16]  [19]

 16 ≤ 17 < 19, go to middle child [16]

Step 3: At leaf node [16, 17, 18]
   Binary search finds 17
   Return value_17
```

### Range Queries (Scan)

**Why Leaf Links Matter:**

```
Query: SELECT * FROM users WHERE id BETWEEN 10 AND 20

1. Search for key=10 (find starting leaf)
2. Iterate through leaf nodes using next_leaf_page_id
3. Collect all keys in range [10, 20]
4. Stop when key > 20

Leaf chain:
[1,2,3] → [4,5,6] → [7,8,9] → [10,11,12] → [13,14,15] → [16,17,18] → [19,20,21]
                                  ↑ start here           collect these → ↑ stop here
```

**Efficiency**: No need to traverse up and down the tree!

### B+ Tree Node Storage

Each B+ Tree node is stored in **one page**.

```
Page Layout for B+ Tree Node:

┌────────────────────────────────────────┐
│ Page Header (from Day 1)              │
│  - page_id, page_type, etc.           │
├────────────────────────────────────────┤
│ B+ Tree Node Header                   │
│  - is_leaf: bool                      │
│  - num_keys: u16                      │
│  - parent_page_id: u32                │
│  - next_leaf_page_id: u32 (if leaf)   │
├────────────────────────────────────────┤
│ Key Array                             │
│  [key_0, key_1, key_2, ...]          │
├────────────────────────────────────────┤
│ Value/Children Array                  │
│  If internal: [child_0, child_1, ...] │
│  If leaf: [value_0, value_1, ...]     │
└────────────────────────────────────────┘
```

**Determining Order from Page Size:**

```
Given:
  - Page size: 4096 bytes
  - Key size: 4 bytes (u32)
  - Page ID size: 4 bytes (u32)
  - Header: 32 bytes

For internal node (n keys, n+1 children):
  space = n * 4 + (n+1) * 4 = 4n + 4n + 4 = 8n + 4
  8n + 4 ≤ 4096 - 32
  8n ≤ 4060
  n ≤ 507

  Order ≈ 500 (conservative)

For our learning purposes: Use order 4 or 8 (easier to debug!)
Production databases: order 100-500
```

### Visualizing B+ Tree Properties

**Property 1: All Leaves at Same Level**

```
Good (balanced):
       [10]
      /    \
   [5]      [15]
   / \      / \
[1] [7]  [13] [18]  ← All at level 3

Bad (unbalanced - NOT a valid B+ Tree):
       [10]
      /    \
   [5]      [15]
   /          \
[1]            [18]
               /
            [16]     ← Different depths!
```

**Property 2: Keys Separate Ranges**

```
     [50]
    /    \
[10|20|30] [60|70|80]

Left child: all keys < 50
Right child: all keys ≥ 50

Within [10|20|30]:
  - Child_0: keys < 10
  - Child_1: 10 ≤ keys < 20
  - Child_2: 20 ≤ keys < 30
  - Child_3: 30 ≤ keys < 50
```

### B+ Tree vs B Tree

**B+ Tree** (what we're building):

- All data in leaves
- Leaves linked together
- Better for range queries
- Used by: PostgreSQL, MySQL InnoDB

**B Tree** (less common):

- Data in all nodes (internal + leaf)
- No leaf links
- Slightly faster single-key lookup
- Used by: Older systems

**Why B+ Tree Wins:**

- Range queries are common in databases
- Leaf links enable sequential scan
- Internal nodes smaller (more fanout)

### Real-World Examples

**PostgreSQL:**

- Uses B+ Tree for indexes (`CREATE INDEX`)
- Called "btree" index type
- Order ~200-300 depending on key size
- Pages are 8KB

**MySQL InnoDB:**

- Clustered B+ Tree (table data in leaf nodes)
- Secondary indexes point to primary key
- Pages are 16KB
- Order ~300-500

**SQLite:**

- B+ Tree for everything (tables and indexes)
- Variable page size (512B-64KB)
- Order depends on page size

---

## Outcome

### What You'll Have by End of Day 3

1. **B+ Tree Node Structure**:
   - Rust structs for internal and leaf nodes
   - Serialization to/from pages
2. **Tree Traversal**:
   - Search function (find key in tree)
   - Binary search within nodes
3. **Helper Functions**:
   - Find correct child for key
   - Check if node is full
   - Get min/max keys from node
4. **Testing**:

   - Create small B+ Tree manually
   - Search for existing keys
   - Search for non-existing keys
   - Verify tree structure

5. **Conceptual Understanding**:
   - Why B+ Trees are optimal for databases
   - How tree structure maps to pages
   - Relationship between order and height

---

## The Project

### What We'll Build Today

**Project: B+ Tree Node Structure and Search**

#### Components:

**1. B+ Tree Node Types**

```
BPlusTreeNode:
  Common:
    - is_leaf: bool
    - num_keys: u16
    - parent_page_id: u32
    - page_id: u32

  Internal Node:
    - keys: Vec<u32>             // Size: order - 1
    - children: Vec<u32>         // Size: order (page IDs)

  Leaf Node:
    - keys: Vec<u32>             // Size: order - 1
    - values: Vec<Vec<u8>>       // Size: order - 1
    - next_leaf_page_id: u32
```

**2. B+ Tree Structure**

```
BPlusTree:
  - root_page_id: u32
  - order: usize
  - buffer_pool: BufferPoolManager

Methods:
  - new(order: usize, buffer_pool: BufferPoolManager) -> Self
  - search(key: u32) -> Option<Vec<u8>>
  - get_node(page_id: u32) -> BPlusTreeNode
  - find_leaf(key: u32) -> u32  // Returns leaf page_id
```

**3. Node Operations**

```
BPlusTreeNode Methods:
  - is_full() -> bool
  - is_underflow() -> bool
  - get_min_key() -> u32
  - get_max_key() -> u32
  - find_child_index(key: u32) -> usize
  - binary_search_keys(key: u32) -> Result<usize, usize>
  - to_page() -> Page         // Serialize
  - from_page(page: Page) -> Self  // Deserialize
```

#### File Structure

```
src/
  storage/
    mod.rs
    page.rs
    disk_manager.rs
    replacer.rs
    buffer_pool.rs

  index/
    mod.rs
    bplus_tree_node.rs  ← NEW: Node structure
    bplus_tree.rs       ← NEW: Tree operations

tests/
  test_bplus_node.rs    ← NEW: Node tests
  test_bplus_search.rs  ← NEW: Search tests
```

### Testing Strategy

**Test 1: Node Creation**

```
Test: Create internal and leaf nodes, verify structure
```

**Test 2: Node Serialization**

```
Test: Create node, serialize to page, deserialize, verify equality
```

**Test 3: Binary Search in Node**

```
Test: Node with keys [10,20,30], search for 15, should return index 1
```

**Test 4: Find Child Index**

```
Test: Internal node [10,20,30], find_child for key=25, should return child 2
```

**Test 5: Single Node Tree Search**

```
Test: Tree with just root (leaf), search for key in node
```

**Test 6: Multi-Level Tree Search**

```
Test: Tree with height 2, search various keys, verify correct leaf found
```

**Test 7: Search Non-Existent Key**

```
Test: Search for key not in tree, should return None
```

**Test 8: Tree Structure Validation**

```
Test: Build tree, verify all internal pointers are correct
```

### Key Implementation Details

#### Node Serialization (Pseudo-code)

```
impl BPlusTreeNode {
    fn to_page(&self) -> Page {
        let mut page = Page::new(self.page_id);
        let mut offset = 0;

        // Write header
        write_u8(&mut page, offset, self.is_leaf as u8); offset += 1;
        write_u16(&mut page, offset, self.num_keys); offset += 2;
        write_u32(&mut page, offset, self.parent_page_id); offset += 4;

        if self.is_leaf {
            write_u32(&mut page, offset, self.next_leaf_page_id); offset += 4;

            // Write keys
            for key in &self.keys {
                write_u32(&mut page, offset, *key); offset += 4;
            }

            // Write values
            for value in &self.values {
                write_u16(&mut page, offset, value.len()); offset += 2;
                write_bytes(&mut page, offset, value); offset += value.len();
            }
        } else {
            // Write keys
            for key in &self.keys {
                write_u32(&mut page, offset, *key); offset += 4;
            }

            // Write children
            for child in &self.children {
                write_u32(&mut page, offset, *child); offset += 4;
            }
        }

        page
    }
}
```

#### B+ Tree Search (Pseudo-code)

```
impl BPlusTree {
    fn search(&mut self, key: u32) -> Option<Vec<u8>> {
        let leaf_page_id = self.find_leaf(key);
        let leaf_node = self.get_node(leaf_page_id);

        // Binary search in leaf
        match leaf_node.keys.binary_search(&key) {
            Ok(index) => Some(leaf_node.values[index].clone()),
            Err(_) => None,
        }
    }

    fn find_leaf(&mut self, key: u32) -> u32 {
        let mut current_page_id = self.root_page_id;

        loop {
            let node = self.get_node(current_page_id);

            if node.is_leaf {
                return current_page_id;
            }

            // Find child to descend to
            let child_index = node.find_child_index(key);
            current_page_id = node.children[child_index];
        }
    }
}

impl BPlusTreeNode {
    fn find_child_index(&self, key: u32) -> usize {
        // Find first key ≥ search key
        for (i, &node_key) in self.keys.iter().enumerate() {
            if key < node_key {
                return i;
            }
        }
        // Key is greater than all keys, return last child
        self.num_keys as usize
    }
}
```

### Discussion Questions

1. **Why not use binary trees (AVL, Red-Black)?**

   - Too many nodes = too many disk I/Os
   - B+ Tree: fewer, wider nodes = fewer disk reads

2. **Why link leaf nodes instead of storing them in parent?**

   - Enables efficient range scans without tree traversal
   - Sequential access pattern (cache-friendly)

3. **What happens if key size varies greatly?**

   - Use maximum size for allocation
   - Or: Prefix compression (advanced topic)

4. **How do we choose the order?**
   - Maximize fanout while fitting in one page
   - Balance: High order = shorter tree, but slower binary search in node

---

## Summary

Today you mastered B+ Tree fundamentals:

✅ **B+ Tree structure** and properties  
✅ **Internal vs leaf nodes** and their roles  
✅ **Tree traversal** and search algorithm  
✅ **Node serialization** to pages  
✅ **Why B+ Trees** are perfect for databases

Tomorrow, we'll implement **insertion and node splits**—the hard part!

---

## Homework / Extended Learning

1. **Visualize B+ Trees** - Use pen and paper to build trees with various insertions
2. **Compare B+ Tree with B Tree** - Understand the trade-offs
3. **Study PostgreSQL btree code** - See production implementation
4. **Calculate tree height** - For 1 billion records with order 100

---

**[← Day 2: Buffer Pool Manager](day_2.md)** | **[Day 4: B+ Tree Insertion →](day_4.md)**
