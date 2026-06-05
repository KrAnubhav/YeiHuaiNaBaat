# 🔗 Linked List — Complete Beginner Notes
> **Goal:** Learn the pattern, not the problem. Master 5 core patterns to solve all 11 NeetCode Linked List problems. Notes are fully self-sufficient — no lecture needed.

---

## 📦 Table of Contents
**PART A — Linked List Fundamentals**
1. [What is a Linked List?](#1-what-is-a-linked-list)
2. [The ListNode Class (Always the Same)](#2-the-listnode-class)
3. [Core Operations — Traversal, Insert, Delete](#3-core-operations)
4. [Doubly Linked List](#4-doubly-linked-list)
5. [The Dummy Node Trick](#5-the-dummy-node-trick)

**PART B — 5 Core Patterns**
6. [Pattern 1 — Two Pointers (Fast & Slow)](#pattern-1--two-pointers-fast--slow)
7. [Pattern 2 — In-Place Pointer Reversal](#pattern-2--in-place-pointer-reversal)
8. [Pattern 3 — Merge / Multi-List Operations](#pattern-3--merge--multi-list-operations)
9. [Pattern 4 — HashMap for Node Mapping](#pattern-4--hashmap-for-node-mapping)
10. [Pattern 5 — Design with DLL + HashMap](#pattern-5--design-with-dll--hashmap)
11. [How to Identify the Pattern (Decision Tree)](#how-to-identify-the-pattern)
12. [Problem → Pattern Mapping](#problem--pattern-mapping)
13. [Complexity Cheat Sheet](#complexity-cheat-sheet)
14. [Common Mistakes to Avoid](#common-mistakes-to-avoid)

---

# PART A — Linked List Fundamentals

---

## 1. What is a Linked List?

A Linked List is a sequence of **nodes** where each node holds a **value** and a **pointer to the next node**. Unlike arrays, nodes are NOT stored in contiguous memory — they are scattered, connected only by pointers.

### Linked List vs Array

```
ARRAY: values stored in adjacent memory blocks — random access O(1)
┌───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │   arr[2] = 3 instantly ✅
└───┴───┴───┴───┴───┘

LINKED LIST: values connected by pointers — must traverse O(n)
┌───┬──┐   ┌───┬──┐   ┌───┬──┐   ┌───┬────┐
│ 1 │ ─┼──▶│ 2 │ ─┼──▶│ 3 │ ─┼──▶│ 4 │null│
└───┴──┘   └───┴──┘   └───┴──┘   └───┴────┘
  head
```

| Operation | Array | Linked List |
|-----------|-------|-------------|
| Access by index | O(1) | O(n) |
| Insert at head | O(n) — shift all | O(1) |
| Insert at tail | O(1) amortised | O(n) without tail pointer |
| Delete from head | O(n) — shift all | O(1) |
| Search | O(n) | O(n) |

### Types of Linked Lists

```
Singly Linked List (most common in problems):
[1] → [2] → [3] → [4] → null

Doubly Linked List (used in LRU Cache):
null ← [1] ⇄ [2] ⇄ [3] ⇄ [4] → null
         prev/next  prev/next

Circular Linked List:
[1] → [2] → [3] → [4] → back to [1]
```

---

## 2. The ListNode Class

> Always given in LeetCode — memorise the structure.

```java
// Singly Linked List Node
public class ListNode {
    int val;
    ListNode next;

    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}
```

### How to Build a List Mentally

```java
// Building: 1 → 2 → 3 → null

ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);
// head.next.next.next is null automatically

// How to read it:
// head.val       = 1
// head.next.val  = 2
// head.next.next.val = 3
// head.next.next.next = null
```

---

## 3. Core Operations

### Traversal — Visiting Every Node

```java
// Pattern: use a pointer `curr`, move with curr = curr.next
void traverse(ListNode head) {
    ListNode curr = head;           // start at head
    while (curr != null) {          // stop when we fall off the end
        System.out.println(curr.val);
        curr = curr.next;           // move to next node
    }
}

// Trace on [1 → 2 → 3]:
// curr = 1 (not null) → print 1 → curr = 2
// curr = 2 (not null) → print 2 → curr = 3
// curr = 3 (not null) → print 3 → curr = null
// curr = null → STOP
```

> ⚠️ **The golden rule: ALWAYS check `curr != null` before `curr.next` to avoid NullPointerException.**

### Insert at Head — O(1)

```java
ListNode insertAtHead(ListNode head, int val) {
    ListNode newNode = new ListNode(val);
    newNode.next = head;   // new node points to old head
    return newNode;        // new node IS the new head
}

// Before: [2] → [3] → null
// After:  [1] → [2] → [3] → null  (inserted 1 at head)
```

### Delete a Node by Value

```java
ListNode deleteByVal(ListNode head, int val) {
    // Edge case: delete head itself
    if (head == null) return null;
    if (head.val == val) return head.next;  // skip head

    ListNode curr = head;
    while (curr.next != null) {
        if (curr.next.val == val) {
            curr.next = curr.next.next;  // skip the node to delete
            break;
        }
        curr = curr.next;
    }
    return head;
}

// Delete 3 from [1 → 2 → 3 → 4]:
// curr=1, curr.next=2, not 3
// curr=2, curr.next=3 ← found! curr.next = 4 (skip 3)
// Result: [1 → 2 → 4]
```

**Visual of deletion:**
```
BEFORE:
curr         target
 ↓            ↓
[2] → [3] → [4] → null

Operation: curr.next = curr.next.next
           curr.next = [4]  (skipping [3])

AFTER:
curr
 ↓
[2] → [4] → null   ([3] is now unreachable, garbage collected)
```

---

## 4. Doubly Linked List

A DLL has both `prev` and `next` pointers, allowing traversal in **both directions**.

```java
class DLLNode {
    int val;
    DLLNode prev;   // points to previous node
    DLLNode next;   // points to next node

    DLLNode(int val) { this.val = val; }
}
```

```
null ← [1] ⇄ [2] ⇄ [3] ⇄ [4] → null
       head                 tail
```

### Insert in DLL (after a given node)

```java
void insertAfter(DLLNode node, int val) {
    DLLNode newNode = new DLLNode(val);

    newNode.next = node.next;   // new node's next = node's old next
    newNode.prev = node;        // new node's prev = node

    if (node.next != null)
        node.next.prev = newNode;  // old next's prev = new node

    node.next = newNode;           // node's next = new node
}
```

### Delete a Node from DLL

```java
void deleteNode(DLLNode node) {
    if (node.prev != null) node.prev.next = node.next;
    if (node.next != null) node.next.prev = node.prev;
    // node is now disconnected
}

// Visual:
// BEFORE: [A] ⇄ [B] ⇄ [C]   (delete B)
// A.next = C,  C.prev = A
// AFTER:  [A] ⇄ [C]
```

---

## 5. The Dummy Node Trick

> **One of the most important tricks in Linked List problems.**

A dummy node is a fake node added **before the head** so you never have to handle "what if we modify the head?" as a special case.

```java
ListNode dummy = new ListNode(0);
dummy.next = head;               // dummy → [real head] → ...

// Do all operations using dummy as anchor
// At the end:
return dummy.next;               // real head (dummy's next)
```

**Why it helps:**
```
Without dummy:
  "If I need to delete the head, special case!"
  "If the list is empty, special case!"

With dummy:
  dummy → head → ...
  The dummy is NEVER deleted, so you always have a safe starting point.
  No special cases needed!
```

---

# PART B — 5 Core Patterns

---

## PATTERN 1 — Two Pointers (Fast & Slow)

### When to Use
- Detect a **cycle** in the list
- Find the **middle** of the list
- Find the **Nth node from the end**
- Find the **start of a cycle**
- "Find duplicate" problems that map to cycle detection

### The Core Ideas

**Slow & Fast Pointers:**
```
slow moves 1 step at a time
fast moves 2 steps at a time

If there IS a cycle:  fast will eventually CATCH slow (they meet inside the cycle)
If there is NO cycle: fast will reach null first

If list has n nodes:
  slow reaches middle when fast reaches end
  → slow is at the middle!
```

**N-ahead Pointer:**
```
Move fast pointer N steps ahead.
Then move BOTH slow and fast one step at a time.
When fast reaches the end, slow is N steps from the end.
```

---

### 🔑 PATTERN 1A — Detect Cycle (Floyd's Algorithm)

```java
boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;        // moves 1 step
        fast = fast.next.next;   // moves 2 steps

        if (slow == fast) return true;  // they met → CYCLE!
    }
    return false;  // fast reached null → NO cycle
}
```

**Visual — List WITH a cycle:**
```
1 → 2 → 3 → 4 → 5
            ↑       ↓
            └── 6 ←─┘

Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Step 3: slow=4, fast=4  ← MEET! Cycle detected.
```

**Visual — List WITHOUT a cycle:**
```
1 → 2 → 3 → 4 → null

Step 1: slow=2, fast=3
Step 2: slow=3, fast=null ← fast.next is null, exit loop → no cycle
```

---

### Example A — Linked List Cycle

```java
class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) return true;
        }
        return false;
    }
}
// Time: O(n)  |  Space: O(1)
```

---

### 🔑 PATTERN 1B — Find Start of Cycle

**Math insight (Striver L17 proof):**
```
When slow and fast meet inside the cycle, the distance from:
  [head]  to  [cycle start]  ==  [meeting point]  to  [cycle start]

So: reset one pointer to head.
Move BOTH one step at a time.
They will meet at the CYCLE START.
```

```java
ListNode detectCycle(ListNode head) {
    ListNode slow = head, fast = head;

    // Phase 1: find meeting point
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) break;
    }

    // No cycle
    if (fast == null || fast.next == null) return null;

    // Phase 2: find cycle start
    slow = head;               // reset slow to head
    while (slow != fast) {     // move both one step at a time
        slow = slow.next;
        fast = fast.next;
    }
    return slow;               // meeting point = cycle start
}
// Time: O(n)  |  Space: O(1)
```

---

### 🔑 PATTERN 1C — Find Middle of Linked List

```java
ListNode findMiddle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;  // slow is at the middle
}

// [1 → 2 → 3 → 4 → 5]:
// Step 1: slow=2, fast=3
// Step 2: slow=3, fast=5
// fast.next = null → stop. slow=3 = MIDDLE ✅

// [1 → 2 → 3 → 4]:
// Step 1: slow=2, fast=3
// Step 2: slow=3, fast=null(fast=4, fast.next=null) → stop. slow=3 ✅
```

---

### Example B — Remove Nth Node From End of List

**Problem:** Remove the Nth node from the end of the list.

**Strategy:**
```
Move fast pointer N steps ahead.
Then move both slow and fast one step at a time.
When fast reaches the END (null), slow is just BEFORE the Nth node from end.

Why? fast is N steps ahead → when fast falls off (null), slow is N behind the end.
```

```
Remove 2nd from end of [1 → 2 → 3 → 4 → 5]:

Using dummy node: dummy → [1 → 2 → 3 → 4 → 5]
                  slow = dummy, fast = dummy

Step 1: Move fast N=2 steps ahead:
  fast = dummy.next = 1 → fast = 1.next = 2
  slow = dummy, fast = node(2)

Step 2: Move both until fast.next = null:
  slow=1, fast=3
  slow=2, fast=4
  slow=3, fast=5
  fast.next = null → STOP

slow = node(3), slow.next = node(4) ← this is the node to remove
slow.next = slow.next.next (skip node 4)

Result: [1 → 2 → 3 → 5]
```

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode slow = dummy, fast = dummy;

        // Move fast n+1 steps ahead (n+1 so slow stops BEFORE the target node)
        for (int i = 0; i <= n; i++) {
            fast = fast.next;
        }

        // Move both until fast is null
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }

        // slow.next is the node to remove
        slow.next = slow.next.next;

        return dummy.next;
    }
}
// Time: O(n)  |  Space: O(1)
```

---

### Example C — Find The Duplicate Number

**Problem:** Array of n+1 integers, each in range [1, n]. Exactly one duplicate. Find it.

**Key Insight — This IS a Linked List Cycle problem!**
```
Treat the array as a linked list:
  Index = node, Value = pointer to next node
  nums[0] → nums[nums[0]] → nums[nums[nums[0]]] → ...

Since a value repeats → two different indices point to the same value
→ the repeated value is the CYCLE START in the implicit linked list!

Example: nums = [1, 3, 4, 2, 2]
  Index:   0  1  2  3  4
  Value:   1  3  4  2  2

  Path: 0 → nums[0]=1 → nums[1]=3 → nums[3]=2 → nums[2]=4 → nums[4]=2 → ...
                                                              ↑ cycle!
  2 is the cycle start → 2 is the duplicate!

Use Floyd's algorithm on the array as if it were a linked list.
```

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];

        // Phase 1: find meeting point
        do {
            slow = nums[slow];         // one step (follow pointer)
            fast = nums[nums[fast]];   // two steps
        } while (slow != fast);

        // Phase 2: find cycle start (= duplicate number)
        slow = nums[0];                // reset slow to beginning
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];         // both move one step now
        }
        return slow;  // duplicate found!
    }
}
// Time: O(n)  |  Space: O(1)
// Note: do-while because slow==fast initially (both start at nums[0])
```

---

## PATTERN 2 — In-Place Pointer Reversal

### When to Use
- Reverse the whole list or a portion of it
- Reorder a list by combining reversed halves
- Process a list in reverse without extra space
- Keywords: "reverse", "reorder", "K group", "palindrome"

### The Core Idea
```
Reversing is just flipping pointers one by one.
You need THREE pointers: prev, curr, next

BEFORE: null ← [1] ← [2] ← [3] ← [4]
         (we want this)

We have: [1] → [2] → [3] → [4] → null

Process:
prev = null, curr = 1
  save next = 2
  curr.next = prev (1 points to null)
  prev = curr (prev moves to 1)
  curr = next (curr moves to 2)

prev = 1, curr = 2
  save next = 3
  curr.next = prev (2 points to 1)
  prev = curr (prev moves to 2)
  curr = next (curr moves to 3)

...continue until curr = null
prev = 4 = NEW HEAD
```

### 🔑 PATTERN 2 TEMPLATE — Iterative Reversal

```java
ListNode reverse(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;

    while (curr != null) {
        ListNode next = curr.next;  // SAVE next before breaking link
        curr.next = prev;           // FLIP the pointer
        prev = curr;                // advance prev
        curr = next;                // advance curr
    }
    return prev;  // prev is now the new head
}
```

**Step-by-step on [1 → 2 → 3 → null]:**
```
Initial:    prev=null, curr=1
Iteration 1: next=2, 1→null, prev=1, curr=2
             null ← [1]   [2] → [3] → null
Iteration 2: next=3, 2→1,  prev=2, curr=3
             null ← [1] ← [2]   [3] → null
Iteration 3: next=null, 3→2, prev=3, curr=null
             null ← [1] ← [2] ← [3]
curr=null → exit loop, return prev=3 = NEW HEAD
```

---

### Example A — Reverse Linked List (Iterative + Recursive)

```java
class Solution {
    // ITERATIVE — preferred, O(1) space
    public ListNode reverseList(ListNode head) {
        ListNode prev = null, curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }

    // RECURSIVE — elegant but uses O(n) call stack
    public ListNode reverseListRecursive(ListNode head) {
        // Base case: empty or single node
        if (head == null || head.next == null) return head;

        // Reverse the rest of the list
        ListNode newHead = reverseListRecursive(head.next);

        // Make head.next point back to head
        head.next.next = head;
        head.next = null;       // disconnect head from the (now reversed) list

        return newHead;
    }
}
// Time: O(n)  |  Space: O(1) iterative, O(n) recursive
```

**Recursive trace for [1 → 2 → 3]:**
```
reverse(1)
  → reverse(2)
      → reverse(3)
          → reverse(null) ← base case, return 3
      ← 3.next.next = 3 means 3.next (null) → no, wait:
         head=2, head.next=3
         head.next.next = head → 3.next = 2
         head.next = null → 2.next = null
         return newHead=3
  ← head=1, head.next=2
     head.next.next = head → 2.next = 1
     head.next = null → 1.next = null
     return newHead=3

Result: 3 → 2 → 1 → null ✅
```

---

### Example B — Reorder List

**Problem:** Reorder `[1 → 2 → 3 → 4 → 5]` to `[1 → 5 → 2 → 4 → 3]`

**Pattern:** 1st half interleaved with REVERSED 2nd half.

**Three-step approach:**
```
Step 1: Find middle using slow/fast pointers
  [1 → 2 → 3] | [4 → 5]
                ↑ middle

Step 2: Reverse the second half
  [4 → 5] becomes [5 → 4]

Step 3: Merge the two halves alternately
  [1 → 2 → 3] + [5 → 4]
  = 1 → 5 → 2 → 4 → 3
```

```java
class Solution {
    public void reorderList(ListNode head) {
        // Step 1: Find the middle
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        // slow is now at the middle

        // Step 2: Reverse the second half
        ListNode secondHalf = reverse(slow.next);
        slow.next = null;  // cut the list in half!

        // Step 3: Merge alternately
        ListNode first = head, second = secondHalf;
        while (second != null) {
            ListNode tmp1 = first.next;   // save first's next
            ListNode tmp2 = second.next;  // save second's next

            first.next = second;          // first → second
            second.next = tmp1;           // second → first's old next

            first = tmp1;                 // advance first
            second = tmp2;                // advance second
        }
    }

    private ListNode reverse(ListNode head) {
        ListNode prev = null, curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
// Time: O(n)  |  Space: O(1)
```

**Step 3 visual merge:**
```
first: [1] → [2] → [3]
second: [5] → [4]

Iteration 1:
  tmp1 = 2, tmp2 = 4
  1.next = 5, 5.next = 2
  List so far: 1 → 5 → 2 → 3
  first = 2, second = 4

Iteration 2:
  tmp1 = 3, tmp2 = null
  2.next = 4, 4.next = 3
  List so far: 1 → 5 → 2 → 4 → 3
  first = 3, second = null → stop

Result: [1 → 5 → 2 → 4 → 3] ✅
```

---

### Example C — Reverse Nodes in K Group

**Problem:** Reverse every K consecutive nodes. If remaining nodes < K, leave them as-is.

```
Input:  [1 → 2 → 3 → 4 → 5], K=2
Output: [2 → 1 → 4 → 3 → 5]
         ↑reversed↑  ↑reversed↑  ↑ (only 1 left, K=2, leave as-is)
```

**Strategy:**
```
1. Check if there are at least K nodes left (if not, stop)
2. Reverse the K nodes
3. Connect to the result of processing the REST of the list recursively
4. Return the new head of this K-group (which was the K-th node before reversal)
```

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        // Check if there are at least k nodes left
        ListNode check = head;
        int count = 0;
        while (check != null && count < k) {
            check = check.next;
            count++;
        }
        if (count < k) return head;  // fewer than k nodes left, don't reverse

        // Reverse k nodes
        ListNode prev = null, curr = head;
        for (int i = 0; i < k; i++) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        // After reversal:
        //   prev = new head of this k-group
        //   head = old head = now the TAIL of this reversed group
        //   curr = first node of the NEXT group

        // Connect tail of reversed group to result of next group
        head.next = reverseKGroup(curr, k);  // head is now the tail!

        return prev;  // prev is the new head of this group
    }
}
// Time: O(n)  |  Space: O(n/k) recursion depth
```

**Trace for [1→2→3→4→5], k=2:**
```
reverseKGroup([1→2→3→4→5], 2):
  count=2 ≥ k=2, proceed
  Reverse [1,2]: prev=2, head=1, curr=3
  1.next = reverseKGroup([3→4→5], 2) → returns [4→3→5]
  1.next = [4→3→5]
  return prev=2

reverseKGroup([3→4→5], 2):
  count=2 ≥ k=2, proceed
  Reverse [3,4]: prev=4, head=3, curr=5
  3.next = reverseKGroup([5], 2) → count=1 < k=2 → return [5] as-is
  3.next = [5]
  return prev=4

Final: 2 → 1 → 4 → 3 → 5 ✅
```

---

## PATTERN 3 — Merge / Multi-List Operations

### When to Use
- Combining two or more sorted lists
- Performing arithmetic on numbers stored in lists
- Keywords: "merge sorted", "add numbers", "merge K lists"

### The Core Idea — Dummy Node for Clean Merging

```
Always use a DUMMY NODE as the head of the result list.
Use a `curr` pointer to build the list, appending to curr.next.
Return dummy.next at the end.

dummy → [?] → [?] → [?]  ← building result
                    ↑ curr
```

---

### Example A — Merge Two Sorted Lists

**Input:** `list1 = [1→2→4]`, `list2 = [1→3→4]`
**Output:** `[1→1→2→3→4→4]`

```
Approach: compare heads of both lists, always pick the smaller one.

l1: [1] → [2] → [4]
l2: [1] → [3] → [4]

Step 1: l1.val=1, l2.val=1. Pick l1. dummy→[1], advance l1
Step 2: l1.val=2, l2.val=1. Pick l2. dummy→[1→1], advance l2
Step 3: l1.val=2, l2.val=3. Pick l1. dummy→[1→1→2], advance l1
Step 4: l1.val=4, l2.val=3. Pick l2. dummy→[1→1→2→3], advance l2
Step 5: l1.val=4, l2.val=4. Pick l1. dummy→[1→1→2→3→4], advance l1
l1 is null → append rest of l2: dummy→[1→1→2→3→4→4]
```

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;

        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                curr.next = list1;  // attach smaller node
                list1 = list1.next; // advance list1
            } else {
                curr.next = list2;
                list2 = list2.next;
            }
            curr = curr.next;  // advance curr in result
        }

        // Attach the remaining non-null list
        curr.next = (list1 != null) ? list1 : list2;

        return dummy.next;
    }
}
// Time: O(m + n)  |  Space: O(1)
```

---

### Example B — Add Two Numbers

**Problem:** Two non-empty linked lists represent two non-negative integers in **reverse order**. Add them and return as a linked list.

```
l1 = [2 → 4 → 3] represents 342
l2 = [5 → 6 → 4] represents 465
Answer: 807 → as list [7 → 0 → 8]
```

**Key Insight:**
```
Since the list is REVERSE order, the head is the ONES digit.
This means we can add from head to tail directly — same as adding numbers right-to-left.
Just handle the CARRY digit.

Position 0: 2+5=7, carry=0 → digit=7
Position 1: 4+6=10, carry=1 → digit=0
Position 2: 3+4+1(carry)=8, carry=0 → digit=8
Result: [7 → 0 → 8]
```

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        int carry = 0;

        // Continue while either list has nodes OR there's a carry left
        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;                           // start with carry

            if (l1 != null) { sum += l1.val; l1 = l1.next; }  // add l1 digit
            if (l2 != null) { sum += l2.val; l2 = l2.next; }  // add l2 digit

            carry = sum / 10;          // new carry (0 or 1)
            int digit = sum % 10;      // current digit

            curr.next = new ListNode(digit);
            curr = curr.next;
        }

        return dummy.next;
    }
}
// Time: O(max(m, n))  |  Space: O(max(m, n)) — for result list
```

---

### Example C — Merge K Sorted Lists

**Problem:** Merge K sorted linked lists into one sorted list.

**Two approaches:**

**Approach 1 — Min-Heap (Priority Queue)**
```
Insert the HEAD of all K lists into a min-heap.
Always poll the smallest, add to result.
Then push that node's NEXT into the heap.
Heap always has at most K elements.
```

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // Min-heap: smallest val at top
        PriorityQueue<ListNode> heap = new PriorityQueue<>(
            (a, b) -> a.val - b.val  // compare by value
        );

        // Add head of every non-null list to heap
        for (ListNode node : lists) {
            if (node != null) heap.offer(node);
        }

        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;

        while (!heap.isEmpty()) {
            ListNode smallest = heap.poll();  // get smallest node
            curr.next = smallest;             // add to result
            curr = curr.next;

            if (smallest.next != null)
                heap.offer(smallest.next);    // add next from same list
        }

        return dummy.next;
    }
}
// Time: O(n log k) — n total nodes, log k for heap operations
// Space: O(k) — heap holds at most k nodes
```

**Approach 2 — Divide and Conquer**
```
Merge lists in pairs: [l1,l2,l3,l4] → [merge(l1,l2), merge(l3,l4)] → merge all
Like merge sort on lists.
```

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) return null;
        return divideAndConquer(lists, 0, lists.length - 1);
    }

    private ListNode divideAndConquer(ListNode[] lists, int left, int right) {
        if (left == right) return lists[left];  // single list
        int mid = left + (right - left) / 2;
        ListNode l1 = divideAndConquer(lists, left, mid);
        ListNode l2 = divideAndConquer(lists, mid + 1, right);
        return mergeTwoLists(l1, l2);  // reuse Pattern 3A
    }

    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0), curr = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) { curr.next = l1; l1 = l1.next; }
            else                  { curr.next = l2; l2 = l2.next; }
            curr = curr.next;
        }
        curr.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
// Time: O(n log k)  |  Space: O(log k) recursion depth
```

---

## PATTERN 4 — HashMap for Node Mapping

### When to Use
- Need to map each original node to its corresponding COPY node
- Problems involving "random pointer" or "deep copy"
- The problem has a non-standard pointer that creates a complex structure

### The Core Idea
```
When you can't just traverse linearly because of random/extra pointers:
  1. First pass: CREATE a copy of every node (just the value, no pointers yet)
                 Store: original node → copy node in a HashMap
  2. Second pass: Set all pointers by looking up the HashMap

This separates "create nodes" from "connect nodes".
```

---

### Example — Copy List With Random Pointer

**Problem:** Each node has `val`, `next`, and `random` (points to ANY node or null). Create a deep copy.

```
Original:
[1] → [2] → [3] → null
 ↓random      ↓random
[3]          [1]

Copy must be:
[1'] → [2'] → [3'] → null
 ↓random        ↓random
[3']            [1']
(completely new nodes, no references to originals)
```

```java
class Node {
    int val;
    Node next;
    Node random;
    Node(int val) { this.val = val; }
}

class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;

        // HashMap: original node → its copy
        Map<Node, Node> map = new HashMap<>();

        // PASS 1: Create all copy nodes (values only, no pointers)
        Node curr = head;
        while (curr != null) {
            map.put(curr, new Node(curr.val));  // create copy with same value
            curr = curr.next;
        }

        // PASS 2: Set next and random pointers for all copies
        curr = head;
        while (curr != null) {
            Node copy = map.get(curr);          // get this node's copy

            // set copy's next = copy of original's next
            copy.next = map.get(curr.next);     // null if curr.next is null ✅

            // set copy's random = copy of original's random
            copy.random = map.get(curr.random); // null if curr.random is null ✅

            curr = curr.next;
        }

        return map.get(head);  // copy of original head
    }
}
// Time: O(n)  |  Space: O(n) — HashMap
```

**Why `map.get(curr.next)` works for null:**
```
HashMap.get(null) returns null → perfectly handles nodes with no next/random!
No special null checks needed.
```

---

## PATTERN 5 — Design with DLL + HashMap

### When to Use
- Design a cache or data structure requiring O(1) for both get AND update
- Keywords: "LRU", "most recently used", "O(1) operations"

### The Core Idea — Why DLL + HashMap?

```
LRU Cache needs:
  1. O(1) GET: "Is key X in cache?" → HashMap gives O(1) lookup
  2. O(1) INSERT: "Add key X" → need to add to a list in O(1)
  3. O(1) DELETE: "Remove least recently used" → need to remove from list in O(1)
  4. ORDER: Need to know which item was used LEAST recently

