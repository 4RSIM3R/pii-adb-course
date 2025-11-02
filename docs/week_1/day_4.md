# Day 4: B+ Tree Insertion and Splits

## Title: Maintaining B+ Tree Balance Through Insertion

### What We Cover in This Section

- B+ Tree insertion algorithm
- Node splitting when full
- Propagating splits up the tree
- Creating new root (tree grows taller)
- Handling duplicate keys
- Maintaining B+ Tree invariants

---

## Description

### The Problem: How Do We Add Data Without Breaking the Tree?

Yesterday, you built a B+ Tree that can **search**. Today, you'll make it **dynamic**—able to grow by inserting new keys.

**Challenges:**

1. Where do we insert the new key?
2. What if the node is full?
3. How do we keep the tree balanced?
4. How do we maintain all invariants?

**The Stakes:**

- Insert incorrectly → Tree becomes imbalanced → Search becomes slow
- Violate invariants → Tree structure breaks → Data lost or unreachable

### Insertion Overview

**High-Level Algorithm:**

```
Algorithm: Insert(key, value)

1. Find the correct leaf node for the key
2. If leaf has space:
     - Insert key-value pair
     - Done!
3. If leaf is full:
     - Split the leaf into two nodes
     - Promote middle key to parent
     - If parent is full, split parent recursively
     - If root splits, create new root (tree grows)
```

**Example: Simple Insertion (No Split)**

```
Before: Insert key=12
      [10|20]
     /   |   \
 [5,7] [10,15] [20,25]

After:
      [10|20]
     /   |   \
 [5,7] [10,12,15] [20,25]  ← Simply added 12
```

Easy! But what if the node is full?

### Node Splitting (The Hard Part)

**Example: Insertion Causing Leaf Split**

```
Before: Insert key=8 (leaf is full, max 3 keys)
        [10]
       /    \
   [5,7,9]   [10,15,20]  ← Full! (max 3 keys)

Step 1: Try to insert 8 into [5,7,9]
   → Node is full! Need to split

Step 2: Split the node
   Original: [5,7,9] + new [8] = [5,7,8,9]
   Split into: [5,7] and [8,9]
   Middle key to promote: 8

Step 3: Promote 8 to parent
        [8|10]           ← Promoted 8
       /  |   \
   [5,7] [8,9] [10,15,20]

Step 4: Update leaf links
   [5,7] → [8,9] → [10,15,20]
```

**Key Observations:**

- Split point: Middle of the sorted keys
- Leaf split: Copy middle key up (key stays in leaf too)
- Internal split: Push middle key up (key moves to parent)
- Leaf links must be updated

### Internal Node Split

**Example: Split Propagates Up**

```
Before: Insert causes leaf split, but parent is also full
            [50]
           /    \
    [10|20|30]   [60|70|80]  ← Parent full (max 3 keys)
    /   |   |  \
 leaves...

Insert key=25 into [20-30 leaf] causes split
Promote 25 to parent [10|20|30]
But parent is full!

Step 1: Add 25 to parent: [10|20|25|30] (temporarily overfull)

Step 2: Split parent internal node
   Split [10|20|25|30] at middle (20)
   Left: [10|20]
   Right: [25|30]
   Promote: 20 (this key moves up, not copied)

Step 3: Create new root
         [20|50]           ← New root
        /   |    \
   [10] [25|30] [60|70|80]
    /  \    / \    / | | \
  leaves leaves  leaves
```

**Important Difference:**

- **Leaf split**: Copy middle key up (appears in both leaf and parent)
- **Internal split**: Push middle key up (appears only in parent)

### Insertion Algorithm (Detailed)

**Phase 1: Navigate to Leaf**

```
fn insert(key, value) -> Result<()> {
    // 1. Find correct leaf using search algorithm
    leaf_page_id = find_leaf(key)
    leaf_node = get_node(leaf_page_id)

    // 2. Check for duplicate key
    if leaf_node.contains(key) {
        return Error("Duplicate key")
    }

    // 3. Attempt insertion
    if !leaf_node.is_full() {
        // Easy case: just insert
        leaf_node.insert_at_leaf(key, value)
        write_node(leaf_node)
        return Ok(())
    }

    // 4. Hard case: node is full, need to split
    split_leaf_and_insert(leaf_node, key, value)
}
```

**Phase 2: Split Leaf Node**

