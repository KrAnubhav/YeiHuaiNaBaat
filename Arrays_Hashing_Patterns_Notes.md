# 🗃️ Arrays & Hashing — Complete Beginner Notes
> **Goal:** Learn the pattern, not the problem. Master 5 core patterns to solve all 9 NeetCode Arrays & Hashing problems + essential Striver array algorithms. Notes are fully self-sufficient — no lecture needed.

---

## 📦 Table of Contents
**PART A — Array Fundamentals**
1. [What is an Array?](#1-what-is-an-array)
2. [Subarray vs Subsequence vs Subset](#2-subarray-vs-subsequence-vs-subset)
3. [The Prefix Sum Concept](#3-the-prefix-sum-concept)
4. [HashMap & HashSet — The Core Tools](#4-hashmap--hashset--the-core-tools)

**PART B — 5 Core Patterns**
5. [Pattern 1 — HashSet (Existence Checking)](#pattern-1--hashset-existence-checking)
6. [Pattern 2 — HashMap / Frequency Counting](#pattern-2--hashmap--frequency-counting)
7. [Pattern 3 — Prefix Sum + HashMap](#pattern-3--prefix-sum--hashmap)
8. [Pattern 4 — Smart Single-Pass (Greedy Traversal)](#pattern-4--smart-single-pass-greedy-traversal)
9. [Pattern 5 — Multi-Dimension Hashing (2D / Structural)](#pattern-5--multi-dimension-hashing-2d--structural)
10. [How to Identify the Pattern (Decision Tree)](#how-to-identify-the-pattern)
11. [Problem → Pattern Mapping](#problem--pattern-mapping)
12. [Complexity Cheat Sheet](#complexity-cheat-sheet)
13. [Common Mistakes to Avoid](#common-mistakes-to-avoid)

---

# PART A — Array Fundamentals

---

## 1. What is an Array?

An array is a **contiguous block of memory** storing elements of the same type. Indexed from `0` to `n-1`.

```
Index:  0    1    2    3    4
       ┌────┬────┬────┬────┬────┐
Array: │  3 │  1 │  4 │  1 │  5 │
       └────┴────┴────┴────┴────┘

arr[0] = 3   arr[2] = 4   arr[4] = 5
```

### Key Operations and Their Costs

| Operation | Time | Notes |
|-----------|------|-------|
| Access by index `arr[i]` | O(1) | Direct memory address calculation |
| Search (unsorted) | O(n) | Must check every element |
| Search (sorted) | O(log n) | Binary search |
| Insert at end | O(1) amortised | ArrayList auto-resizes |
| Insert at middle | O(n) | Must shift elements right |
| Delete from middle | O(n) | Must shift elements left |

### Java Array vs ArrayList

```java
// Fixed-size array (primitive)
int[] arr = new int[5];         // [0, 0, 0, 0, 0]
int[] arr2 = {3, 1, 4, 1, 5};  // initialised with values

// Dynamic array (preferred in problems)
List<Integer> list = new ArrayList<>();
list.add(10);         // add to end: O(1)
list.get(0);          // access: O(1)
list.size();          // length
list.contains(10);    // O(n) — use HashSet for O(1) contains!
```

---

## 2. Subarray vs Subsequence vs Subset

> These three terms appear constantly. Know the difference!

```
Original array: [1, 2, 3, 4]

SUBARRAY — contiguous, maintains order, cannot skip
  [1], [2], [3], [4]
  [1,2], [2,3], [3,4]
  [1,2,3], [2,3,4]
  [1,2,3,4]
  Total: n*(n+1)/2 subarrays

SUBSEQUENCE — non-contiguous allowed, but maintains ORDER
  [1], [2], [3], [4],
  [1,2], [1,3], [1,4], [2,3], [2,4], [3,4]
  [1,2,3], [1,2,4], [1,3,4], [2,3,4]
  [1,2,3,4], []
  Total: 2^n subsequences

SUBSET — like subsequence but ORDER doesn't matter
  {}, {1}, {2}, {3}, {4}, {1,2}, {1,3}, ...
  (same count as subsequences: 2^n, but treated as sets)
```

### Generating All Subarrays (O(n²))

```java
// Every pair (i, j) where i <= j defines a subarray
for (int i = 0; i < n; i++) {
    for (int j = i; j < n; j++) {
        // subarray is arr[i..j]
        // process it here
    }
}
```

---

## 3. The Prefix Sum Concept

> **One of the most important array tricks.** Enables O(1) range sum queries after O(n) preprocessing.

### What is a Prefix Sum?

```
Array:        [3,  1,  4,  1,  5,  9]
Index:          0   1   2   3   4   5

Prefix sum[i] = sum of all elements from index 0 to i (inclusive)

prefix[0] = 3
prefix[1] = 3+1 = 4
prefix[2] = 3+1+4 = 8
prefix[3] = 3+1+4+1 = 9
prefix[4] = 3+1+4+1+5 = 14
prefix[5] = 3+1+4+1+5+9 = 23
```

### Sum of Subarray [L, R] in O(1)

```
Sum(arr[L..R]) = prefix[R] - prefix[L-1]
                             (0 if L=0)

Example: Sum of arr[2..4]:
= prefix[4] - prefix[1]
= 14 - 4 = 10 ✅ (4 + 1 + 5 = 10)
```

```java
// Build prefix sum array
int[] prefix = new int[n];
prefix[0] = arr[0];
for (int i = 1; i < n; i++) {
    prefix[i] = prefix[i - 1] + arr[i];
}

// Query sum of arr[L..R] in O(1)
int rangeSum(int L, int R) {
    if (L == 0) return prefix[R];
    return prefix[R] - prefix[L - 1];
}
```

### Prefix Sum + HashMap → Count Subarrays

> This is the key insight for "count subarrays with sum = K" problems.

```
Key insight:
  If prefix[j] - prefix[i] = K
  Then subarray arr[i+1 .. j] has sum K
  → We need: prefix[j] - K = prefix[i]
  → As we build prefix sums, check if (currentSum - K) was seen before!

Map stores: prefix_sum → how many times it occurred
Initialize map with {0: 1} because empty prefix (sum=0) exists once.
```

---

## 4. HashMap & HashSet — The Core Tools

> These are the backbone of almost every Arrays & Hashing problem.

### HashSet — "Have I seen this before?"

```
Purpose: Store UNIQUE values. Check existence in O(1).

add(x)       → add x to set
contains(x)  → is x in set? O(1)
remove(x)    → remove x
size()       → number of elements
```

```java
Set<Integer> seen = new HashSet<>();
seen.add(5);
seen.add(3);
seen.contains(5);  // true  — O(1)
seen.contains(9);  // false — O(1)
```

### HashMap — "What is associated with this key?"

```
Purpose: Map key → value. Get/put/check in O(1).

put(k, v)         → store value v for key k
get(k)            → retrieve value for key k (null if not found)
getOrDefault(k,d) → get value or return default d if missing
containsKey(k)    → is key k present?
getOrDefault      → most useful in frequency counting
```

```java
Map<Integer, Integer> freq = new HashMap<>();

// Frequency counting pattern (most common usage)
for (int num : arr) {
    freq.put(num, freq.getOrDefault(num, 0) + 1);
    //                              ↑ default 0 if key not present
}
```

### Mental Model: HashMap vs HashSet

```
HashSet: Is X present? (just presence, no extra data)
  → "Have I seen this number before?"
  → "Does this character exist in the string?"

HashMap: What do I know about X? (presence + associated value)
  → "How many times have I seen this number?" (freq map)
  → "What index did I last see this number?" (index map)
  → "What group does this string belong to?" (group map)
```

---

# PART B — 5 Core Patterns

---

## PATTERN 1 — HashSet (Existence Checking)

### When to Use
- Check if ANY duplicate exists
- Find the LONGEST sequence by checking membership O(1)
- Keywords: "contains duplicate", "consecutive", "unique elements"

### The Core Idea
```
Instead of checking arr[i] against all other elements (O(n²)),
add elements to a HashSet and check in O(1).

For CONSECUTIVE sequences:
  Only START counting from a number n if (n-1) is NOT in the set.
  This ensures each sequence is counted exactly once from its start.
```

---

### Example A — Contains Duplicate

**Problem:** Return true if any value appears at least twice.

```
[1, 2, 3, 1] → true   (1 appears twice)
[1, 2, 3, 4] → false
```

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int num : nums) {
            if (seen.contains(num)) return true;  // seen before → duplicate!
            seen.add(num);
        }
        return false;
    }
}
// Time: O(n)  |  Space: O(n)
```

**Why not just sort?**
```
Sorting works too (O(n log n)) but HashSet is O(n).
Sorting changes the array which might not be allowed.
```

---

### Example B — Longest Consecutive Sequence

**Problem:** Find the length of the longest consecutive sequence. Must be O(n).

```
[100, 4, 200, 1, 3, 2]
Consecutive sequences: [1,2,3,4] and [100] and [200]
Answer: 4 (the sequence 1,2,3,4)
```

**Naive approach — O(n²):** For each number, keep checking num+1, num+2... in the array.

**Optimal O(n) insight:**
```
Only START a sequence count from n if (n-1) does NOT exist in the set.
This way, each sequence is visited exactly once from its start.

nums = {100, 4, 200, 1, 3, 2}

n=100: is 99 in set? NO → start counting: 100,101... → 101 not in set → length=1
n=4:   is 3 in set? YES → skip (4 is not a start)
n=200: is 199 in set? NO → start counting: 200,201... → 201 not in set → length=1
n=1:   is 0 in set? NO → start counting: 1,2,3,4,5... → 5 not in set → length=4 ✅
n=3:   is 2 in set? YES → skip
n=2:   is 1 in set? YES → skip

Max = 4
```

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);  // load all into set

        int maxLen = 0;

        for (int num : set) {
            // Only start counting if num is the START of a sequence
            if (!set.contains(num - 1)) {
                int current = num;
                int length = 1;

                // Extend the sequence as far as possible
                while (set.contains(current + 1)) {
                    current++;
                    length++;
                }

                maxLen = Math.max(maxLen, length);
            }
        }
        return maxLen;
    }
}
// Time: O(n) — each number visited at most twice (once as start, once in while)
// Space: O(n)
```

---

## PATTERN 2 — HashMap / Frequency Counting

### When to Use
- Count occurrences of elements
- Group elements that share a property
- Find elements that meet a frequency condition
- Map a value to its index
- Keywords: "anagram", "group", "top K", "two sum"

### The Core Ideas

```
FREQUENCY MAP:  char/num → count
INDEX MAP:      num → its index in array
GROUPING MAP:   canonical_key → list of original values
```

---

### Example A — Valid Anagram

**Problem:** Are `s` and `t` anagrams of each other? (same characters, same counts)

```
s="anagram", t="nagaram" → true
s="rat",     t="car"     → false
```

```
Anagram check = same frequency of every character.

Approach 1: Sort both strings → compare (O(n log n))
Approach 2: Frequency array of 26 chars (O(n)) ← better
Approach 3: HashMap (handles Unicode)
```

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;

        int[] count = new int[26];  // count[0]=freq of 'a', count[1]=freq of 'b'...

        for (char c : s.toCharArray()) count[c - 'a']++;  // increment for s
        for (char c : t.toCharArray()) count[c - 'a']--;  // decrement for t

        // If all counts are 0 → same frequencies → anagram
        for (int c : count) {
            if (c != 0) return false;
        }
        return true;
    }
}
// Time: O(n)  |  Space: O(1) — fixed 26-char array

// KEY TRICK: count[c - 'a'] converts 'a'→0, 'b'→1, ... 'z'→25
// Increment for one string, decrement for the other.
// Non-zero at end = different frequency = not anagram.
```

---

### Example B — Two Sum

**Problem:** Return indices of two numbers that add up to target.

```
nums = [2, 7, 11, 15], target = 9
Answer: [0, 1]  (nums[0] + nums[1] = 2 + 7 = 9)
```

**Key Insight:**
```
For each number x, the COMPLEMENT we need is (target - x).
Store x's index in HashMap as we go.
When we find a number whose complement is already in the map → done!

nums = [2, 7, 11, 15], target = 9

i=0: x=2, need=(9-2)=7. Is 7 in map? NO. Add {2:0} to map.
i=1: x=7, need=(9-7)=2. Is 2 in map? YES → index 0! Return [0, 1] ✅
```

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> seen = new HashMap<>();  // value → index

        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];

            if (seen.containsKey(complement)) {
                return new int[]{seen.get(complement), i};  // found!
            }
            seen.put(nums[i], i);  // store AFTER checking (don't use same element twice)
        }
        return new int[]{};  // no solution (problem guarantees one exists)
    }
}
// Time: O(n)  |  Space: O(n)

// WHY store after checking?
// If target=6 and nums[i]=3, we don't want to use index i as BOTH elements.
// Storing after checking ensures we look at PREVIOUSLY seen elements only.
```

---

### Example C — Group Anagrams

**Problem:** Group strings that are anagrams of each other.

```
["eat","tea","tan","ate","nat","bat"]
→ [["bat"],["nat","tan"],["ate","eat","tea"]]
```

**Key Insight:**
```
Two strings are anagrams ↔ they have the same sorted form.
"eat" sorted = "aet"
"tea" sorted = "aet"   ← same key!
"ate" sorted = "aet"   ← same key!

Use sorted string as HashMap key → groups anagrams together.
```

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();

        for (String s : strs) {
            // Sort the string to get its canonical form
            char[] chars = s.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);   // "eat" → "aet"

            // Add to the group for this key
            map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
            // computeIfAbsent: if key not present, create new ArrayList, then add s
        }
        return new ArrayList<>(map.values());
    }
}
// Time: O(n × k log k) — n strings, k = max string length (sorting each)
// Space: O(n × k)

// ALTERNATIVE KEY: count array instead of sorting (O(n×k) total)
// char[] count = new char[26];
// for (char c : s.toCharArray()) count[c-'a']++;
// String key = Arrays.toString(count);  // "[1,0,0,0,1,0,...,1,...]"
```

**Visual:**
```
"eat" → sort → "aet" → map{"aet": ["eat"]}
"tea" → sort → "aet" → map{"aet": ["eat","tea"]}
"tan" → sort → "ant" → map{"aet": [...], "ant": ["tan"]}
"ate" → sort → "aet" → map{"aet": ["eat","tea","ate"]}
"nat" → sort → "ant" → map{"ant": ["tan","nat"]}
"bat" → sort → "abt" → map{"abt": ["bat"]}
```

---

### Example D — Top K Frequent Elements

**Problem:** Return the k most frequent elements.

```
nums=[1,1,1,2,2,3], k=2 → [1, 2]  (1 appears 3x, 2 appears 2x)
```

**Two approaches:**

**Approach 1 — Min-Heap (O(n log k)):**
```
Build freq map → push into min-heap of size k
→ heap keeps only top k frequent
```

**Approach 2 — Bucket Sort (O(n)) — Optimal:**
```
Insight: frequency can be at most n (all elements the same).
Create buckets where bucket[i] = list of numbers with frequency i.
Then scan from bucket[n] down to bucket[1], collect k elements.
```

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // Step 1: Build frequency map
        Map<Integer, Integer> freq = new HashMap<>();
        for (int num : nums)
            freq.put(num, freq.getOrDefault(num, 0) + 1);

        // Step 2: Bucket sort — bucket[i] holds numbers with frequency i
        List<Integer>[] bucket = new List[nums.length + 1];
        for (int i = 0; i <= nums.length; i++)
            bucket[i] = new ArrayList<>();

        for (Map.Entry<Integer, Integer> entry : freq.entrySet())
            bucket[entry.getValue()].add(entry.getKey());

        // Step 3: Collect top k from highest frequency bucket downward
        int[] result = new int[k];
        int idx = 0;
        for (int i = nums.length; i >= 1 && idx < k; i--) {
            for (int num : bucket[i]) {
                result[idx++] = num;
                if (idx == k) break;
            }
        }
        return result;
    }
}
// Time: O(n)  |  Space: O(n)
```

**Visual of bucket sort:**
```
nums=[1,1,1,2,2,3], freq={1:3, 2:2, 3:1}

bucket[0] = []
bucket[1] = [3]       ← freq 1
bucket[2] = [2]       ← freq 2
bucket[3] = [1]       ← freq 3
bucket[4] = []
bucket[5] = []
bucket[6] = []

Scan from top: bucket[6]→[], bucket[5]→[], bucket[4]→[],
               bucket[3]→[1] → collect 1 (idx=1, k=2 not reached)
               bucket[2]→[2] → collect 2 (idx=2, k=2 reached!)
Result: [1, 2] ✅
```

---

### Example E — Encode and Decode Strings

**Problem:** Design encode/decode so a list of strings can be serialized to a single string and recovered.

```
["hello","world"] → encode → "5#hello5#world" → decode → ["hello","world"]
```

**Key Insight:**
```
Can't just use a delimiter like "," because the strings themselves might contain ",".
Solution: PREFIX each string with its LENGTH + a separator character "#".

"5#hello" = "this string is 5 chars long, then comes 'hello'"
            → no ambiguity! Length tells you exactly where the string ends.
```

```java
public class Codec {
    // Encode list of strings to single string
    public String encode(List<String> strs) {
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s.length()).append('#').append(s);
            // "hello" → "5#hello"
            // "hi"    → "2#hi"
        }
        return sb.toString();
        // ["hello","world"] → "5#hello5#world"
    }

    // Decode single string back to list of strings
    public List<String> decode(String s) {
        List<String> result = new ArrayList<>();
        int i = 0;

        while (i < s.length()) {
            // Find the '#' separator
            int j = i;
            while (s.charAt(j) != '#') j++;

            // Parse the length
            int len = Integer.parseInt(s.substring(i, j));

            // Extract the string
            result.add(s.substring(j + 1, j + 1 + len));

            // Move i to start of next encoded string
            i = j + 1 + len;
        }
        return result;
    }
}
// Time: O(n) encode, O(n) decode  |  Space: O(n)
```

**Decode trace:**
```
s = "5#hello5#world"
i=0: j finds '#' at index 1. len=5. extract s[2..6]="hello". i=7.
i=7: j finds '#' at index 8. len=5. extract s[9..13]="world". i=14.
i=14: done. Result: ["hello","world"] ✅
```

---

## PATTERN 3 — Prefix Sum + HashMap

### When to Use
- Count subarrays with a given SUM
- Find subarrays with property that depends on cumulative values
- Keywords: "subarray sum equals K", "product except self"

### The Core Idea
```
prefix[i] = sum of arr[0..i]

Sum of subarray arr[L+1 .. R] = prefix[R] - prefix[L]

If we want subarrays where sum = K:
  prefix[R] - prefix[L] = K
  → prefix[L] = prefix[R] - K

As we scan right to left at index R:
  Ask: "How many times have I seen (currentSum - K) before?"
  That count = number of valid subarrays ending at R.

Store prefix sums in HashMap as we go: {prefix_sum → count}
Initialize with {0: 1} for the empty prefix (subarray starting at index 0).
```

---

### Example A — Product of Array Except Self

**Problem:** Return array where `output[i]` = product of all elements EXCEPT `arr[i]`. No division. O(n).

```
nums = [1, 2, 3, 4]
output = [24, 12, 8, 6]
  output[0] = 2×3×4 = 24
  output[1] = 1×3×4 = 12
  output[2] = 1×2×4 = 8
  output[3] = 1×2×3 = 6
```

**Key Insight — Prefix & Suffix Products:**
```
output[i] = (product of everything LEFT of i) × (product of everything RIGHT of i)

LEFT products:         RIGHT products:
index: 0  1  2  3     index: 0  1  2  3
left:  1  1  2  6     right: 24 12  4  1
                       ↑ built right to left

output[i] = left[i] × right[i]
output = [1×24, 1×12, 2×4, 6×1] = [24, 12, 8, 6] ✅

Can do in O(1) space by building left products into output array,
then multiplying right product in a second pass.
```

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] output = new int[n];

        // Pass 1: fill output with LEFT products
        // output[i] = product of all elements to the LEFT of i
        output[0] = 1;  // nothing to the left of index 0
        for (int i = 1; i < n; i++) {
            output[i] = output[i - 1] * nums[i - 1];
        }
        // output = [1, 1, 2, 6] for [1,2,3,4]

        // Pass 2: multiply with RIGHT products (using a running variable)
        int rightProduct = 1;  // nothing to the right of last index yet
        for (int i = n - 1; i >= 0; i--) {
            output[i] *= rightProduct;    // multiply by right product so far
            rightProduct *= nums[i];      // update right product
        }
        // i=3: output[3] = 6×1=6,  rightProduct = 1×4=4
        // i=2: output[2] = 2×4=8,  rightProduct = 4×3=12
        // i=1: output[1] = 1×12=12, rightProduct = 12×2=24
        // i=0: output[0] = 1×24=24, rightProduct = 24×1=24

        return output;
    }
}
// Time: O(n)  |  Space: O(1) extra (output array doesn't count)
```

**Two-pass visual:**
```
nums:         [1,  2,  3,  4]

After pass 1 (left products):
output:       [1,  1,  2,  6]
               ↑   ↑   ↑   ↑
               1  1×1 1×2 1×2×3

After pass 2 (multiply right products):
rightProduct starts at 1, moves right to left:
i=3: output[3]=6×1=6,   right=4
i=2: output[2]=2×4=8,   right=12
i=1: output[1]=1×12=12, right=24
i=0: output[0]=1×24=24

Final: [24, 12, 8, 6] ✅
```

---

### Example B — Subarray Sum Equals K (Striver L17)

**Problem:** Count subarrays with sum exactly equal to K.

```
nums = [1, 1, 1], K = 2
Subarrays: [1,1] (index 0-1), [1,1] (index 1-2) → Answer: 2
```

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> prefixCount = new HashMap<>();
        prefixCount.put(0, 1);  // empty prefix has sum 0, seen once

        int currentSum = 0;
        int count = 0;

        for (int num : nums) {
            currentSum += num;  // running prefix sum

            // How many subarrays ending HERE have sum = k?
            // We need prefix[L] = currentSum - k
            int needed = currentSum - k;
            count += prefixCount.getOrDefault(needed, 0);

            // Record this prefix sum
            prefixCount.put(currentSum, prefixCount.getOrDefault(currentSum, 0) + 1);
        }
        return count;
    }
}
// Time: O(n)  |  Space: O(n)
```

**Trace for [1,1,1], k=2:**
```
prefixCount = {0:1}
i=0: currentSum=1. needed=1-2=-1. count+=0. prefixCount={0:1, 1:1}
i=1: currentSum=2. needed=2-2=0. count+=1(seen 0 once). prefixCount={0:1,1:1,2:1}
i=2: currentSum=3. needed=3-2=1. count+=1(seen 1 once). prefixCount={...,3:1}

Total count = 2 ✅
```

---

## PATTERN 4 — Smart Single-Pass (Greedy Traversal)

### When to Use
- Find the maximum/minimum in ONE pass using a running variable
- Majority element (appears > n/2 times)
- Stock prices — track minimum price seen so far
- Kadane's Algorithm — maximum subarray sum
- Keywords: "maximum subarray", "majority element", "best time to buy"

### The Core Idea
```
Maintain one or more running variables as you traverse.
Make a LOCAL decision at each step that leads to the GLOBAL answer.
No backtracking, no extra data structures (usually O(1) space).
```

---

### Example A — Best Time to Buy and Sell Stock (Striver L10)

**Problem:** Given prices, find max profit from one buy-sell transaction.

```
prices = [7, 1, 5, 3, 6, 4]
Buy at 1 (index 1), sell at 6 (index 4) → profit = 5
```

**Key Insight:**
```
Track the MINIMUM price seen so far (best time to buy).
At each day: profit if sold TODAY = price[i] - minPrice
Track the maximum of all such profits.
```

```java
class Solution {
    public int maxProfit(int[] prices) {
        int minPrice = Integer.MAX_VALUE;
        int maxProfit = 0;

        for (int price : prices) {
            if (price < minPrice) {
                minPrice = price;           // found a cheaper buy day
            } else {
                maxProfit = Math.max(maxProfit, price - minPrice); // profit today
            }
        }
        return maxProfit;
    }
}
// Time: O(n)  |  Space: O(1)
```

**Trace for [7, 1, 5, 3, 6, 4]:**
```
price=7: minPrice=7, maxProfit=0
price=1: minPrice=1  (cheaper!)
price=5: maxProfit=max(0, 5-1)=4
price=3: maxProfit=max(4, 3-1)=4
price=6: maxProfit=max(4, 6-1)=5 ✅
price=4: maxProfit=max(5, 4-1)=5
```

---

### Example B — Kadane's Algorithm — Maximum Subarray Sum (Striver L8)

**Problem:** Find the contiguous subarray with the largest sum.

```
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Answer: 6  (subarray [4, -1, 2, 1])
```

**Key Insight:**
```
At each element, decide: "Should I EXTEND the current subarray or START FRESH?"
  - If currentSum + nums[i] > nums[i] → extend (currentSum is positive, helps us)
  - If currentSum + nums[i] < nums[i] → start fresh from nums[i]

Simplified: currentSum = max(nums[i], currentSum + nums[i])
            = nums[i] + max(0, currentSum)

Track the global maximum across all these local decisions.
```

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int currentSum = nums[0];   // current subarray sum
        int maxSum = nums[0];       // global maximum

        for (int i = 1; i < nums.length; i++) {
            // Extend current subarray OR start fresh at nums[i]
            currentSum = Math.max(nums[i], currentSum + nums[i]);
            maxSum = Math.max(maxSum, currentSum);
        }
        return maxSum;
    }
}
// Time: O(n)  |  Space: O(1)
```

**Trace for [-2, 1, -3, 4, -1, 2, 1, -5, 4]:**
```
i=0: curr=-2, max=-2
i=1: curr=max(1,-2+1)=max(1,-1)=1,  max=1
i=2: curr=max(-3,1-3)=max(-3,-2)=-2, max=1
i=3: curr=max(4,-2+4)=max(4,2)=4,   max=4
i=4: curr=max(-1,4-1)=max(-1,3)=3,  max=4
i=5: curr=max(2,3+2)=max(2,5)=5,    max=5
i=6: curr=max(1,5+1)=max(1,6)=6,    max=6 ✅
i=7: curr=max(-5,6-5)=max(-5,1)=1,  max=6
i=8: curr=max(4,1+4)=max(4,5)=5,    max=6
```

---

### Example C — Majority Element — Moore's Voting Algorithm (Striver L7)

**Problem:** Find the element appearing more than n/2 times. Guaranteed to exist.

```
nums = [2, 2, 1, 1, 1, 2, 2]
Answer: 2  (appears 4 times, n=7, 4 > 7/2 = 3.5)
```

**Moore's Voting Algorithm Intuition:**
```
Every time we see a DIFFERENT number, they "cancel" each other out.
The majority element (> n/2 times) will SURVIVE after all cancellations.