Array → O(n) delete (shift elements) ❌
Singly LL → O(n) delete (need to find prev node) ❌
DLL → O(1) delete IF you have a pointer to the node ✅

HashMap stores: key → DLL node (so we can access the node directly!)
DLL maintains order: head = least recently used, tail = most recently used
```

**The structure:**
```
HashMap: { key → DLL Node }

DLL (doubly linked, always with dummy head and tail):
  [dummy_head] ⇄ [LRU item] ⇄ ... ⇄ [MRU item] ⇄ [dummy_tail]
                   ↑                                ↑
               least recently used       most recently used
```

**Operations:**
```
GET(key):
  1. Found in HashMap? → Move node to TAIL (it's now most recently used)
  2. Not found? → return -1

PUT(key, val):
  1. Already exists? → Update value, move to TAIL
  2. New key:
     a. Add to HashMap + insert at TAIL
     b. If over capacity → remove node at HEAD (LRU) + remove from HashMap
```

---

### Example — LRU Cache

```java
class LRUCache {
    // DLL node
    class Node {
        int key, val;
        Node prev, next;
        Node(int k, int v) { key = k; val = v; }
    }

    private int capacity;
    private Map<Integer, Node> map;  // key → Node
    private Node head, tail;         // dummy nodes

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();

        // Dummy head (LRU end) and tail (MRU end)
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!map.containsKey(key)) return -1;

        Node node = map.get(key);
        remove(node);       // remove from current position
        insertAtTail(node); // re-insert at MRU end (tail)
        return node.val;
    }

    public void put(int key, int value) {
        if (map.containsKey(key)) {
            remove(map.get(key));   // remove old node
        }
        Node node = new Node(key, value);
        map.put(key, node);
        insertAtTail(node);         // add as most recently used

        if (map.size() > capacity) {
            // Remove the LRU node (just after dummy head)
            Node lru = head.next;
            remove(lru);
            map.remove(lru.key);
        }
    }

    // Remove a node from the DLL
    private void remove(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    // Insert a node just before dummy tail (= most recently used position)
    private void insertAtTail(Node node) {
        node.prev = tail.prev;
        node.next = tail;
        tail.prev.next = node;
        tail.prev = node;
    }
}
// Time: O(1) for both get and put
// Space: O(capacity) — HashMap + DLL