```
fn split_leaf_and_insert(node, key, value) -> Result<()> {
    // 1. Create new leaf node
    new_leaf = allocate_new_node(is_leaf=true)

    // 2. Temporarily insert into old node (now overfull)
    temp_keys = node.keys + [key]
    temp_values = node.values + [value]
    temp_keys.sort()  // Keep sorted

    // 3. Split point (middle)
    mid = temp_keys.len() / 2

    // 4. Distribute keys
    node.keys = temp_keys[0..mid]
    node.values = temp_values[0..mid]

    new_leaf.keys = temp_keys[mid..]
    new_leaf.values = temp_values[mid..]

    // 5. Update leaf links
    new_leaf.next_leaf_page_id = node.next_leaf_page_id
    node.next_leaf_page_id = new_leaf.page_id

    // 6. Promote first key of new leaf to parent
    promoted_key = new_leaf.keys[0]

    // 7. Insert into parent (may cause recursive split)
    insert_into_parent(node, promoted_key, new_leaf)
}
```

**Phase 3: Insert Into Parent (Recursive)**

```
fn insert_into_parent(left_child, key, right_child) -> Result<()> {
    // Special case: splitting root
    if left_child.is_root() {
        create_new_root(left_child, key, right_child)
        return Ok(())
    }

    // Get parent node
    parent = get_node(left_child.parent_page_id)

    // Find insertion position in parent
    index = parent.find_insert_position(key)

    // Case 1: Parent has space
    if !parent.is_full() {
        parent.insert_key_at(index, key)
        parent.insert_child_at(index + 1, right_child.page_id)
        write_node(parent)
        return Ok(())
    }

    // Case 2: Parent is full, need to split
    split_internal_and_insert(parent, key, right_child)
}
```

**Phase 4: Split Internal Node**

```
fn split_internal_and_insert(node, key, new_child_page) -> Result<()> {
    // 1. Create new internal node
    new_internal = allocate_new_node(is_leaf=false)

    // 2. Temporarily insert into old node
    temp_keys = insert_into_sorted(node.keys, key)
    temp_children = insert_at_position(node.children, new_child_page)

    // 3. Split point
    mid = temp_keys.len() / 2
    promoted_key = temp_keys[mid]  // This key goes up!

    // 4. Distribute keys (excluding middle)
    node.keys = temp_keys[0..mid]
    node.children = temp_children[0..mid+1]

    new_internal.keys = temp_keys[mid+1..]  // Note: skip mid!
    new_internal.children = temp_children[mid+1..]

    // 5. Update parent pointers for children
    for child_page_id in new_internal.children {
        child = get_node(child_page_id)
        child.parent_page_id = new_internal.page_id
        write_node(child)
    }

    // 6. Recursively insert into parent
    insert_into_parent(node, promoted_key, new_internal)
}
```

**Phase 5: Create New Root**

```
fn create_new_root(left_child, key, right_child) -> Result<()> {
    // 1. Allocate new root node
    new_root = allocate_new_node(is_leaf=false)

    // 2. Set up new root
    new_root.keys = [key]
    new_root.children = [left_child.page_id, right_child.page_id]
    new_root.num_keys = 1

    // 3. Update children's parent pointers
    left_child.parent_page_id = new_root.page_id
    right_child.parent_page_id = new_root.page_id

    // 4. Update tree root
    tree.root_page_id = new_root.page_id
    tree.height += 1  // Tree grew taller!

    // 5. Write all changes
    write_node(new_root)
    write_node(left_child)
    write_node(right_child)
}
```

### Split Point Selection

**Why Middle?**

```
Original node (order 4, max 3 keys): [10|20|30]
Insert 25: [10|20|25|30] (temporarily overfull)

Split at middle (index 2):
  Left: [10|20]    (2 keys)
  Right: [25|30]   (2 keys)

Both nodes are ≥ ⌈4/2⌉ = 2 keys ✓
Maintains minimum occupancy invariant
```

**Alternative (Bad): Split at End**

```
Split at index 3:
  Left: [10|20|25]  (3 keys)
  Right: []         (0 keys)  ✗ Violates minimum occupancy!
```

### Leaf vs Internal Split Differences

**Leaf Node Split:**

```
Before: [10|20|30] full, insert 25
Temp:   [10|20|25|30]

Split:
  Left leaf:  [10|20]
  Right leaf: [25|30]
  Promote: 25 (COPIED to parent, stays in right leaf)

Parent gets: [...|25|...]
                  ↓
           [10|20] [25|30]
```

**Internal Node Split:**

```
Before: [10|20|30] full, insert 25
Temp:   [10|20|25|30]

Split:
  Left internal:  [10|20]
  Right internal: [30]        ← Note: Only 1 key
  Promote: 25 (MOVED to parent, NOT in children)

Parent gets: [...|25|...]
                 /   \
           [10|20]   [30]
```

**Why the Difference?**