Think of it as: majority element votes "+" and every other element votes "-".
Since majority has > n/2 votes, it always wins.

Process:
  candidate = first element, count = 1
  For each next element:
    if element == candidate: count++  (reinforce)
    else:                    count--  (cancel)
    if count == 0:           candidate = next element, count = 1 (reset)
```

```java
class Solution {
    public int majorityElement(int[] nums) {
        int candidate = nums[0];
        int count = 1;

        for (int i = 1; i < nums.length; i++) {
            if (count == 0) {
                candidate = nums[i];  // start fresh with new candidate
                count = 1;
            } else if (nums[i] == candidate) {
                count++;              // reinforce current candidate
            } else {
                count--;              // cancel one vote
            }
        }
        return candidate;  // guaranteed to be majority
    }
}
// Time: O(n)  |  Space: O(1)
```

**Trace for [2,2,1,1,1,2,2]:**
```
i=0: candidate=2, count=1
i=1: 2==2 → count=2
i=2: 1≠2 → count=1
i=3: 1≠2 → count=0
i=4: count=0 → candidate=1, count=1
i=5: 2≠1 → count=0
i=6: count=0 → candidate=2, count=1

Return 2 ✅
```

---

### Example D — Dutch National Flag / Sort 0s, 1s, 2s (Striver L6)

**Problem:** Sort an array containing only 0s, 1s, and 2s in O(n) time and O(1) space.

```
nums = [2, 0, 2, 1, 1, 0]
After: [0, 0, 1, 1, 2, 2]
```

**Three-Pointer Approach:**
```
Maintain three regions:
  [0..low-1]  → all 0s
  [low..mid-1] → all 1s
  [mid..high] → unknown (yet to process)
  [high+1..n-1] → all 2s