/*
State example (capacity=3):
put(1,1): head ⇄ [1] ⇄ tail         map={1:node1}
put(2,2): head ⇄ [1] ⇄ [2] ⇄ tail   map={1,2}
put(3,3): head ⇄ [1] ⇄ [2] ⇄ [3] ⇄ tail  map={1,2,3}
get(1):   head ⇄ [2] ⇄ [3] ⇄ [1] ⇄ tail  (1 moved to MRU end)
put(4,4): head ⇄ [3] ⇄ [1] ⇄ [4] ⇄ tail  (2 was LRU, evicted)
*/
```

**Visual — insertAtTail:**
```
BEFORE: ... ⇄ [X] ⇄ [tail]

insertAtTail(new_node):
  new_node.prev = X
  new_node.next = tail
  X.next = new_node
  tail.prev = new_node

AFTER: ... ⇄ [X] ⇄ [new_node] ⇄ [tail]
```

---

## How to Identify the Pattern

```
READ the problem. Ask these questions in order:

START
  │
  ▼
Does the problem involve DESIGNING a data structure (LRU Cache etc)?
  ├── YES → PATTERN 5 (DLL + HashMap)
  │         DLL for O(1) order operations, HashMap for O(1) lookup
  └── NO ↓

Does the problem require DEEP COPYING a list with non-standard pointers?
  ├── YES → PATTERN 4 (HashMap for node mapping)
  │         Pass 1: create all copies, Pass 2: wire all pointers
  └── NO ↓

