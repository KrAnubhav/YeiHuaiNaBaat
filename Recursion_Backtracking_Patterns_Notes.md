# 🔄 Recursion & Backtracking — Complete Beginner Notes
> **Goal:** Understand recursion from zero, then apply it to all 10 NeetCode Backtracking problems using 5 reusable patterns. Notes are self-sufficient — no lecture needed.

---

## 📦 Table of Contents
**PART A — Recursion Fundamentals**
1. [What is Recursion?](#1-what-is-recursion)
2. [The Recursion Tree & Stack Space](#2-the-recursion-tree--stack-space)
3. [Base Case — The Most Important Thing](#3-base-case)
4. [Parameterised vs Functional Recursion](#4-parameterised-vs-functional-recursion)
5. [Multiple Recursion Calls](#5-multiple-recursion-calls)
6. [Subsequences — The Foundation of Backtracking](#6-subsequences--the-foundation-of-backtracking)
7. [Three Recursion Problem Types: Print All / Print One / Count](#7-three-recursion-problem-types)

**PART B — Backtracking**
8. [What is Backtracking?](#8-what-is-backtracking)
9. [Pattern 1 — Subsets (Pick / Don't Pick)](#pattern-1--subsets-pick--dont-pick)
10. [Pattern 2 — Combinations (Start Index)](#pattern-2--combinations-start-index)
11. [Pattern 3 — Permutations (Order Matters)](#pattern-3--permutations-order-matters)
12. [Pattern 4 — Constraint-Based Building](#pattern-4--constraint-based-building)
13. [Pattern 5 — Hard Grid / Constraint Placement](#pattern-5--hard-grid--constraint-placement)
14. [How to Identify the Pattern (Decision Tree)](#how-to-identify-the-pattern)
15. [Problem → Pattern Mapping](#problem--pattern-mapping)
16. [Complexity Cheat Sheet](#complexity-cheat-sheet)
17. [Common Mistakes to Avoid](#common-mistakes-to-avoid)

---

# PART A — Recursion Fundamentals

---

## 1. What is Recursion?

**Recursion** = A function that **calls itself** to solve a smaller version of the same problem, until it hits a base case and stops.

### Real-world analogy
```
You want to know "how many people are behind you in a queue?"

YOU ask the person behind you.
THEY ask the person behind THEM.
...and so on...
Until the LAST person says "0 people are behind me." ← BASE CASE

Then answers come back:
Last person: 0
Second-last: 0 + 1 = 1
...
You: n-1 + 1 = n
```

### The Two Parts of Every Recursive Function

```java
void recursiveFunction(int n) {
    // PART 1: Base Case — WHEN TO STOP
    if (n == 0) {
        return; // stop recursing
    }

    // PART 2: Recursive Case — CALL YOURSELF with a SMALLER problem
    System.out.println(n);
    recursiveFunction(n - 1); // n-1 is a smaller problem
}
```

> ⚠️ **If you forget the base case → StackOverflowError (infinite recursion).**

### Simple Example — Print 1 to N

```java
// Print 1 to N using recursion
void print1toN(int i, int n) {
    if (i > n) return;        // Base case: stop when i > n
    System.out.println(i);    // Print current number
    print1toN(i + 1, n);      // Recurse with i+1
}
// Call with: print1toN(1, 5)
// Output: 1 2 3 4 5
```

### Simple Example — Print N to 1 (Reverse)

```java
void printNto1(int i, int n) {
    if (i < 1) return;        // Base case
    System.out.println(i);    // Print current number
    printNto1(i - 1, n);      // Recurse with i-1
}
// Call with: printNto1(5, 5)
// Output: 5 4 3 2 1
```

---

## 2. The Recursion Tree & Stack Space

### What is a Recursion Tree?
When a function calls itself, each call is a **node** in the tree. Understanding this tree is KEY to understanding recursion.

```
f(5)            ← call 1 (put on stack)
 └── f(4)       ← call 2 (put on stack)
      └── f(3)  ← call 3 (put on stack)
           └── f(2)  ← call 4
                └── f(1)  ← call 5
                     └── f(0)  ← BASE CASE: returns!
                ← f(1) resumes and returns
           ← f(2) resumes and returns
      ← f(3) resumes and returns
 ← f(4) resumes and returns
← f(5) resumes and returns
```

### What is the Call Stack?
Every time a function is called, Java pushes a **frame** onto the **call stack** (memory). When it returns, the frame is popped.

```
CALL STACK (grows down as functions call each other):

┌──────────┐
│  f(0)    │  ← TOP (most recent call, currently executing)
├──────────┤
│  f(1)    │
├──────────┤
│  f(2)    │
├──────────┤
│  f(3)    │
├──────────┤
│  f(4)    │
├──────────┤
│  f(5)    │  ← BOTTOM (first call)
└──────────┘
```

> **Space complexity of recursion = O(depth of recursion tree)**  
> For f(n) → f(n-1) → ... → f(0): space = O(n)

---

## 3. Base Case

> The base case is the **termination condition**. Without it, recursion never stops.

### How to Think About Base Cases

```
Ask yourself: "What is the SIMPLEST version of this problem I can answer directly?"

Problem: Sum of 1 to N
Simplest: Sum of 1 to 0 = 0   ← base case!

Problem: Factorial of N
Simplest: Factorial of 0 = 1  ← base case!

Problem: Reverse a string
Simplest: Empty string → return it  ← base case!
```

### Example — Factorial

```java
int factorial(int n) {
    // Base case: 0! = 1
    if (n == 0) return 1;

    // Recursive case: n! = n × (n-1)!
    return n * factorial(n - 1);
}

// Trace for factorial(4):
// factorial(4) = 4 × factorial(3)
//                    = 3 × factorial(2)
//                         = 2 × factorial(1)
//                              = 1 × factorial(0)
//                                   = 1  ← BASE CASE
// Returns: 1×1×2×3×4 = 24
```

### Example — Sum of 1 to N (Two Ways)

```java
// WAY 1: Going down (n to 0)
int sum(int n) {
    if (n == 0) return 0;       // base: sum of nothing = 0
    return n + sum(n - 1);      // n + sum(1 to n-1)
}

// WAY 2: Using backtracking phase (print on way UP)
void sumPrint(int i, int sum, int n) {
    if (i > n) {
        System.out.println("Total sum: " + sum);
        return;
    }
    sumPrint(i + 1, sum + i, n);  // add i to sum, go forward
}
```

---

## 4. Parameterised vs Functional Recursion

These are two **styles** of writing recursive functions. Both solve the same problem, but differently.

### Parameterised Recursion
> **Carry the answer as a PARAMETER.** The function doesn't return anything — it builds the result as it goes forward.

```
f(i=1, sum=0)
  → f(i=2, sum=1)
      → f(i=3, sum=3)
          → f(i=4, sum=6)
              → BASE CASE: print(sum=10)

Answer is printed when you HIT the base case (going DOWN).
```

```java
// Parameterised: sum = accumulator passed as parameter
void sumParameterised(int i, int sum, int n) {
    if (i > n) {
        System.out.println(sum);  // print final answer at base case
        return;
    }
    sumParameterised(i + 1, sum + i, n);  // pass accumulated sum forward
}
// Call: sumParameterised(1, 0, 5)
```

### Functional Recursion
> **Return the answer.** The function computes the result on the way BACK UP from the base case.

```
f(5)
  ← returns 5 + f(4)
             ← returns 4 + f(3)
                          ← returns 3 + f(2)
                                       ← returns 2 + f(1)
                                                    ← returns 1 + f(0)
                                                                 ← returns 0

Answer is assembled on the way BACK UP.
```

```java
// Functional: RETURN the answer
int sumFunctional(int n) {
    if (n == 0) return 0;       // base case
    return n + sumFunctional(n - 1);  // return n + answer for smaller problem
}
// Call: int result = sumFunctional(5);
```

### When to Use Which?

| Style | Use When | Example |
|-------|----------|---------|
| Parameterised | You want to print all paths / collect results | Print all subsequences |
| Functional | You want a single computed value | Sum, factorial, max depth |

> In **backtracking**, you mostly use **parameterised** style — you carry a `current` list as parameter and add/remove from it.

---

## 5. Multiple Recursion Calls

So far, each function made ONE recursive call. Now, a function makes **TWO or more** calls — this creates a **binary recursion tree**.

```
f(4)
├── f(3)                  ← left call
│   ├── f(2)
│   │   ├── f(1) → base
│   │   └── f(0) → base
│   └── f(1) → base
└── f(2)                  ← right call
    ├── f(1) → base
    └── f(0) → base
```

**Total calls = O(2^n)** — this is why backtracking can be slow for large inputs.

### Example — Fibonacci

```java
int fib(int n) {
    // Base cases
    if (n <= 1) return n;  // fib(0) = 0, fib(1) = 1

    // TWO recursive calls
    return fib(n - 1) + fib(n - 2);
}

// Recursion tree for fib(4):
//          fib(4)
//         /      \
//      fib(3)   fib(2)
//      /    \    /   \
//   fib(2) fib(1) fib(1) fib(0)
//   /   \
// fib(1) fib(0)
```

---

## 6. Subsequences — The Foundation of Backtracking

> **This is the most important concept before backtracking.** Almost every backtracking problem is "generate all subsequences with some condition."

### What is a Subsequence?
A subsequence is a subset of elements that **maintains relative order**.

```
Array: [3, 1, 2]

All subsequences:
[]         ← take nothing
[3]        ← take only 3
[1]        ← take only 1
[2]        ← take only 2
[3, 1]     ← take 3 and 1
[3, 2]     ← take 3 and 2
[1, 2]     ← take 1 and 2
[3, 1, 2]  ← take all

Total: 2^3 = 8 subsequences for n=3 elements
```

### The "Pick / Don't Pick" Pattern

> At **each element**, you have TWO choices: **include it** OR **exclude it**.

```
                      f(0, [])
                     /         \
            PICK 3              DON'T PICK 3
         f(1, [3])               f(1, [])
          /      \               /       \
     PICK 1    DONT       PICK 1       DONT
  f(2,[3,1])  f(2,[3])  f(2,[1])    f(2,[])
   /   \       /   \      /   \      /    \
PICK2 DONT  PICK2 DONT PICK2 DONT PICK2  DONT
[3,1,2][3,1][3,2] [3]  [1,2]  [1]  [2]   []
```

### 🔑 PRINT ALL SUBSEQUENCES TEMPLATE

```java
void printSubsequences(int[] arr, int index, List<Integer> current) {
    // Base case: processed all elements
    if (index == arr.length) {
        System.out.println(current);  // print current subsequence
        return;
    }

    // CHOICE 1: PICK arr[index]
    current.add(arr[index]);                          // add element
    printSubsequences(arr, index + 1, current);       // recurse

    // CHOICE 2: DON'T PICK arr[index]
    current.remove(current.size() - 1);               // undo the add (backtrack!)
    printSubsequences(arr, index + 1, current);       // recurse without it
}
// Call: printSubsequences(arr, 0, new ArrayList<>())
```

> ⚠️ **Notice `current.remove(...)` before the second call.** This is backtracking — **undo your choice** before making a different choice.

---

## 7. Three Recursion Problem Types

Striver's L7 teaches that all recursion problems fall into 3 types. The **only difference is the base case action**.

### Type 1 — Print ALL solutions

```java
void solve(int index, List<Integer> current, int[] arr) {
    if (index == arr.length) {
        System.out.println(current);  // PRINT at base case
        return;
    }
    // ... pick/don't pick ...
}
```

### Type 2 — Print ONE (first valid) solution

```java
boolean solve(int index, List<Integer> current, int[] arr, int target) {
    if (index == arr.length) {
        if (/* condition met */) {
            System.out.println(current);
            return true;   // RETURN TRUE to stop further search
        }
        return false;
    }
    // ... try choices ...
    if (solve(...)) return true;   // ← stop as soon as one found
    // ... undo, try other choices ...
    return false;
}
```

### Type 3 — COUNT all solutions

```java
int solve(int index, int[] arr, int target) {
    if (index == arr.length) {
        if (/* condition met */) return 1;  // COUNT this solution
        return 0;                           // don't count
    }
    int pick   = solve(index + 1, arr, target - arr[index]);
    int noPick = solve(index + 1, arr, target);
    return pick + noPick;   // RETURN sum of counts
}
```

---

# PART B — Backtracking

---

## 8. What is Backtracking?

**Backtracking** = Try a choice → go deeper → if it doesn't work (or you've collected the result) → **UNDO** the choice → try the next choice.

### The 3-Step Mantra (memorise this!)

```
┌─────────────────────────────────────────────────────┐
│  1. CHOOSE  — make a choice (add to current path)   │
│  2. EXPLORE — recurse deeper with that choice       │
│  3. UNCHOOSE — undo the choice (remove from path)   │
└─────────────────────────────────────────────────────┘
```

### Backtracking vs Recursion

| Recursion | Backtracking |
|-----------|-------------|
| Just calls itself | Calls itself + UNDOES choices |
| Usually returns one answer | Explores ALL possible answers |
| No "undo" step needed | MUST have an "undo" step |

### The Backtracking Tree (Decision Tree)

```
Goal: Generate all subsets of [1, 2, 3]

                     start []
                   /           \
             pick 1             skip 1
            [1]                  []
           /     \             /    \
       pick 2   skip 2     pick 2   skip 2
       [1,2]    [1]         [2]      []
       /  \     /  \       /  \     /  \
     p3  s3   p3  s3    p3   s3  p3   s3
  [1,2,3][1,2][1,3][1] [2,3] [2] [3]  []

Leaf nodes = answers: [1,2,3],[1,2],[1,3],[1],[2,3],[2],[3],[]
```

### The Universal Backtracking Template

```java
void backtrack(/* state parameters */, List<ResultType> results) {
    // STEP 1: Base case — when to record/return
    if (/* done */ ) {
        results.add(new ArrayList<>(current)); // always copy!
        return;
    }

    // STEP 2: Loop over choices
    for (int i = start; i < end; i++) {
        // CHOOSE
        current.add(choices[i]);

        // EXPLORE
        backtrack(/* updated state */, results);

        // UNCHOOSE (backtrack)
        current.remove(current.size() - 1);
    }
}
```

> ⚠️ **Always do `new ArrayList<>(current)` when adding to results!** If you add `current` directly, it gets modified later and your result will be wrong.

---

## PATTERN 1 — Subsets (Pick / Don't Pick)

### When to Use
- Generate ALL possible subsets/subsequences
- Every element can either be IN or OUT of the result
- Keywords: "all subsets", "power set", "all subsequences"

### Key Idea
```
At each index: PICK the element OR DON'T PICK it.
The result is added when you reach the END of the array.
```

### 🔑 PATTERN 1 TEMPLATE — Subsets

```java
void subsets(int[] nums, int index, List<Integer> current, List<List<Integer>> result) {
    // Base case: processed all elements — add current subset
    result.add(new ArrayList<>(current));  // add at EVERY node, not just leaf!

    // Try adding each remaining element
    for (int i = index; i < nums.length; i++) {
        current.add(nums[i]);                         // CHOOSE
        subsets(nums, i + 1, current, result);        // EXPLORE (i+1 = no reuse)
        current.remove(current.size() - 1);           // UNCHOOSE
    }
}
```

> ⚠️ Notice: `result.add(...)` is at the **TOP** (before the loop), not at the base case leaf. This captures subsets of ALL sizes including empty `[]`.

---

### Example A — Subsets (No Duplicates in Input)

**Input:** `nums = [1, 2, 3]`
**Output:** `[[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]`

```
                []        ← add [] to result
              / | \
            1   2   3
           /|    \    \
        [1] |   [2]  [3]
          [1,2] [1,3]
         [1,2,3]

At each node, add current to result.
```

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, int start, List<Integer> current,
                           List<List<Integer>> result) {
        result.add(new ArrayList<>(current));  // add current subset (every time!)

        for (int i = start; i < nums.length; i++) {
            current.add(nums[i]);              // CHOOSE
            backtrack(nums, i + 1, current, result);  // EXPLORE
            current.remove(current.size() - 1);       // UNCHOOSE
        }
    }
}
// Time: O(n × 2^n) — 2^n subsets, each takes O(n) to copy
// Space: O(n) — recursion depth
```

---

### Example B — Subsets II (Duplicates in Input → Skip Duplicates)

**Input:** `nums = [1, 2, 2]`
**Output:** `[[], [1], [1,2], [1,2,2], [2], [2,2]]` ← no duplicate subsets!

**Problem:** `[2, 2]` appears twice in input → would generate `[2]` twice without fix.

**Fix:** Sort first, then skip `nums[i] == nums[i-1]` at the same recursion level.

```
nums = [1, 2, 2]  (after sorting)

At index 1, we pick nums[1]=2:   produces [2], [2,2]
At index 1, we also see nums[2]=2 BUT nums[2]==nums[1]!
→ SKIP it at this level (would produce duplicate [2] again)

RULE: if (i > start && nums[i] == nums[i-1]) → skip
      ↑ "i > start" means we're NOT the first choice at this level
```

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);  // MUST sort first to group duplicates together
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, int start, List<Integer> current,
                           List<List<Integer>> result) {
        result.add(new ArrayList<>(current));

        for (int i = start; i < nums.length; i++) {
            // SKIP duplicate at the same recursion level
            if (i > start && nums[i] == nums[i - 1]) continue;

            current.add(nums[i]);
            backtrack(nums, i + 1, current, result);
            current.remove(current.size() - 1);
        }
    }
}
// Time: O(n × 2^n)  |  Space: O(n)
```

**Visual of the skip:**
```
nums = [1, 2, 2]

Level 0 (start=0): try i=0(1), i=1(2), i=2(2←SKIP, 2==2 and i>start)
Level 1 (start=1): try i=1(2), i=2(2←SKIP, 2==2 and i>start)
Level 2 (start=2): try i=2(2)

Result: [], [1], [1,2], [1,2,2], [2], [2,2]  ✅
```

---

## PATTERN 2 — Combinations (Start Index)

### When to Use
- Pick elements to form combinations that meet a **target or condition**
- Order does NOT matter: `[1,2]` and `[2,1]` are the same
- Keywords: "combination", "sum equals target", "pick k elements"

### Key Differences in Combination Variants

```
Variant A (Combination Sum):    Can reuse same element → recurse with same i
Variant B (Combination Sum II): Cannot reuse, has duplicates → skip duplicates
Variant C (Phone Number):       Mapping from digit → letters, no "start index"
```

### 🔑 PATTERN 2 TEMPLATE

```java
void combinations(int[] nums, int start, int target, List<Integer> current,
                  List<List<Integer>> result) {
    // Base case: found a valid combination
    if (target == 0) {
        result.add(new ArrayList<>(current));
        return;
    }
    // Pruning: if target < 0, no point continuing
    if (target < 0) return;

    for (int i = start; i < nums.length; i++) {
        // (optional: skip duplicates for Combination Sum II)

        current.add(nums[i]);               // CHOOSE
        combinations(nums,
                     /* i or i+1 */,        // i = reuse allowed, i+1 = no reuse
                     target - nums[i],
                     current, result);
        current.remove(current.size() - 1); // UNCHOOSE
    }
}
```

---

### Example A — Combination Sum (Reuse Allowed, No Duplicates in Input)

**Input:** `candidates = [2, 3, 6, 7]`, `target = 7`
**Output:** `[[2,2,3], [7]]`

```
Key: same element CAN be used multiple times → recurse with same i (not i+1)

                        f(start=0, rem=7)
                   /        |        |       \
              pick 2      pick 3   pick 6   pick 7
           f(0,rem=5)   f(1,rem=4) ...    f(3,rem=0)←✅[7]
           /    |                  
        pick2  pick3    
     f(0,rem=3) ...
     /   |
  pick2 pick3
f(0,1) f(1,rem=0)←✅[2,2,3]
  |
pick2
f(0,rem=-1)←❌ target<0, prune!
```

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(candidates, 0, target, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] candidates, int start, int remaining,
                           List<Integer> current, List<List<Integer>> result) {
        if (remaining == 0) {
            result.add(new ArrayList<>(current));  // found valid combination!
            return;
        }
        if (remaining < 0) return;  // pruning: overshot, stop this path

        for (int i = start; i < candidates.length; i++) {
            current.add(candidates[i]);
            backtrack(candidates, i, remaining - candidates[i], current, result);
            //                   ↑ same i = reuse same element allowed
            current.remove(current.size() - 1);
        }
    }
}
// Time: O(2^(t/min)) where t=target, min=smallest candidate
// Space: O(t/min) — max recursion depth
```

---

### Example B — Combination Sum II (No Reuse, Duplicates in Input)

**Input:** `candidates = [10,1,2,7,6,1,5]`, `target = 8`
**Output:** `[[1,1,6], [1,2,5], [1,7], [2,6]]`

**Two differences from Combination Sum:**
1. Each element used at most once → `i + 1` in recursion
2. Input has duplicates → sort + skip duplicates (same trick as Subsets II)

```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);  // MUST sort to group duplicates
        List<List<Integer>> result = new ArrayList<>();
        backtrack(candidates, 0, target, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] candidates, int start, int remaining,
                           List<Integer> current, List<List<Integer>> result) {
        if (remaining == 0) {
            result.add(new ArrayList<>(current));
            return;
        }
        if (remaining < 0) return;

        for (int i = start; i < candidates.length; i++) {
            // Skip duplicate at same recursion level
            if (i > start && candidates[i] == candidates[i - 1]) continue;

            current.add(candidates[i]);
            backtrack(candidates, i + 1, remaining - candidates[i], current, result);
            //                   ↑ i+1 = cannot reuse same element
            current.remove(current.size() - 1);
        }
    }
}
// Time: O(2^n)  |  Space: O(n)
```

---

### Example C — Letter Combinations of a Phone Number

**Input:** `digits = "23"`
**Output:** `["ad","ae","af","bd","be","bf","cd","ce","cf"]`

```
Phone mapping:
2 → [a, b, c]
3 → [d, e, f]
4 → [g, h, i]  etc.

This is a combinations problem but with a MAP, not an array.
For each digit, try each letter mapped to it.
```

```
f(index=0, current="")
├── pick 'a' → f(index=1, current="a")
│              ├── pick 'd' → f(index=2, "ad") ← BASE CASE, add "ad"
│              ├── pick 'e' → f(index=2, "ae") ← BASE CASE, add "ae"
│              └── pick 'f' → f(index=2, "af") ← BASE CASE, add "af"
├── pick 'b' → f(index=1, current="b")
│              ├── "bd", "be", "bf"
└── pick 'c' → f(index=1, current="c")
               ├── "cd", "ce", "cf"
```

```java
class Solution {
    // Map each digit to its letters
    private static final Map<Character, String> PHONE_MAP = Map.of(
        '2', "abc", '3', "def", '4', "ghi", '5', "jkl",
        '6', "mno", '7', "pqrs", '8', "tuv", '9', "wxyz"
    );

    public List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        if (digits == null || digits.isEmpty()) return result;
        backtrack(digits, 0, new StringBuilder(), result);
        return result;
    }

    private void backtrack(String digits, int index, StringBuilder current,
                           List<String> result) {
        // Base case: processed all digits
        if (index == digits.length()) {
            result.add(current.toString());
            return;
        }

        // Get letters for current digit
        String letters = PHONE_MAP.get(digits.charAt(index));

        for (char letter : letters.toCharArray()) {
            current.append(letter);                        // CHOOSE
            backtrack(digits, index + 1, current, result);// EXPLORE
            current.deleteCharAt(current.length() - 1);   // UNCHOOSE
        }
    }
}
// Time: O(4^n × n) — 4 letters per digit worst case, n digits
// Space: O(n)
```

---

## PATTERN 3 — Permutations (Order Matters)

### When to Use
- All **arrangements** of elements where **order matters**
- `[1,2]` and `[2,1]` are DIFFERENT permutations
- Keywords: "all permutations", "all arrangements"
- Total permutations of n elements = n! (n factorial)

### Key Difference from Combinations
```
Combinations: [1,2] == [2,1] (same set)  → use start index to avoid going back
Permutations: [1,2] != [2,1] (different) → can use ANY unused element each time
```

### Two Approaches

**Approach 1 — Visited Array (Extra Space, Striver Approach 1)**
```
Use a boolean[] visited to track which elements are used.
At each step, try every UNVISITED element.
```

**Approach 2 — Swap In Place (No Extra Space, Striver Approach 2)**
```
Swap current position with each position ahead.
After recursion, swap back (backtrack).
```

### 🔑 PATTERN 3 TEMPLATE — Approach 1 (Visited Array)

```java
void permutations(int[] nums, boolean[] visited, List<Integer> current,
                  List<List<Integer>> result) {
    // Base case: used all elements
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }

    for (int i = 0; i < nums.length; i++) {
        if (visited[i]) continue;  // skip already used elements

        visited[i] = true;          // CHOOSE: mark as used
        current.add(nums[i]);
        permutations(nums, visited, current, result);  // EXPLORE
        current.remove(current.size() - 1);            // UNCHOOSE
        visited[i] = false;         // UNCHOOSE: unmark
    }
}
```

---

### Example — Permutations

**Input:** `nums = [1, 2, 3]`
**Output:** `[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]`

```
Level 0: try 1, 2, 3 (all unused)
Level 1 (chose 1): try 2, 3 (1 is visited)
Level 2 (chose 1,2): try 3 (1,2 are visited)
Level 3 (chose 1,2,3): BASE CASE → add [1,2,3]

Full tree (partial):
                f([], visited=[F,F,F])
          /           |           \
      pick1         pick2        pick3
  f([1],[T,F,F])  f([2],[F,T,F]) f([3],[F,F,T])
    /     \           ...
  pick2  pick3
f([1,2]) f([1,3])
   |         |
 pick3     pick2
[1,2,3]  [1,3,2]    etc.
```

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        boolean[] visited = new boolean[nums.length];
        backtrack(nums, visited, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, boolean[] visited, List<Integer> current,
                           List<List<Integer>> result) {
        if (current.size() == nums.length) {
            result.add(new ArrayList<>(current));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            if (visited[i]) continue;  // skip used

            visited[i] = true;
            current.add(nums[i]);
            backtrack(nums, visited, current, result);
            current.remove(current.size() - 1);
            visited[i] = false;
        }
    }
}
// Time: O(n! × n)  |  Space: O(n) for visited + recursion
```

### Approach 2 — Swap (More Space Efficient)

```java
void backtrack(int[] nums, int start, List<List<Integer>> result) {
    if (start == nums.length) {
        // Convert array to list and add
        List<Integer> perm = new ArrayList<>();
        for (int n : nums) perm.add(n);
        result.add(perm);
        return;
    }
    for (int i = start; i < nums.length; i++) {
        swap(nums, start, i);              // CHOOSE: swap start with i
        backtrack(nums, start + 1, result);// EXPLORE
        swap(nums, start, i);              // UNCHOOSE: swap back
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
}
```

---

## PATTERN 4 — Constraint-Based Building

### When to Use
- Build a string/path character by character with **constraints** on what you can add
- Not "pick from a pool" but "build step by step with rules"
- Keywords: "generate valid X", "all valid arrangements of Y", "partition into Z"
- Problems: Generate Parentheses, Word Search, Palindrome Partitioning

---

### Example A — Generate Parentheses

**Problem:** Given n=3, generate all valid parenthesis combinations.
**Output:** `["((()))","(()())","(())()","()(())","()()()"]`

**Constraints:**
```
A string of 2n characters using '(' and ')'
Rules:
  1. Can add '(' if open count < n
  2. Can add ')' if close count < open count (can't close what's not open!)
  3. Done when open == close == n
```

```
n=2, trace:
f(open=0, close=0, current="")
├── add '(' → f(1,0,"(")
│   ├── add '(' → f(2,0,"((")
│   │   └── add ')' → f(2,1,"(()")
│   │       └── add ')' → f(2,2,"(())") ← BASE CASE ✅
│   └── add ')' → f(1,1,"()")
│       └── add '(' → f(2,1,"()(")
│           └── add ')' → f(2,2,"()()") ← BASE CASE ✅
```

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(0, 0, n, new StringBuilder(), result);
        return result;
    }

    private void backtrack(int open, int close, int n,
                           StringBuilder current, List<String> result) {
        // Base case: used n open and n close brackets
        if (open == n && close == n) {
            result.add(current.toString());
            return;
        }

        // Add '(' if we haven't used n open brackets yet
        if (open < n) {
            current.append('(');
            backtrack(open + 1, close, n, current, result);
            current.deleteCharAt(current.length() - 1);  // backtrack
        }

        // Add ')' only if close count < open count
        if (close < open) {
            current.append(')');
            backtrack(open, close + 1, n, current, result);
            current.deleteCharAt(current.length() - 1);  // backtrack
        }
    }
}
// Time: O(4^n / sqrt(n)) — Catalan number
// Space: O(n) recursion depth
```

---

### Example B — Word Search

**Problem:** Does word exist as a path in the grid? Can move up/down/left/right. Can't reuse a cell.

```
Grid:                 Find: "ABCCED"
[A, B, C, E]
[S, F, C, S]         A→B→C→C→E→D  ✅
[A, D, E, E]
```

**Key Idea:**
```
At each cell, try going in all 4 directions.
Mark cell as VISITED before recursing (can't reuse).
UNMARK it after (backtrack) so other paths can use it.
```

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        int rows = board.length, cols = board[0].length;

        // Try starting from every cell
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (dfs(board, word, r, c, 0)) return true;
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int r, int c, int index) {
        // Base case: matched all characters!
        if (index == word.length()) return true;

        // Boundary checks + character match
        if (r < 0 || r >= board.length) return false;
        if (c < 0 || c >= board[0].length) return false;
        if (board[r][c] != word.charAt(index)) return false;

        // CHOOSE: mark as visited (temporarily)
        char temp = board[r][c];
        board[r][c] = '#';   // '#' means "visited"

        // EXPLORE: try all 4 directions
        boolean found = dfs(board, word, r + 1, c, index + 1) ||
                        dfs(board, word, r - 1, c, index + 1) ||
                        dfs(board, word, r, c + 1, index + 1) ||
                        dfs(board, word, r, c - 1, index + 1);

        // UNCHOOSE: restore cell (backtrack)
        board[r][c] = temp;

        return found;
    }
}
// Time: O(rows × cols × 4^wordLen)
// Space: O(wordLen) — recursion depth
```

**Visualising the visited marking:**
```
Searching for "ABC" starting at (0,0):

Step 1: Visit A(0,0) → mark as #
[#, B, C, E]

Step 2: Move right, visit B(0,1) → mark as #
[#, #, C, E]

Step 3: Move right, visit C(0,2) → mark as #
[#, #, #, E]  → index=3=word.length → return TRUE!

BACKTRACK restores:
[A, B, C, E]  ← all restored
```

---

### Example C — Palindrome Partitioning

**Problem:** Partition string `s` into substrings where EVERY substring is a palindrome.

**Input:** `s = "aab"`
**Output:** `[["a","a","b"], ["aa","b"]]`

**Key Idea:**
```
At each position, try all substrings starting from current position.
If substring s[start..end] is a palindrome → include it, recurse from end+1.
If not palindrome → skip it.
```

```
s = "aab"

f(start=0):
  try "a"(0,0) → palindrome! → f(start=1)
      try "a"(1,1) → palindrome! → f(start=2)
          try "b"(2,2) → palindrome! → f(start=3) ← BASE CASE → add ["a","a","b"]
      try "ab"(1,2) → NOT palindrome → skip
  try "aa"(0,1) → palindrome! → f(start=2)
      try "b"(2,2) → palindrome! → f(start=3) ← BASE CASE → add ["aa","b"]
  try "aab"(0,2) → NOT palindrome → skip
```

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        backtrack(s, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(String s, int start, List<String> current,
                           List<List<String>> result) {
        // Base case: processed the whole string
        if (start == s.length()) {
            result.add(new ArrayList<>(current));
            return;
        }

        // Try every possible end for the current partition
        for (int end = start; end < s.length(); end++) {
            // Only proceed if substring is a palindrome
            if (isPalindrome(s, start, end)) {
                current.add(s.substring(start, end + 1));  // CHOOSE
                backtrack(s, end + 1, current, result);    // EXPLORE
                current.remove(current.size() - 1);         // UNCHOOSE
            }
        }
    }

    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }
}
// Time: O(n × 2^n) — 2^n partitions, O(n) palindrome check each
// Space: O(n)
```

---

## PATTERN 5 — Hard Grid / Constraint Placement

### When to Use
- Place items on a board/grid satisfying strict rules
- Can't just "pick or skip" — must validate complex constraints
- Keywords: "N-Queens", "Sudoku", "place X such that no two conflict"

### The Key Difference from Other Patterns
```
Other patterns: constraint is simple (sum == target, element not used before)
This pattern:   constraint is complex (no queen attacks another, no repeated digit in row/col/box)

The structure is the same:
  for each position:
    if valid(place here):
      place it
      recurse
      remove it
```

---

### Example — N Queens

**Problem:** Place N queens on an N×N board so no two queens attack each other.

```
A queen attacks in ALL 8 directions:
↑ ↗ → ↘ ↓ ↙ ← ↖

For N=4:
. Q . .
. . . Q
Q . . .
. . Q .
```

**Strategy:**
```
Place one queen per ROW (we know exactly one queen per row).
For each row, try each COLUMN.
A placement is VALID if:
  1. No queen in the same COLUMN
  2. No queen on the same UPPER-LEFT diagonal (row - col = constant)
  3. No queen on the same UPPER-RIGHT diagonal (row + col = constant)
  (We don't need to check rows below — we haven't placed there yet)
```

**Diagonal Math:**
```
Board:          Upper-left diag (row-col):    Upper-right diag (row+col):
0 1 2 3         -3 -2 -1  0                   0  1  2  3
1 2 3 4         -2 -1  0  1                   1  2  3  4
2 3 4 5         -1  0  1  2                   2  3  4  5
3 4 5 6          0  1  2  3                   3  4  5  6

Cells on same upper-left diagonal share (row - col) value.
Cells on same upper-right diagonal share (row + col) value.
```

```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> result = new ArrayList<>();
        char[][] board = new char[n][n];
        for (char[] row : board) Arrays.fill(row, '.');  // fill with empty

        Set<Integer> cols    = new HashSet<>(); // columns with queens
        Set<Integer> diagUL  = new HashSet<>(); // upper-left diagonals (row-col)
        Set<Integer> diagUR  = new HashSet<>(); // upper-right diagonals (row+col)

        backtrack(board, 0, n, cols, diagUL, diagUR, result);
        return result;
    }

    private void backtrack(char[][] board, int row, int n,
                           Set<Integer> cols, Set<Integer> diagUL, Set<Integer> diagUR,
                           List<List<String>> result) {
        // Base case: placed queens in all n rows
        if (row == n) {
            result.add(buildBoard(board));
            return;
        }

        // Try placing queen in each column of current row
        for (int col = 0; col < n; col++) {
            // Check if this position is under attack
            if (cols.contains(col))         continue; // same column
            if (diagUL.contains(row - col)) continue; // same upper-left diagonal
            if (diagUR.contains(row + col)) continue; // same upper-right diagonal

            // CHOOSE: place queen
            board[row][col] = 'Q';
            cols.add(col);
            diagUL.add(row - col);
            diagUR.add(row + col);

            // EXPLORE: place queen in next row
            backtrack(board, row + 1, n, cols, diagUL, diagUR, result);

            // UNCHOOSE: remove queen
            board[row][col] = '.';
            cols.remove(col);
            diagUL.remove(row - col);
            diagUR.remove(row + col);
        }
    }

    private List<String> buildBoard(char[][] board) {
        List<String> boardState = new ArrayList<>();
        for (char[] row : board) boardState.add(new String(row));
        return boardState;
    }
}
// Time: O(n!) — at most n options for row 0, n-1 for row 1, ...
// Space: O(n²) — board storage
```

---

## How to Identify the Pattern

```
READ the problem. Ask these questions in order:

START
  │
  ▼
Does the problem ask for arrangements where ORDER MATTERS?
(e.g. "all permutations", [1,2] ≠ [2,1])
  ├── YES → PATTERN 3 (Permutations)
  │         Use visited[] array, try all unvisited elements each time
  └── NO ↓

Does the problem involve PLACING items on a GRID or BOARD with complex validity rules?
(e.g. N-Queens, Word Search, Sudoku)
  ├── YES (placement/board) → PATTERN 5 (Grid/Hard Constraint)
  │         Mark visited on grid, try all 4 directions (Word Search)
  │         OR validate row/col/diagonal (N-Queens)
  └── NO ↓

Does the problem ask to BUILD something character by character with RULES?
(e.g. valid parentheses, palindrome partitions, phone letters)
  ├── YES → PATTERN 4 (Constraint-Based Building)
  │         Track open/close counts (Generate Parentheses)
  │         OR check palindrome before adding (Palindrome Partitioning)
  │         OR use a phone map to get letter choices (Letter Combinations)
  └── NO ↓

Does the problem ask for combinations that SUM to a target?
  ├── YES → PATTERN 2 (Combinations with Start Index)
  │         Reuse allowed? → recurse with same i
  │         No reuse, duplicates? → sort + skip + recurse with i+1
  └── NO ↓

Default → PATTERN 1 (Subsets / Pick-or-Don't-Pick)
          Add to result at EVERY node (not just leaves)
          Duplicates in input? → sort + skip with (i > start && nums[i]==nums[i-1])
```

---

## Problem → Pattern Mapping

| Problem | Pattern | Key Trick |
|---------|---------|-----------|
| Subsets | Pattern 1 | Add result at every node, `i+1` prevents reuse |
| Subsets II | Pattern 1 | Sort + `if (i > start && nums[i]==nums[i-1]) skip` |
| Combination Sum | Pattern 2A | Reuse: recurse with same `i`, prune if `remaining < 0` |
| Combination Sum II | Pattern 2B | No reuse: `i+1`, sort + skip duplicates |
| Letter Combinations | Pattern 2C | Map digit→letters, try each letter for each digit |
| Permutations | Pattern 3 | `visited[]` array, try all unvisited at each step |
| Generate Parentheses | Pattern 4 | Track `open` and `close` counts, apply rules |
| Word Search | Pattern 4/5 | DFS on grid, mark `#` as visited, unmark on backtrack |
| Palindrome Partitioning | Pattern 4 | Try every `end`, only proceed if palindrome |
| N Queens | Pattern 5 | One queen per row, check col + two diagonals with Sets |

---

## Complexity Cheat Sheet

| Problem | Time | Space | Notes |
|---------|------|-------|-------|
| Subsets | O(n × 2ⁿ) | O(n) | 2ⁿ subsets, O(n) to copy each |
| Subsets II | O(n × 2ⁿ) | O(n) | Sorting doesn't change overall complexity |
| Combination Sum | O(2^(t/m)) | O(t/m) | t=target, m=min candidate |
| Combination Sum II | O(2ⁿ) | O(n) | At most one of each element |
| Letter Combinations | O(4ⁿ × n) | O(n) | 4 letters per digit worst case |
| Permutations | O(n! × n) | O(n) | n! permutations, O(n) to copy each |
| Generate Parentheses | O(4ⁿ/√n) | O(n) | Catalan number of valid strings |
| Word Search | O(m × n × 4^L) | O(L) | m×n cells, L=word length |
| Palindrome Partitioning | O(n × 2ⁿ) | O(n) | 2ⁿ partitions, O(n) palindrome check |
| N Queens | O(n!) | O(n²) | n options row 0, n-1 row 1, ... |

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Not copying the list before adding to results
```java
// WRONG — adds a reference, list gets modified later!
result.add(current);

// CORRECT — add a COPY
result.add(new ArrayList<>(current));  // ← always copy!

// Same for strings — StringBuilder needs .toString()
result.add(current.toString());
```

### ❌ Mistake 2: Forgetting to undo the choice (no backtrack)
```java
// WRONG — never undoes the choice
current.add(nums[i]);
backtrack(nums, i + 1, current, result);
// ← forgot to remove! current keeps growing forever

// CORRECT
current.add(nums[i]);                          // CHOOSE
backtrack(nums, i + 1, current, result);       // EXPLORE
current.remove(current.size() - 1);            // UNCHOOSE ← essential!
```

### ❌ Mistake 3: Subsets — adding result only at the leaf (base case)
```java
// WRONG — only adds subsets of full length
void backtrack(int start, ...) {
    if (start == nums.length) {
        result.add(new ArrayList<>(current));  // only adds full-length subsets
        return;
    }
    ...
}

// CORRECT — add at EVERY call (captures all subset sizes)
void backtrack(int start, ...) {
    result.add(new ArrayList<>(current));  // ← at the TOP, captures []  [1]  [1,2]  etc.
    for (int i = start; ...) {
        ...
    }
}
```

### ❌ Mistake 4: Combination Sum — using i+1 instead of i (forbids reuse)
```java
// WRONG for Combination Sum (where reuse is allowed)
backtrack(candidates, i + 1, remaining - candidates[i], current, result);
//                   ↑ i+1 means "don't reuse", wrong!

// CORRECT for Combination Sum (reuse allowed)
backtrack(candidates, i, remaining - candidates[i], current, result);
//                   ↑ same i = can reuse same element
```

### ❌ Mistake 5: Skipping duplicate check at wrong condition
```java
// WRONG — skips the FIRST occurrence of a duplicate
if (i >= start && nums[i] == nums[i - 1]) continue;
//   ↑ >= means skip even i==start (first element at this level)

// CORRECT — only skip DUPLICATE occurrences (i > start)
if (i > start && nums[i] == nums[i - 1]) continue;
//   ↑ strictly greater: i==start means it's the FIRST at this level, keep it
```

### ❌ Mistake 6: Word Search — not restoring the cell after backtracking
```java
// WRONG — cell stays marked, other paths can't use it
board[r][c] = '#';  // mark
dfs(board, word, r+1, c, index+1);
// ← forgot to restore!

// CORRECT
char temp = board[r][c];
board[r][c] = '#';               // mark
dfs(board, word, r+1, c, index+1);
board[r][c] = temp;              // restore ← essential!
```

### ❌ Mistake 7: N-Queens — only checking immediate neighbours, not whole column
```java
// WRONG — only checks cells directly adjacent
if (board[row-1][col] == 'Q') ...  // just checks one cell above

// CORRECT — use Sets to track attacked columns/diagonals globally
Set<Integer> cols = new HashSet<>();
// if cols.contains(col) → whole column is attacked, not just adjacent cell
```

---

## 📋 Quick Reference Card

```
┌──────────────────────────────────────────────────────────────────────┐
│              RECURSION & BACKTRACKING QUICK CARD                     │
├────────────────────┬─────────────────────────────────────────────────┤
│ BASE CASE          │ Always write this FIRST.                        │
│                    │ "What is the simplest case I can answer?"       │
├────────────────────┼─────────────────────────────────────────────────┤
│ 3-STEP MANTRA      │ CHOOSE → EXPLORE → UNCHOOSE                    │
│                    │ (Add → Recurse → Remove)                        │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 1          │ Subsets: add result at TOP (every node)         │
│                    │ Duplicates: sort + skip if i>start & nums[i-1]  │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 2          │ Combinations: start index prevents reuse        │
│                    │ Reuse allowed? → same i. Not allowed? → i+1     │
│                    │ Duplicates? → sort + skip                        │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 3          │ Permutations: visited[] array                   │
│                    │ Try ALL elements but skip if visited[i]         │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 4          │ Constraint-based: build step by step            │
│                    │ Parentheses: track open/close counts            │
│                    │ Partition: check palindrome before adding       │
│                    │ Phone: Map digit → letters, loop each letter    │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 5          │ Grid/Hard: mark visited, validate constraints   │
│                    │ Word Search: mark '#', restore after            │
│                    │ N-Queens: Sets for col, diagUL, diagUR          │
├────────────────────┼─────────────────────────────────────────────────┤
│ NEVER FORGET       │ new ArrayList<>(current) when adding to result  │
│                    │ Remove/restore after every recursive call        │
└────────────────────┴─────────────────────────────────────────────────┘
```

---

## 🔁 Recursion Pattern Summary

```
┌────────────────────────────────────────────────────────────────┐
│            ALL RECURSION PROBLEM TYPES                         │
├──────────────────┬─────────────────────────────────────────────┤
│ Print ALL        │ At base case: print/add to list             │
│                  │ Don't return early — explore all paths      │
├──────────────────┼─────────────────────────────────────────────┤
│ Print ONE        │ Return boolean. If found: return true       │
│                  │ Stops as soon as first answer found         │
├──────────────────┼─────────────────────────────────────────────┤
│ Count solutions  │ Return int. At base: return 1 if valid, 0  │
│                  │ Return sum of all recursive calls           │
├──────────────────┼─────────────────────────────────────────────┤
│ Parameterised    │ Carry result as parameter. Print at base    │
├──────────────────┼─────────────────────────────────────────────┤
│ Functional       │ Return result. Combine on way back up       │
└──────────────────┴─────────────────────────────────────────────┘
```

---

*Notes Version 1.0 — Covers all of Striver's Recursion series (L1-L7, L8-L13, L14, L17, L18, L19) + all 10 NeetCode Backtracking problems using 5 patterns.*
*Revise order: Recursion basics → Pattern 1 (Subsets) → Pattern 2 (Combinations) → Pattern 3 (Permutations) → Pattern 4 → Pattern 5 (N-Queens)*