- Leaves: Keys represent actual data, can be duplicated for routing
- Internals: Keys only guide search, appear once in tree

### Maintaining Invariants

**Checklist After Each Insertion:**

1. ✓ All leaves at same depth (balanced)
2. ✓ Keys sorted within each node
3. ✓ Each node (except root) is ≥ half full
4. ✓ Parent keys correctly separate child ranges
5. ✓ Leaf nodes linked correctly
6. ✓ All parent pointers updated

**Common Bugs:**

- Forgetting to update leaf links during split
- Not updating parent pointers for moved children
- Off-by-one errors in split point calculation
- Forgetting to unpin pages after modifications
- Memory leaks from allocating but not tracking new nodes

### Edge Cases to Handle

1. **Inserting into Empty Tree**

   ```
   Create root as leaf node
   Insert key-value pair
   ```

2. **Duplicate Keys**

   ```
   Option A: Reject insertion (unique index)
   Option B: Allow duplicates (non-unique index)
   We'll choose Option A for simplicity
   ```

3. **Tree Height Increases**

   ```
   Only when root splits
   Tree grows from top, not bottom
   Height = max path length from root to any leaf
   ```

4. **Cascading Splits**
   ```
   Leaf split → parent split → grandparent split → ... → new root
   Rare but possible if many insertions happen
   ```

### Performance Analysis

**Time Complexity:**

- **Search for leaf**: O(log n)
- **Insert into leaf**: O(order) for sorted insertion
- **Split**: O(order) for redistributing keys
- **Total**: O(log n) tree traversal + O(order) node operations

**Space Complexity:**

- Temporary arrays during split: O(order)
- Tree height: O(log\_{order} n)

**Amortized Cost:**

- Most insertions don't cause splits
- Splits are rare (< 1% of insertions typically)
- Overall amortized: O(log n)

### Real-World Optimizations (Not Implementing Yet)

1. **Bulk Loading**: Build tree bottom-up for sorted data (faster)
2. **Deferred Splits**: Allow slight overfill, split later
3. **Key Compression**: Store key prefixes to increase fanout
4. **Sibling Redistribution**: Borrow from neighbor before splitting

---

## Outcome

### What You'll Have by End of Day 4

1. **Complete B+ Tree Insertion**:
   - Insert key-value pairs
   - Handle node splits (leaf and internal)
   - Propagate splits up the tree
   - Create new root when needed
2. **Helper Functions**:
   - `insert_into_leaf()`
   - `split_leaf_node()`
   - `split_internal_node()`
   - `insert_into_parent()`
   - `create_new_root()`
3. **Testing Suite**:
   - Insert into empty tree
   - Insert without splits
   - Insert causing leaf split
   - Insert causing cascading splits
   - Insert causing root split (tree growth)
   - Verify tree structure after insertions
4. **Working Storage Engine**:
   - Can insert thousands of records
   - Can search for any inserted key
   - Maintains balanced tree structure
   - Persists to disk

---

## The Project

### What We'll Build Today

**Project: Full B+ Tree Insertion with Splitting**

#### New Methods:

**1. BPlusTree Insertion API**

```
BPlusTree:
  Methods:
    - insert(key: u32, value: Vec<u8>) -> Result<()>
    - delete(key: u32) -> Result<()>  [Optional, for extra credit]
```

**2. Node Modification Methods**

```
BPlusTreeNode:
  Methods:
    - insert_at_leaf(key, value)
    - insert_into_parent(key, child_page_id)
    - split_leaf() -> (BPlusTreeNode, u32)  // Returns new node + promoted key
    - split_internal() -> (BPlusTreeNode, u32)
    - find_insert_position(key) -> usize
```

**3. Tree Helper Methods**

```
BPlusTree:
  Methods:
    - allocate_new_node(is_leaf: bool) -> BPlusTreeNode
    - get_node_mut(page_id: u32) -> &mut BPlusTreeNode
    - write_node(node: &BPlusTreeNode) -> Result<()>
    - is_root(node: &BPlusTreeNode) -> bool
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
    bplus_tree_node.rs
    bplus_tree.rs       ← Extended with insert()

tests/
  test_bplus_insert.rs  ← NEW: Insertion tests
  integration_test.rs   ← NEW: End-to-end tests
```

### Testing Strategy

**Test 1: Insert into Empty Tree**

```
Test: Create tree, insert one key, verify tree structure
Expected: Root is leaf with one key-value pair
```

**Test 2: Insert Multiple Keys (No Split)**

```
Test: Insert 3 keys (order 4), verify all searchable
Expected: Single leaf node with 3 keys
```

**Test 3: Insert Causing Leaf Split**