Does the problem involve MERGING or COMBINING multiple lists?
OR doing arithmetic (add numbers, carry)?
  ├── YES → PATTERN 3 (Merge / Multi-List)
  │         Always use dummy node + curr pointer
  │         K lists? → Min-heap or divide & conquer
  └── NO ↓

Does the problem ask to REVERSE some or all of the list?
OR reorder / restructure pointer direction?
  ├── YES → PATTERN 2 (In-Place Pointer Reversal)
  │         Three pointers: prev, curr, next
  │         K group? → reverse K, recurse on rest
  │         Reorder? → find middle + reverse second half + merge
  └── NO ↓

Default → PATTERN 1 (Fast & Slow Two Pointers)
  Has a cycle? → slow/fast, meet inside cycle
  Find cycle start? → Phase 2: reset slow to head, move both 1 step
  Find middle? → fast moves 2, slow moves 1
  Nth from end? → fast goes N ahead, then both move together
  Duplicate in array? → treat array as linked list, Floyd's algorithm
```

---

## Problem → Pattern Mapping

| Problem | Pattern | Key Trick |
|---------|---------|-----------|
| Reverse Linked List | Pattern 2 | Three pointers: prev, curr, next |
| Merge Two Sorted Lists | Pattern 3 | Dummy node + compare heads |
| Linked List Cycle | Pattern 1A | slow/fast meet → cycle exists |
| Reorder List | Pattern 1C + 2 | Find middle + reverse + merge |
| Remove Nth Node From End | Pattern 1 (N-ahead) | fast goes N+1 ahead, slow stops before target |
| Copy List With Random Pointer | Pattern 4 | Two-pass: create all copies, then wire pointers |
| Add Two Numbers | Pattern 3 | Dummy node + carry variable |
| Find The Duplicate Number | Pattern 1A | Array as LL, Floyd's cycle = duplicate |
| LRU Cache | Pattern 5 | DLL + HashMap, dummy head/tail |
| Merge K Sorted Lists | Pattern 3 | Min-heap (log k per node) or D&C |
| Reverse Nodes in K Group | Pattern 2 | Reverse K, recurse on rest, reconnect |

---

## Complexity Cheat Sheet

| Problem | Time | Space | Notes |
|---------|------|-------|-------|
| Reverse Linked List | O(n) | O(1) iterative / O(n) recursive | Iterative preferred |
| Merge Two Sorted Lists | O(m+n) | O(1) | No new nodes created |
| Linked List Cycle | O(n) | O(1) | Floyd's two-pointer |
| Reorder List | O(n) | O(1) | Find mid + reverse + merge |
| Remove Nth From End | O(n) | O(1) | One pass with two pointers |
| Copy List With Random Pointer | O(n) | O(n) | HashMap for node mapping |
| Add Two Numbers | O(max(m,n)) | O(max(m,n)) | Output list has max(m,n)+1 nodes |
| Find The Duplicate Number | O(n) | O(1) | Floyd's on array-as-LL |
| LRU Cache | O(1) per op | O(capacity) | DLL + HashMap |
| Merge K Sorted Lists | O(n log k) | O(k) | n=total nodes, k=num lists |
| Reverse Nodes in K Group | O(n) | O(n/k) | Recursion depth = n/k |

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Accessing `.next` without null check
```java
// WRONG — NullPointerException if curr is null!
while (curr.next != null) { ... }