low = 0, mid = 0, high = n-1

Process element at mid:
  If nums[mid] == 0: swap(mid, low), low++, mid++
  If nums[mid] == 1: mid++  (already in right place)
  If nums[mid] == 2: swap(mid, high), high-- (DON'T mid++, swapped element is unprocessed)

Stop when mid > high.
```

```java
class Solution {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;

        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {  // nums[mid] == 2
                swap(nums, mid, high);
                high--;
                // Don't increment mid — newly swapped element needs checking!
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
// Time: O(n)  |  Space: O(1)
```

**Trace for [2,0,2,1,1,0]:**
```
low=0,mid=0,high=5: nums[0]=2 → swap(0,5):[0,0,2,1,1,2], high=4
low=0,mid=0,high=4: nums[0]=0 → swap(0,0):[0,0,2,1,1,2], low=1,mid=1
low=1,mid=1,high=4: nums[1]=0 → swap(1,1), low=2,mid=2
low=2,mid=2,high=4: nums[2]=2 → swap(2,4):[0,0,1,1,2,2], high=3
low=2,mid=2,high=3: nums[2]=1 → mid=3
low=2,mid=3,high=3: nums[3]=1 → mid=4
mid=4 > high=3 → STOP
Result: [0,0,1,1,2,2] ✅
```

---

## PATTERN 5 — Multi-Dimension Hashing (2D / Structural)

### When to Use
- Validate constraints across multiple dimensions simultaneously
- Each element belongs to multiple groups, each group must be unique
- Keywords: "valid sudoku", "matrix", "row and column constraint"

### The Core Idea
```
Use MULTIPLE HashSets simultaneously:
  - One set per ROW (check row uniqueness)
  - One set per COLUMN (check column uniqueness)
  - One set per BOX/GROUP (check box uniqueness)

Key: encode WHICH group and WHAT value into a single string key.
  Row group for value v at row r:   "r,v"  or  "row" + r + "_" + v
  Col group for value v at col c:   "c,v"
  Box group for value v at box b:   "b,v"  where b = (r/3)*3 + c/3
```

---

### Example A — Valid Sudoku

**Problem:** Determine if a 9×9 Sudoku board is valid. Each row, column, and 3×3 box must contain digits 1-9 with no repeats.

```
Valid constraints:
  Each ROW: digits 1-9 appear at most once
  Each COL: digits 1-9 appear at most once
  Each 3×3 BOX: digits 1-9 appear at most once

Board has '.' for empty cells — ignore those.
```

**Box Index Formula:**
```
For cell at (row, col):
  box index = (row / 3) * 3 + (col / 3)

  (0,0)(0,1)(0,2) | (0,3)(0,4)(0,5) | (0,6)(0,7)(0,8)
  (1,0)(1,1)(1,2) | ...
  (2,0)(2,1)(2,2) |
  ─────────────────────────────────────────────────────
  box 0            | box 1            | box 2
  box 3            | box 4            | box 5
  box 6            | box 7            | box 8
```

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();

        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                char val = board[r][c];
                if (val == '.') continue;  // skip empty cells

                // Create unique keys for row, column, and box
                String rowKey = "row" + r + "_" + val;
                String colKey = "col" + c + "_" + val;
                String boxKey = "box" + (r / 3) * 3 + (c / 3) + "_" + val;

                // If any key already seen → INVALID
                if (!seen.add(rowKey) || !seen.add(colKey) || !seen.add(boxKey)) {
                    return false;
                }
                // seen.add() returns false if element was already present
            }
        }
        return true;
    }
}
// Time: O(81) = O(1) — fixed 9×9 board
// Space: O(81×3) = O(1) — fixed number of sets entries
```

**Why string keys?**
```
"row2_5" uniquely means "value 5 in row 2"
"col7_3" uniquely means "value 3 in col 7"
"box4_8" uniquely means "value 8 in box 4"

If we add "row2_5" and it returns false → 5 appeared TWICE in row 2 → invalid!
```

**Alternative — cleaner with 3 arrays of sets:**
```java
Set<Integer>[] rows  = new HashSet[9];
Set<Integer>[] cols  = new HashSet[9];
Set<Integer>[] boxes = new HashSet[9];
for (int i = 0; i < 9; i++) {
    rows[i] = new HashSet<>();
    cols[i] = new HashSet<>();
    boxes[i] = new HashSet<>();
}

for (int r = 0; r < 9; r++) {
    for (int c = 0; c < 9; c++) {
        if (board[r][c] == '.') continue;
        int val = board[r][c] - '0';
        int box = (r / 3) * 3 + (c / 3);

        if (!rows[r].add(val) || !cols[c].add(val) || !boxes[box].add(val))
            return false;
    }
}
return true;
```

---

## How to Identify the Pattern

```
READ the problem. Ask these questions in order:

START
  │
  ▼
Does the problem involve a 2D GRID / MATRIX with row+column+box constraints?
  ├── YES → PATTERN 5 (Multi-Dimension Hashing)
  │         Use separate sets for each dimension
  │         Key encoding: "row/col/box + index + value"
  └── NO ↓

Does the problem ask for a COUNT of SUBARRAYS meeting a sum condition?
OR need range sum queries efficiently?
  ├── YES → PATTERN 3 (Prefix Sum + HashMap)
  │         Running sum + map of {prefixSum → count}
  │         Init map with {0: 1}
  │         For product: two-pass prefix × suffix
  └── NO ↓

Does the problem require ONE PASS with a running variable to find max/min?
(Majority element, max subarray, buy/sell stock, sort 0s 1s 2s)
  ├── YES → PATTERN 4 (Smart Single-Pass / Greedy)
  │         Best Time to Buy/Sell → track min price
  │         Max Subarray → Kadane's (extend or restart)
  │         Majority Element → Moore's voting (cancel/reinforce)
  │         Sort 0s 1s 2s → Dutch National Flag (3 pointers)
  └── NO ↓

Does the problem need to MAP values to COUNTS, INDICES, or GROUPS?
(Two Sum, Group Anagrams, Top K Frequent, Valid Anagram, Encode/Decode)
  ├── YES → PATTERN 2 (HashMap / Frequency Counting)
  │         Two Sum → map value → index, check complement
  │         Anagram → freq array or sorted string as key
  │         Group Anagrams → sorted string as key → list of originals
  │         Top K Frequent → freq map + bucket sort
  │         Encode/Decode → length-prefixed encoding
  └── NO ↓

Default → PATTERN 1 (HashSet — Existence Checking)
          "Have I seen this before?" → add to set, check contains
          Contains Duplicate → just detect any repeat
          Longest Consecutive → only count from sequence start
```

---

## Problem → Pattern Mapping

| Problem | Pattern | Key Trick |
|---------|---------|-----------|
| Contains Duplicate | Pattern 1 | HashSet, return true if already seen |
| Valid Anagram | Pattern 2 | Freq array[26]: +1 for s, -1 for t, check all zeros |
| Two Sum | Pattern 2 | Map value→index, check if (target-num) exists |
| Group Anagrams | Pattern 2 | Sorted string as HashMap key |
| Top K Frequent Elements | Pattern 2 | Freq map + bucket sort by frequency |
| Encode and Decode Strings | Pattern 2 | Length + "#" prefix encoding |
| Product of Array Except Self | Pattern 3 | Two-pass prefix × suffix product |
| Valid Sudoku | Pattern 5 | Three sets (row/col/box), encode as string keys |
| Longest Consecutive Sequence | Pattern 1 | HashSet + only count from sequence start |
| Best Time to Buy/Sell Stock | Pattern 4 | Track min price, max profit |
| Maximum Subarray (Kadane's) | Pattern 4 | Extend or restart at each element |
| Majority Element (Moore's) | Pattern 4 | Candidate + vote count, cancel opposites |
| Sort Colors (Dutch Flag) | Pattern 4 | low/mid/high three pointers |
| Subarray Sum = K | Pattern 3 | Prefix sum + HashMap {sum→count} |

---

## Complexity Cheat Sheet

| Problem | Time | Space | Key Algorithm |
|---------|------|-------|---------------|
| Contains Duplicate | O(n) | O(n) | HashSet |
| Valid Anagram | O(n) | O(1) | Freq array[26] |
| Two Sum | O(n) | O(n) | HashMap value→index |
| Group Anagrams | O(n × k log k) | O(n×k) | Sort each string as key |
| Top K Frequent | O(n) | O(n) | Bucket sort by freq |
| Encode/Decode | O(n) | O(n) | Length-prefix encoding |
| Product Except Self | O(n) | O(1) | Two-pass prefix/suffix |
| Valid Sudoku | O(1) | O(1) | 9×9 fixed, 3 sets |
| Longest Consecutive | O(n) | O(n) | HashSet, start at n if n-1 missing |
| Best Time Buy/Sell | O(n) | O(1) | Track min price |
| Max Subarray (Kadane) | O(n) | O(1) | Extend or restart |
| Majority Element | O(n) | O(1) | Moore's voting |
| Sort 0s 1s 2s | O(n) | O(1) | Dutch national flag |
| Subarray Sum = K | O(n) | O(n) | Prefix sum + HashMap |

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Using `.contains()` on List instead of Set
```java
// WRONG — List.contains() is O(n), making whole solution O(n²)
List<Integer> seen = new ArrayList<>();
if (seen.contains(num)) ...  // O(n) per lookup!

// CORRECT — HashSet.contains() is O(1)
Set<Integer> seen = new HashSet<>();
if (seen.contains(num)) ...  // O(1) per lookup ✅
```

### ❌ Mistake 2: Two Sum — storing index BEFORE checking
```java
// WRONG — might use the same element twice (e.g., target=6, num=3)
seen.put(nums[i], i);  // store first
if (seen.containsKey(complement)) ...  // might find itself!

// CORRECT — check first, then store
if (seen.containsKey(complement)) return ...;  // check first
seen.put(nums[i], i);  // then store
```

### ❌ Mistake 3: Prefix Sum — forgetting to initialise {0:1}
```java
// WRONG — misses subarrays starting from index 0
Map<Integer, Integer> map = new HashMap<>();  // empty init
// If subarray [0..i] itself has sum K, it won't be counted!

// CORRECT — initialise with empty prefix sum
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);  // sum 0 seen once (before any elements)
```

### ❌ Mistake 4: Group Anagrams — using unsorted char array as key
```java
// WRONG — Arrays.toString on char array uses reference, not content
char[] chars = s.toCharArray();
Arrays.sort(chars);
map.put(chars, ...);  // chars is a reference, two equal arrays are != as keys!

// CORRECT — convert sorted char array to String first
String key = new String(chars);  // "eat" → "aet" as a proper String key
map.put(key, ...);
```

### ❌ Mistake 5: Kadane's — initialising with 0 (breaks for all-negative arrays)
```java
// WRONG — returns 0 if all elements are negative
int maxSum = 0;  // 0 is wrong baseline

// CORRECT — initialise with nums[0], start loop at i=1
int maxSum = nums[0];
int currentSum = nums[0];
for (int i = 1; i < nums.length; i++) { ... }
```

### ❌ Mistake 6: Longest Consecutive — not using HashSet (O(n²) instead of O(n))
```java
// WRONG — checking arr for each consecutive number is O(n²)
for (int i = 0; i < nums.length; i++) {
    int next = nums[i] + 1;
    while (contains(nums, next)) { next++; }  // O(n) each time!
}

// CORRECT — load into HashSet first, O(1) lookups
Set<Integer> set = new HashSet<>();
for (int num : nums) set.add(num);
// Then check set.contains(num-1) and set.contains(num+1) in O(1)
```

### ❌ Mistake 7: Dutch National Flag — incrementing mid after swapping a 2
```java
// WRONG — newly swapped element from high hasn't been processed yet!
} else {  // nums[mid] == 2
    swap(nums, mid, high);
    high--;
    mid++;  // ← WRONG! The element now at mid came from high, unprocessed!
}

// CORRECT — don't increment mid when swapping with high
} else {
    swap(nums, mid, high);
    high--;
    // mid stays → the swapped element will be processed next iteration
}
```

---

## 📋 Quick Reference Card

```
┌──────────────────────────────────────────────────────────────────────┐
│                 ARRAYS & HASHING QUICK CARD                          │
├────────────────────┬─────────────────────────────────────────────────┤
│ CORE TOOLS         │ HashSet: existence in O(1)                      │
│                    │ HashMap: key→value in O(1)                      │
│                    │ getOrDefault(key, 0): safe freq counting        │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 1          │ HashSet for existence                           │
│ HashSet            │ Contains Dup: seen.contains → return true       │
│                    │ Consecutive: only start if (n-1) ∉ set         │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 2          │ HashMap for grouping/counting/mapping           │
│ HashMap / Freq     │ Anagram: freq[c-'a']++ for s, -- for t         │
│                    │ Two Sum: check complement BEFORE storing        │
│                    │ Group: sorted string as key                     │
│                    │ Top K: freq map + bucket[freq].add(num)         │
│                    │ Encode: len + "#" + str                         │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 3          │ Prefix Sum: prefix[i]=prefix[i-1]+arr[i]       │
│ Prefix Sum         │ Range sum: prefix[R]-prefix[L-1]               │
│                    │ Subarray sum=K: map{sum→count}, init {0:1}     │
│                    │ Product except self: prefix pass + suffix pass  │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 4          │ Single-pass greedy                              │
│ Smart Single-Pass  │ Buy/Sell: track min price seen so far           │
│                    │ Kadane's: curr=max(num, curr+num), init=arr[0]  │
│                    │ Moore's: candidate+count, reset when count=0   │
│                    │ Dutch Flag: low/mid/high, don't mid++ on swap2  │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 5          │ Multi-dimension: rows[] + cols[] + boxes[]      │
│ 2D Hashing         │ Box index = (r/3)*3 + (c/3)                    │
│                    │ set.add() returns false if duplicate → invalid  │
└────────────────────┴─────────────────────────────────────────────────┘
```

---

## 🔁 Core Building Blocks

```java
// ── 1. FREQUENCY MAP ──────────────────────────────────────────────
Map<T, Integer> freq = new HashMap<>();
for (T item : collection)
    freq.put(item, freq.getOrDefault(item, 0) + 1);

// ── 2. EXISTENCE SET ──────────────────────────────────────────────
Set<T> seen = new HashSet<>();
if (seen.contains(x)) { /* duplicate found */ }
seen.add(x);

// ── 3. PREFIX SUM ─────────────────────────────────────────────────
int[] prefix = new int[n];
prefix[0] = arr[0];
for (int i = 1; i < n; i++) prefix[i] = prefix[i-1] + arr[i];
// range sum [L,R] = prefix[R] - (L > 0 ? prefix[L-1] : 0)

// ── 4. PREFIX SUM + MAP (subarray sum = K) ────────────────────────
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);          // ← don't forget this!
int curr = 0, count = 0;
for (int num : arr) {
    curr += num;
    count += map.getOrDefault(curr - k, 0);
    map.put(curr, map.getOrDefault(curr, 0) + 1);
}

// ── 5. KADANE'S (max subarray sum) ────────────────────────────────
int curr = nums[0], max = nums[0];
for (int i = 1; i < n; i++) {
    curr = Math.max(nums[i], curr + nums[i]);
    max = Math.max(max, curr);
}

// ── 6. MOORE'S VOTING (majority element) ──────────────────────────
int candidate = nums[0], count = 1;
for (int i = 1; i < n; i++) {
    if (count == 0) { candidate = nums[i]; count = 1; }
    else if (nums[i] == candidate) count++;
    else count--;
}
// candidate = majority element

// ── 7. BUCKET SORT BY FREQUENCY ───────────────────────────────────
List<Integer>[] bucket = new List[n + 1];
for (int i = 0; i <= n; i++) bucket[i] = new ArrayList<>();
for (var entry : freq.entrySet()) bucket[entry.getValue()].add(entry.getKey());
// scan from bucket[n] downward to get top-k
```

---

*Notes Version 1.0 — Covers all 9 NeetCode Arrays & Hashing problems + Striver Arrays series key algorithms (Kadane's, Moore's Voting, Dutch National Flag, Best Time to Buy/Sell, Subarray Sum K, Product Except Self).*
*Revise order: Pattern 1 (HashSet) → Pattern 2 (HashMap) → Pattern 3 (Prefix Sum) → Pattern 4 (Single-Pass Greedy) → Pattern 5 (2D Hashing)*