```
Test: Insert 4 keys (order 4, max 3), verify split occurred
Expected: Root internal node with 2 leaf children
```

**Test 4: Insert Causing Root Split**

```
Test: Fill tree until root splits, verify height increase
Expected: New root created, height = 3
```

**Test 5: Insert in Sorted Order**

```
Test: Insert keys 1,2,3,4,5,6,7,8,9,10
Expected: Balanced tree with all keys searchable
```

**Test 6: Insert in Reverse Order**

```
Test: Insert keys 10,9,8,7,6,5,4,3,2,1
Expected: Balanced tree (same structure as sorted)
```

**Test 7: Insert Random Order**

```
Test: Insert 1000 random keys
Expected: All keys searchable, tree balanced
```

**Test 8: Duplicate Key Rejection**

```
Test: Insert key=5 twice
Expected: Second insert returns error
```

**Test 9: Persistence Test**

```
Test: Insert keys, close tree, reopen, search for keys
Expected: All keys still present after reload
```

**Test 10: Stress Test**

```
Test: Insert 10,000 keys, search for random 1,000
Expected: All searches succeed in < 5 disk I/Os
```

### Key Implementation Tips

#### Managing Page Pins

```
Critical: Always unpin pages when done!

fn insert(&mut self, key: u32, value: Vec<u8>) -> Result<()> {
    let leaf_page_id = self.find_leaf(key);

    // Fetch and pin the page
    let page = self.buffer_pool.fetch_page(leaf_page_id)?;
    let mut node = BPlusTreeNode::from_page(page);

    // ... do work ...

    // MUST unpin before returning!
    self.buffer_pool.unpin_page(leaf_page_id, true)?;

    Ok(())
}
```

#### Handling Temporary Overfull Nodes

```
Strategy: Create temporary vector with extra element

fn split_leaf(&mut self, key: u32, value: Vec<u8>) -> Result<()> {
    // Temporarily overfull
    let mut all_keys = self.keys.clone();
    let mut all_values = self.values.clone();

    // Find insertion position
    let pos = all_keys.binary_search(&key).unwrap_err();
    all_keys.insert(pos, key);
    all_values.insert(pos, value);

    // Now split
    let mid = all_keys.len() / 2;

    self.keys = all_keys[0..mid].to_vec();
    self.values = all_values[0..mid].to_vec();

    let new_node_keys = all_keys[mid..].to_vec();
    let new_node_values = all_values[mid..].to_vec();

    // Create new node with right half
    // ...
}
```

### Debugging Tips

**Visualize Your Tree:**

```
fn print_tree(&self) {
    // Implement level-order traversal
    // Print each level of tree

    Example output:
    Level 0 (root): [50]
    Level 1:        [10|20|30]  [60|70|80]
    Level 2:        [5,7] [10,12] [20,25] [30,35] [60,65] [70,75] [80,85]
}
```

**Validate Tree Invariants:**

```
fn validate_tree(&self) -> Result<()> {
    // Check all paths same length
    // Check keys sorted
    // Check children in correct ranges
    // Check parent pointers correct
    // Check leaf links correct

    Call after each insert during testing!
}
```

### Discussion Questions

1. **Why split at middle, not some other ratio?**

   - Ensures both nodes meet minimum occupancy
   - Balances tree evenly
   - Alternative: 2/3 split (used in some systems)

2. **What if we always split at the end?**

   - One node full, one node minimal
   - Next insert might split again
   - More splits = slower

3. **Why does root have relaxed minimum?**

   - Root can have just 1 key (2 children)
   - Allows tree to shrink after deletions
   - Still maintains balance

4. **How do deletions work? (Preview for future)**
   - Opposite of insertion
   - Borrow from sibling or merge nodes
   - Tree height decreases when root becomes empty

---

## Summary

Today you completed the B+ Tree implementation:

✅ **Insert algorithm** with all cases handled  
✅ **Node splitting** for leaves and internal nodes  
✅ **Split propagation** up to root  
✅ **Tree growth** by creating new root  
✅ **Working storage engine** ready for use!

Tomorrow, we'll **integrate everything**, test thoroughly, and optimize!

---

## Homework / Extended Learning

1. **Implement bulk loading** - Build tree from sorted data (faster than repeated inserts)
2. **Add deletion** - Complete the CRUD operations
3. **Visualize tree** - Create graphical representation of your tree
4. **Benchmark insertion** - Compare sorted vs random insertion performance
5. **Add range scan** - Implement `scan(start_key, end_key)` using leaf links

---

**[← Day 3: B+ Tree Foundation](day_3.md)** | **[Day 5: Integration & Testing →](day_5.md)**