// CORRECT — always check curr first
while (curr != null && curr.next != null) { ... }
// OR for fast pointer (two steps):
while (fast != null && fast.next != null) { ... }
```

### ❌ Mistake 2: Not saving `next` before breaking the link
```java
// WRONG — you lose curr.next after flipping!
curr.next = prev;
curr = curr.next;   // ← curr.next is now prev (old), not the next node!

// CORRECT — save next BEFORE flipping
ListNode next = curr.next;  // save FIRST
curr.next = prev;            // then flip
prev = curr;
curr = next;                 // use saved next
```

### ❌ Mistake 3: Forgetting to cut the list in Reorder List
```java
// WRONG — after finding middle, first half still points to second half
ListNode secondHalf = reverse(slow.next);
// Didn't cut! First half's tail still points into reversed second half = cycle!

// CORRECT — cut the list at the middle
ListNode secondHalf = reverse(slow.next);
slow.next = null;  // ← ESSENTIAL: cut the link!
```

### ❌ Mistake 4: Not copying result list before returning in HashMap pattern
```java
// WRONG — returning original node, not the copy
return head;

// CORRECT — return the copy node from the map
return map.get(head);
```

### ❌ Mistake 5: In LRU Cache — forgetting to remove before re-inserting
```java
// WRONG — on GET, if key exists, just inserting at tail creates duplicate nodes
if (map.containsKey(key)) {
    insertAtTail(map.get(key));  // ← node is still in its old position too!
}

// CORRECT — remove from current position, THEN insert at tail
if (map.containsKey(key)) {
    remove(map.get(key));       // remove from old position
    insertAtTail(map.get(key)); // insert at MRU position
}
```

### ❌ Mistake 6: In Remove Nth Node — off-by-one in how far to advance fast
```java
// WRONG for n=1 — slow ends UP on the node to delete, not BEFORE it
for (int i = 0; i < n; i++) fast = fast.next;

// CORRECT — advance fast n+1 times (slow stops one BEFORE target)
// OR: start both from dummy node and advance fast n+1 times
// Easiest: use dummy node + advance fast n+1 steps from dummy
ListNode dummy = new ListNode(0);
dummy.next = head;
ListNode slow = dummy, fast = dummy;
for (int i = 0; i <= n; i++) fast = fast.next;  // n+1 steps from dummy
```

### ❌ Mistake 7: In K Group Reversal — not checking if K nodes exist before reversing
```java
// WRONG — might reverse a partial group incorrectly
void reverseKGroup(ListNode head, int k) {
    // jumping straight to reversal without checking count
}

// CORRECT — always count first
int count = 0;
ListNode check = head;
while (check != null && count < k) { check = check.next; count++; }
if (count < k) return head;  // fewer than k nodes, return as-is
```

---

## 📋 Quick Reference Card

```
┌──────────────────────────────────────────────────────────────────────┐
│                  LINKED LIST PATTERN QUICK CARD                      │
├────────────────────┬─────────────────────────────────────────────────┤
│ GOLDEN RULE        │ ALWAYS check null before accessing .next        │
│                    │ while (curr != null && curr.next != null)       │
├────────────────────┼─────────────────────────────────────────────────┤
│ DUMMY NODE         │ ListNode dummy = new ListNode(0);               │
│                    │ dummy.next = head;  return dummy.next;          │
│                    │ Use whenever modifying the head is possible     │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 1          │ slow/fast two pointers                          │
│ Two Pointers       │ Cycle: fast catches slow inside loop            │
│                    │ Middle: fast=2 steps, slow=1 step               │
│                    │ Nth from end: fast goes N+1 ahead (from dummy)  │
│                    │ Cycle start: after meeting, reset slow to head  │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 2          │ Reversal: prev=null, curr=head                  │
│ Pointer Reversal   │ Save next → flip pointer → advance both         │
│                    │ K group: reverse K, recurse rest, reconnect     │
│                    │ Reorder: find mid + reverse 2nd half + merge    │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 3          │ Dummy node always                               │
│ Merge / Multi-List │ Merge 2: compare heads, pick smaller            │
│                    │ Add numbers: carry = sum/10, digit = sum%10     │
│                    │ Merge K: min-heap (poll + push next)            │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 4          │ Two passes: create copies → wire pointers       │
│ HashMap Mapping    │ map.get(null) returns null — handles null safe  │
│                    │ return map.get(head), NOT head                  │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 5          │ HashMap (key→node) + DLL with dummy head/tail   │
│ LRU / Design       │ GET: remove + insertAtTail                      │
│                    │ PUT: if exists remove old, insert at tail,      │
│                    │      if over cap remove head.next (LRU node)    │
└────────────────────┴─────────────────────────────────────────────────┘
```

---

## 🔁 Core Building Blocks (Reused Everywhere)

```java
// ── 1. TRAVERSE ──────────────────────────────────────────────
ListNode curr = head;
while (curr != null) { /* process */ curr = curr.next; }

// ── 2. REVERSE ───────────────────────────────────────────────
ListNode prev = null, curr = head;
while (curr != null) {
    ListNode next = curr.next; curr.next = prev; prev = curr; curr = next;
}
// prev = new head

// ── 3. FIND MIDDLE ───────────────────────────────────────────
ListNode slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next; fast = fast.next.next;
}
// slow = middle

// ── 4. DETECT CYCLE ──────────────────────────────────────────
ListNode slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next; fast = fast.next.next;
    if (slow == fast) { /* cycle! */ }
}

// ── 5. DUMMY NODE MERGE ──────────────────────────────────────
ListNode dummy = new ListNode(0), curr = dummy;
// ... build list using curr.next = ... ; curr = curr.next;
return dummy.next;

// ── 6. DLL INSERT AT TAIL (before dummy tail) ────────────────
node.prev = tail.prev;  node.next = tail;
tail.prev.next = node;  tail.prev = node;

// ── 7. DLL REMOVE ────────────────────────────────────────────
node.prev.next = node.next;
node.next.prev = node.prev;
```

---

*Notes Version 1.0 — Covers all of Striver's LL series (L1–L22) + all 11 NeetCode Linked List problems using 5 patterns.*
*Revise order: Fundamentals → Pattern 1 (Two Pointers) → Pattern 2 (Reversal) → Pattern 3 (Merge) → Pattern 4 (HashMap) → Pattern 5 (LRU Design)*
