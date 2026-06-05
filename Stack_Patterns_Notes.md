# 📚 Stack — Complete Beginner Notes
> **Goal:** Learn the pattern, not the problem. Master 5 core patterns to solve all 6 NeetCode Stack problems + essential Striver Stack & Queue algorithms. Notes are fully self-sufficient — no lecture needed.

---

## 📦 Table of Contents
**PART A — Stack Fundamentals**
1. [What is a Stack?](#1-what-is-a-stack)
2. [Stack in Java — Three Ways](#2-stack-in-java)
3. [What is a Monotonic Stack?](#3-what-is-a-monotonic-stack)

**PART B — 5 Core Patterns**
4. [Pattern 1 — Bracket Matching / Validation](#pattern-1--bracket-matching--validation)
5. [Pattern 2 — Stack as Design (Augmented Stack / Evaluator)](#pattern-2--stack-as-design)
6. [Pattern 3 — Monotonic Stack: NGE / PSE](#pattern-3--monotonic-stack-nge--pse)
7. [Pattern 4 — Monotonic Stack: Area / Contribution](#pattern-4--monotonic-stack-area--contribution)
8. [Pattern 5 — Stack-Based Simulation / Greedy](#pattern-5--stack-based-simulation--greedy)
9. [How to Identify the Pattern (Decision Tree)](#how-to-identify-the-pattern)
10. [Problem → Pattern Mapping](#problem--pattern-mapping)
11. [Complexity Cheat Sheet](#complexity-cheat-sheet)
12. [Common Mistakes to Avoid](#common-mistakes-to-avoid)

---

# PART A — Stack Fundamentals

---

## 1. What is a Stack?

A stack is a **Last In, First Out (LIFO)** data structure. Like a stack of plates — you always add and remove from the TOP.

```
PUSH 1 → PUSH 2 → PUSH 3 → POP → POP

After 3 pushes:        After POP:       After POP:
    ┌───┐                               
    │ 3 │  ← TOP       ┌───┐            ┌───┐
    ├───┤               │ 2 │  ← TOP     │ 1 │  ← TOP
    │ 2 │               ├───┤            └───┘
    ├───┤               │ 1 │
    │ 1 │               └───┘
    └───┘

POP returned 3        POP returns 2
```

### Core Operations

| Operation | Time | Description |
|-----------|------|-------------|
| `push(x)` | O(1) | Add element to top |
| `pop()` | O(1) | Remove and return top element |
| `peek()` / `top()` | O(1) | View top without removing |
| `isEmpty()` | O(1) | Is the stack empty? |

### When is Stack the Right Tool?
```
Think STACK when you see:
  ✅ "matching" problems: brackets, parentheses, tags
  ✅ "undo" / "cancel" problems: asteroid collision, remove k digits
  ✅ "nearest greater/smaller" problems: daily temperatures, histogram
  ✅ "evaluate expression" problems: postfix, RPN
  ✅ anything needing LAST element seen (most recent context matters)
```

---

## 2. Stack in Java

### Option A — `Deque<Integer>` as Stack (PREFERRED in interviews)

```java
// Best practice — Deque<Integer> used as a stack
Deque<Integer> stack = new ArrayDeque<>();

stack.push(1);          // push to top (adds to FRONT of deque)
stack.push(2);
stack.push(3);

stack.peek();           // view top → 3 (does NOT remove)
stack.pop();            // remove and return top → 3
stack.isEmpty();        // false
stack.size();           // 2
```

> ⚠️ Prefer `ArrayDeque` over `Stack` class. `Stack` is legacy and synchronized (slower). `ArrayDeque` is modern and faster.

### Option B — `Stack<Integer>` (legacy, avoid)

```java
Stack<Integer> stack = new Stack<>();
stack.push(1);
stack.peek();    // view top
stack.pop();     // remove top
stack.empty();   // is empty?
```

### Option C — `List<Integer>` as Stack (when you also need index access)

```java
List<Integer> stack = new ArrayList<>();
stack.add(x);                          // push
stack.get(stack.size() - 1);          // peek
stack.remove(stack.size() - 1);       // pop
stack.isEmpty();
```

---

## 3. What is a Monotonic Stack?

> **This is the most important concept in the Stack chapter.** ~60% of stack problems use it.

A **monotonic stack** is a stack that is always kept in either:
- **Strictly increasing order** from bottom to top, OR
- **Strictly decreasing order** from bottom to top

by popping elements that violate the order before pushing a new one.

### Monotonic Increasing Stack

```
Elements are pushed only if they are GREATER than (or equal to) the current top.
Smaller elements cause POPS until the stack is valid again.

Processing [3, 1, 4, 1, 5, 9, 2, 6]:

push 3: [3]
push 1: 1 < 3 → pop 3. [1]         ← 3 was "blocked" by 1
push 4: 4 > 1, push. [1, 4]
push 1: 1 < 4 → pop 4. 1 ≤ 1 → pop 1. [1]
push 5: [1, 5]
push 9: [1, 5, 9]
push 2: 2 < 9 → pop 9. 2 < 5 → pop 5. 2 > 1, push. [1, 2]
push 6: [1, 2, 6]

Stack always has INCREASING values bottom → top.
```

### Monotonic Decreasing Stack

```
Elements are pushed only if they are SMALLER than (or equal to) the current top.
Larger elements cause POPS.

Processing [3, 1, 4, 1, 5]:

push 3: [3]
push 1: 1 < 3, push. [3, 1]
push 4: 4 > 1 → pop 1. 4 > 3 → pop 3. [4]
push 1: 1 < 4, push. [4, 1]
push 5: 5 > 1 → pop 1. 5 > 4 → pop 4. [5]

Stack always has DECREASING values bottom → top.
```

### The Key Insight — WHY Monotonic Stacks Work

```
When you POP an element because a new element is larger (for increasing stack):
→ The NEW element is the NEXT GREATER ELEMENT for the popped element!

When you POP an element because a new element is smaller (for decreasing stack):
→ The NEW element is the NEXT SMALLER ELEMENT for the popped element!

This is how we find NGE/NSE for ALL elements in O(n):
  each element is pushed once and popped once → O(n) total.
```

---

# PART B — 5 Core Patterns

---

## PATTERN 1 — Bracket Matching / Validation

### When to Use
- Check if brackets/parentheses are **balanced**
- Match opening with closing brackets
- Keywords: "valid parentheses", "balanced brackets", "matching tags"

### The Core Logic
```
For every OPENING bracket → PUSH it onto the stack.
For every CLOSING bracket → CHECK if top of stack is the matching opener.
  If yes → POP (matched pair found)
  If no  → INVALID (mismatch or stack is empty)

At the end: stack must be EMPTY (every opener was matched).
```

### 🔑 PATTERN 1 TEMPLATE

```java
boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();

    for (char c : s.toCharArray()) {
        if (isOpening(c)) {
            stack.push(c);                   // push opening bracket
        } else {
            if (stack.isEmpty()) return false;  // no opener to match
            if (!matches(stack.pop(), c)) return false;  // mismatch
        }
    }
    return stack.isEmpty();  // all openers must have been matched
}
```

---

### Example A — Valid Parentheses

**Problem:** Return true if `()[]{}` are all properly matched and closed.

```
"()[]{}"  → true
"([)]"    → false   (wrong order of closing)
"{[]}"    → true
"("       → false   (unclosed)
```

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();

        for (char c : s.toCharArray()) {
            // Opening bracket → push
            if (c == '(' || c == '[' || c == '{') {
                stack.push(c);
            } else {
                // Closing bracket → check for matching opener
                if (stack.isEmpty()) return false;  // nothing to match

                char top = stack.pop();
                if (c == ')' && top != '(') return false;
                if (c == ']' && top != '[') return false;
                if (c == '}' && top != '{') return false;
            }
        }
        return stack.isEmpty();  // unmatched openers remaining → invalid
    }
}
// Time: O(n)  |  Space: O(n)
```

**Trace for `"([)]"`:**
```
c='(': push → stack=['(']
c='[': push → stack=['(','[']
c=')': pop='['. Does ')' match '['? NO → return false ✅
```

**Trace for `"{[]}"`:**
```
c='{': push → stack=['{']
c='[': push → stack=['{','[']
c=']': pop='['. Does ']' match '['? YES → stack=['{']
c='}': pop='{'. Does '}' match '{'? YES → stack=[]
return stack.isEmpty() = true ✅
```

**Cleaner with a Map:**
```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        Map<Character, Character> map = Map.of(')', '(', ']', '[', '}', '{');

        for (char c : s.toCharArray()) {
            if (!map.containsKey(c)) {
                stack.push(c);  // it's an opener
            } else {
                // It's a closer — check match
                if (stack.isEmpty() || stack.pop() != map.get(c)) return false;
            }
        }
        return stack.isEmpty();
    }
}
```

---

## PATTERN 2 — Stack as Design

### When to Use
- Design a data structure with O(1) operations
- Evaluate mathematical expressions (postfix / RPN)
- Keywords: "min stack", "evaluate expression", "design stack"

---

### Example A — Min Stack

**Problem:** Design a stack that supports `push`, `pop`, `top`, and `getMin` all in O(1).

**Key Insight:**
```
Storing just values isn't enough — if we pop the current min, we need
to KNOW the previous min instantly.

Approach: store PAIRS (value, currentMin) — each entry remembers
what the minimum was at the time it was pushed.

stack: [(val1, min1), (val2, min2), ...]
When we push x:  newMin = min(x, stack.top().min)
                 push (x, newMin)
When we pop:     just pop the pair
getMin:          return stack.top().min
```

```
Example: push 5, push 3, push 7, push 2, pop, getMin

Push 5: min=5.  stack=[(5,5)]
Push 3: min=min(3,5)=3. stack=[(5,5),(3,3)]
Push 7: min=min(7,3)=3. stack=[(5,5),(3,3),(7,3)]
Push 2: min=min(2,3)=2. stack=[(5,5),(3,3),(7,3),(2,2)]
Pop:    remove (2,2). stack=[(5,5),(3,3),(7,3)]
getMin: top.min = 3 ✅ (correctly recovered min after popping 2)
```

```java
class MinStack {
    // Stack of [value, currentMin] pairs
    private Deque<int[]> stack = new ArrayDeque<>();

    public void push(int val) {
        int currentMin = stack.isEmpty() ? val : Math.min(val, stack.peek()[1]);
        stack.push(new int[]{val, currentMin});
    }

    public void pop() {
        stack.pop();
    }

    public int top() {
        return stack.peek()[0];  // the value
    }

    public int getMin() {
        return stack.peek()[1];  // the stored minimum
    }
}
// Time: O(1) for all operations  |  Space: O(n)
```

---

### Example B — Evaluate Reverse Polish Notation (RPN)

**Problem:** Evaluate an expression in postfix notation.

```
Postfix / RPN: operators come AFTER their operands.
["2","1","+","3","*"] = (2 + 1) * 3 = 9

Normal infix: (2 + 1) * 3
Postfix RPN:   2 1 + 3 *
```

**Why postfix? No parentheses needed!** Order of evaluation is unambiguous.

**Strategy:**
```
Scan tokens left to right:
  Number → PUSH onto stack
  Operator (+, -, *, /) → POP two operands, COMPUTE, PUSH result

At the end, stack has exactly ONE element = the answer.
```

```
["2","1","+","3","*"]:
token="2": push 2. stack=[2]
token="1": push 1. stack=[2,1]
token="+": pop 1 and 2. compute 2+1=3. push 3. stack=[3]
token="3": push 3. stack=[3,3]
token="*": pop 3 and 3. compute 3*3=9. push 9. stack=[9]
Return stack.pop() = 9 ✅
```

```java
class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();

        for (String token : tokens) {
            if (isOperator(token)) {
                int b = stack.pop();  // SECOND operand (popped first!)
                int a = stack.pop();  // FIRST operand

                switch (token) {
                    case "+": stack.push(a + b); break;
                    case "-": stack.push(a - b); break;
                    case "*": stack.push(a * b); break;
                    case "/": stack.push(a / b); break;  // integer division
                }
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        return stack.pop();
    }

    private boolean isOperator(String s) {
        return s.equals("+") || s.equals("-") || s.equals("*") || s.equals("/");
    }
}
// Time: O(n)  |  Space: O(n)
```

> ⚠️ **Order matters!** `a = stack.pop()` is wrong for subtraction/division.
> Pop b FIRST (top = second operand), then pop a (first operand).
> `a - b` not `b - a`. For `["6","2","-"]`: a=6, b=2 → 6-2=4, not 2-6=-4.

---

### Bonus — Prefix, Infix, Postfix Conversions (Striver L3)

```
Infix:   A + B * C       (normal human notation, needs parentheses for order)
Prefix:  + A * B C       (operator BEFORE operands, Polish notation)
Postfix: A B C * +       (operator AFTER operands, Reverse Polish notation)

Key insight for infix → postfix:
  Use operator PRECEDENCE and a stack.
  * and / have higher precedence than + and -.

Infix → Postfix algorithm:
  For each token:
    OPERAND (letter/digit) → output directly
    '(' → push to stack
    ')' → pop and output until '(' is found
    OPERATOR → pop and output all operators with >= precedence, then push

Postfix evaluation = Pattern 2B above (RPN).
```

---

## PATTERN 3 — Monotonic Stack: NGE / PSE

### When to Use
- Find the **Next Greater Element** for each element (to the right)
- Find the **Previous Greater Element** for each element (to the left)
- Find the **Next Smaller Element** (NSE) / **Previous Smaller Element** (PSE)
- Stock span problem, daily temperatures, circular array versions
- Keywords: "next greater", "next warmer", "span", "previous smaller"

### The Four Variants

```
NGE (Next Greater Element to the RIGHT):
  Use DECREASING monotonic stack (pop when current > top).
  When an element is popped, current = its NGE.

PGE (Previous Greater Element to the LEFT):
  Process left to right, use DECREASING stack.
  Top of stack when pushing = PGE of current element.

NSE (Next Smaller Element to the RIGHT):
  Use INCREASING monotonic stack (pop when current < top).
  When an element is popped, current = its NSE.

PSE (Previous Smaller Element to the LEFT):
  Process left to right, use INCREASING stack.
  Top of stack when pushing = PSE of current element.
```

### 🔑 PATTERN 3 TEMPLATE — NGE (Next Greater Element)

```java
int[] nextGreater(int[] arr) {
    int n = arr.length;
    int[] nge = new int[n];
    Arrays.fill(nge, -1);              // default: -1 if no NGE exists

    Deque<Integer> stack = new ArrayDeque<>();  // stores INDICES

    for (int i = 0; i < n; i++) {
        // Pop all elements smaller than arr[i]
        // → arr[i] is their Next Greater Element
        while (!stack.isEmpty() && arr[stack.peek()] < arr[i]) {
            nge[stack.pop()] = arr[i];  // found NGE!
        }
        stack.push(i);  // push current index
    }
    return nge;
    // Remaining elements in stack have no NGE → already -1
}
```

**Trace on [4, 1, 2, 3]:**
```
i=0(4): stack=[]→push 0. stack=[0]
i=1(1): arr[0]=4 < 1? NO → push 1. stack=[0,1]
i=2(2): arr[1]=1 < 2? YES → nge[1]=2, pop. arr[0]=4 < 2? NO → push 2. stack=[0,2]
i=3(3): arr[2]=2 < 3? YES → nge[2]=3, pop. arr[0]=4 < 3? NO → push 3. stack=[0,3]

Remaining in stack: [0,3] → no NGE → nge[0]=-1, nge[3]=-1

nge = [-1, 2, 3, -1] ✅
```

---

### Example A — Daily Temperatures

**Problem:** Given temperatures, return how many days until a warmer temperature. 0 if no warmer day exists.

```
temps = [73, 74, 75, 71, 69, 72, 76, 73]
answer= [ 1,  1,  4,  2,  1,  1,  0,  0]
         ↑ day 0 (73): 1 day later (74 is warmer)
                   ↑ day 2 (75): 4 days later (76 is warmer)
```

**This is exactly NGE — but instead of the greater value, we need the DISTANCE (index difference).**

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] answer = new int[n];  // default 0 (no warmer day)
        Deque<Integer> stack = new ArrayDeque<>();  // stores INDICES

        for (int i = 0; i < n; i++) {
            // Pop indices whose temperature is LESS than today's
            while (!stack.isEmpty() && temperatures[stack.peek()] < temperatures[i]) {
                int prevIdx = stack.pop();
                answer[prevIdx] = i - prevIdx;  // days to wait = distance
            }
            stack.push(i);
        }
        return answer;
    }
}
// Time: O(n)  |  Space: O(n)
```

**Trace for [73, 74, 75, 71, 69, 72, 76, 73]:**
```
i=0(73): stack empty. push 0. stack=[0]
i=1(74): temp[0]=73 < 74 → answer[0]=1-0=1, pop. push 1. stack=[1]
i=2(75): temp[1]=74 < 75 → answer[1]=2-1=1, pop. push 2. stack=[2]
i=3(71): temp[2]=75 < 71? NO → push 3. stack=[2,3]
i=4(69): temp[3]=71 < 69? NO → push 4. stack=[2,3,4]
i=5(72): temp[4]=69 < 72 → answer[4]=5-4=1, pop.
         temp[3]=71 < 72 → answer[3]=5-3=2, pop.
         temp[2]=75 < 72? NO → push 5. stack=[2,5]
i=6(76): temp[5]=72 < 76 → answer[5]=6-5=1, pop.
         temp[2]=75 < 76 → answer[2]=6-2=4, pop. push 6. stack=[6]
i=7(73): temp[6]=76 < 73? NO → push 7. stack=[6,7]

Remaining [6,7]: answer stays 0 (no warmer day after them).

answer = [1,1,4,2,1,1,0,0] ✅
```

---

### Example B — Next Greater Element II (Circular Array, Striver L6)

**Problem:** Given a circular array, find the NGE for each element (can wrap around).

```
nums = [1, 2, 1]
answer = [2, -1, 2]
  nums[0]=1: next greater is 2 (index 1)
  nums[1]=2: no greater element in circle → -1
  nums[2]=1: next greater is 2 (index 1, wrapping around)
```

**Trick: Process the array TWICE (simulate circular by iterating 2n):**

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);
        Deque<Integer> stack = new ArrayDeque<>();  // stores indices

        // Iterate 2n to simulate circular array
        for (int i = 0; i < 2 * n; i++) {
            int idx = i % n;  // wrap around using modulo

            while (!stack.isEmpty() && nums[stack.peek()] < nums[idx]) {
                result[stack.pop()] = nums[idx];
            }
            if (i < n) stack.push(idx);  // only push in first pass
        }
        return result;
    }
}
// Time: O(n)  |  Space: O(n)
```

---

### Example C — Stock Span Problem (Striver L15)

**Problem:** For each day's stock price, find the span — the number of consecutive days BEFORE it (including itself) where the price was ≤ today's price.

```
prices = [100, 80, 60, 70, 60, 75, 85]
spans  = [  1,  1,  1,  2,  1,  4,  6]
  Day 5 (75): spans 4 days (75, 60, 70, 60 — all ≤ 75 going backwards)
  Day 6 (85): spans 6 days (85 ≤ 100? no, but 85 > all previous → spans all 6)
```

**This is PGE (Previous Greater Element) — span = current_index - PGE_index.**

```java
int[] stockSpan(int[] prices) {
    int n = prices.length;
    int[] span = new int[n];
    Deque<Integer> stack = new ArrayDeque<>();  // stores indices (decreasing prices)

    for (int i = 0; i < n; i++) {
        // Pop all indices with price ≤ current
        while (!stack.isEmpty() && prices[stack.peek()] <= prices[i]) {
            stack.pop();
        }

        // PGE index is the new top (or -1 if stack empty)
        int pge = stack.isEmpty() ? -1 : stack.peek();
        span[i] = i - pge;   // span = distance to previous greater element

        stack.push(i);
    }
    return span;
}
// Time: O(n)  |  Space: O(n)
```

**Trace for [100, 80, 60, 70, 60, 75, 85]:**
```
i=0(100): stack empty. pge=-1. span[0]=0-(-1)=1. push 0. stack=[0]
i=1(80):  100>80, stop. pge=0. span[1]=1-0=1. push 1. stack=[0,1]
i=2(60):  80>60, stop.  pge=1. span[2]=2-1=1. push 2. stack=[0,1,2]
i=3(70):  60≤70→pop. 80>70, stop. pge=1. span[3]=3-1=2. push 3. stack=[0,1,3]
i=4(60):  70>60, stop. pge=3. span[4]=4-3=1. push 4. stack=[0,1,3,4]
i=5(75):  60≤75→pop. 70≤75→pop. 80>75, stop. pge=1. span[5]=5-1=4. push 5.
i=6(85):  75≤85→pop. 80≤85→pop. 100>85, stop. pge=0. span[6]=6-0=6. push 6.

spans = [1,1,1,2,1,4,6] ✅
```

---

### Summary Table: Which Stack to Use

```
┌─────────────────────────────────┬───────────────────────────────────────┐
│ Problem                         │ Stack Type & Key Operation            │
├─────────────────────────────────┼───────────────────────────────────────┤
│ NGE (Next Greater to Right)     │ DECREASING. Pop when arr[top] < curr  │
│                                 │ popped element's NGE = curr           │
├─────────────────────────────────┼───────────────────────────────────────┤
│ PGE (Previous Greater to Left)  │ DECREASING. Don't pop.                │
│                                 │ top of stack BEFORE push = PGE        │
├─────────────────────────────────┼───────────────────────────────────────┤
│ NSE (Next Smaller to Right)     │ INCREASING. Pop when arr[top] > curr  │
│                                 │ popped element's NSE = curr           │
├─────────────────────────────────┼───────────────────────────────────────┤
│ PSE (Previous Smaller to Left)  │ INCREASING. Don't pop.                │
│                                 │ top of stack BEFORE push = PSE        │
└─────────────────────────────────┴───────────────────────────────────────┘
```

---

## PATTERN 4 — Monotonic Stack: Area / Contribution

### When to Use
- Find the largest rectangle in a histogram
- Problems where each element's "contribution" depends on its nearest smaller/greater boundary
- Keywords: "largest rectangle", "maximal rectangle", "sum of subarray minimums"

### The Core Idea for Histogram
```
For each bar in the histogram:
  The largest rectangle using this bar as the HEIGHT extends:
  LEFT: until a bar SHORTER than this bar is found (Previous Smaller Element)
  RIGHT: until a bar SHORTER than this bar is found (Next Smaller Element)

  Width = (NSE_index - PSE_index - 1)
  Area  = height × width

Find MAX area across all bars.

The monotonic INCREASING stack gives us both PSE and NSE in ONE pass!
```

---

### Example A — Largest Rectangle in Histogram ⭐ (Hard)

**Problem:** Find the largest rectangle that can be formed in a histogram.

```
heights = [2, 1, 5, 6, 2, 3]

     6
   5 █
   █ █
 2 █ █ 2 3
 █ 1 █ █ █ █
 ─────────────
   Largest: 10 (bars of height 5 and 6, width=2)
```

**One-Pass Approach (Striver's Optimal):**
```
Process bars left to right using an increasing monotonic stack.

Key insight: when we pop a bar (because current bar is SHORTER):
  - Popped bar's height = rectangle height
  - Right boundary = current index (first bar shorter to the right)
  - Left boundary = new stack top (first bar shorter to the left)
  - Width = right - left - 1

After the loop: remaining bars in stack have no shorter bar to the right (NSE = n).
```

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        Deque<Integer> stack = new ArrayDeque<>();  // stores indices (increasing heights)
        int maxArea = 0;

        for (int i = 0; i <= n; i++) {
            // Use height 0 as a sentinel at position n to flush remaining stack
            int currentHeight = (i == n) ? 0 : heights[i];

            // Pop when current is SHORTER than top → calculates rectangle for top
            while (!stack.isEmpty() && heights[stack.peek()] >= currentHeight) {
                int height = heights[stack.pop()];           // rectangle height
                int rightBound = i;                          // NSE index
                int leftBound = stack.isEmpty() ? -1 : stack.peek();  // PSE index
                int width = rightBound - leftBound - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }
        return maxArea;
    }
}
// Time: O(n) — each index pushed and popped at most once
// Space: O(n)
```

**Detailed trace for [2, 1, 5, 6, 2, 3]:**
```
i=0(h=2): stack empty. push 0. stack=[0]
i=1(h=1): h[0]=2 ≥ 1 → POP 0.
  height=2, rightBound=1, leftBound=-1 (stack empty). width=1-(-1)-1=1. area=2×1=2.
  stack empty. push 1. stack=[1]
i=2(h=5): h[1]=1 < 5, no pop. push 2. stack=[1,2]
i=3(h=6): h[2]=5 < 6, no pop. push 3. stack=[1,2,3]
i=4(h=2): h[3]=6 ≥ 2 → POP 3.
  height=6, rightBound=4, leftBound=2. width=4-2-1=1. area=6×1=6.
  h[2]=5 ≥ 2 → POP 2.
  height=5, rightBound=4, leftBound=1. width=4-1-1=2. area=5×2=10. ← MAX!
  h[1]=1 < 2, stop. push 4. stack=[1,4]
i=5(h=3): h[4]=2 < 3. push 5. stack=[1,4,5]
i=6(sentinel h=0):
  h[5]=3 ≥ 0 → POP 5. height=3, rightBound=6, leftBound=4. width=6-4-1=1. area=3.
  h[4]=2 ≥ 0 → POP 4. height=2, rightBound=6, leftBound=1. width=6-1-1=4. area=8.
  h[1]=1 ≥ 0 → POP 1. height=1, rightBound=6, leftBound=-1. width=6-(-1)-1=6. area=6.
  stack empty.

maxArea = 10 ✅
```

**Visual of the rectangle found:**
```
heights = [2, 1, 5, 6, 2, 3]
index:      0  1  2  3  4  5

When popping index 2 (height=5) at i=4:
  leftBound = 1 (PSE, value=1)
  rightBound = 4 (NSE, value=2)
  width = 4 - 1 - 1 = 2  (bars at indices 2 and 3)
  area = 5 × 2 = 10

    [5][6]
    [5][6]
    [5][6]
  ← width=2 →
```

---

### Example B — Sum of Subarray Minimums (Striver L9)

**Problem:** Find the sum of the minimums of all subarrays of an array.

```
arr = [3, 1, 2, 4]
Subarrays and their mins:
[3]=3, [1]=1, [2]=2, [4]=4
[3,1]=1, [1,2]=1, [2,4]=2
[3,1,2]=1, [1,2,4]=1
[3,1,2,4]=1
Sum = 3+1+2+4+1+1+2+1+1+1 = 17
```

**Key Insight — Contribution Technique:**
```
For each element arr[i], count how many subarrays have arr[i] as their MINIMUM.
  left[i]  = number of elements to the left where arr[i] is still the minimum
             = distance to Previous Smaller Element (PSE)
  right[i] = number of elements to the right where arr[i] is still the minimum
             = distance to Next Smaller or Equal Element (NSEE)

  contribution[i] = arr[i] × left[i] × right[i]

Note: use "strictly smaller" for PSE and "smaller or equal" for NSEE
      to avoid double-counting equal elements.
```

```java
class Solution {
    public int sumSubarrayMins(int[] arr) {
        int n = arr.length;
        int MOD = 1_000_000_007;

        int[] left  = new int[n];  // distance to PSE (strictly smaller)
        int[] right = new int[n];  // distance to NSEE (smaller or equal)

        // Compute left[i]: count of consecutive elements ≥ arr[i] to the left + 1
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) stack.pop();
            left[i] = stack.isEmpty() ? i + 1 : i - stack.peek();
            stack.push(i);
        }

        // Compute right[i]: count of consecutive elements > arr[i] to the right + 1
        stack.clear();
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) stack.pop();
            right[i] = stack.isEmpty() ? n - i : stack.peek() - i;
            stack.push(i);
        }

        // Sum contributions
        long result = 0;
        for (int i = 0; i < n; i++) {
            result = (result + (long) arr[i] * left[i] * right[i]) % MOD;
        }
        return (int) result;
    }
}
// Time: O(n)  |  Space: O(n)
```

---

## PATTERN 5 — Stack-Based Simulation / Greedy

### When to Use
- Simulate events that can cancel, collide, or block each other
- Build a structure greedily (remove "bad" elements as we go)
- Keywords: "asteroids", "collision", "remove digits", "car fleet"
- The stack holds "active" elements that haven't been resolved yet

---

### Example A — Car Fleet

**Problem:** N cars heading to target. A faster car behind a slower car merges into a fleet. Return number of fleets.

```
target=12, positions=[10,8,0,5,3], speeds=[2,4,1,1,3]

Car times to reach target:
  pos=10, speed=2: (12-10)/2 = 1.0
  pos=8,  speed=4: (12-8)/4  = 1.0
  pos=5,  speed=1: (12-5)/1  = 7.0
  pos=3,  speed=3: (12-3)/3  = 3.0
  pos=0,  speed=1: (12-0)/1  = 12.0

Sort by position DESCENDING (closest to target first):
  pos=10: time=1.0
  pos=8:  time=1.0
  pos=5:  time=7.0
  pos=3:  time=3.0
  pos=0:  time=12.0

Fleet logic: a car MERGES if its time ≤ the car AHEAD (it catches up).
             If its time > car ahead, it forms a new fleet.

Stack of fleet times (top = leading fleet):
  push 1.0: [1.0]
  1.0 ≤ 1.0 → merges with top fleet (same time = same fleet). [1.0]
  7.0 > 1.0 → new fleet. [1.0, 7.0]
  3.0 ≤ 7.0 → merges. [1.0, 7.0]
  12.0 > 7.0 → new fleet. [1.0, 7.0, 12.0]

Answer = stack.size() = 3 fleets ✅
```

```java
class Solution {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;

        // Pair up position and speed, sort by position DESCENDING
        int[][] cars = new int[n][2];
        for (int i = 0; i < n; i++) cars[i] = new int[]{position[i], speed[i]};
        Arrays.sort(cars, (a, b) -> b[0] - a[0]);  // sort descending by position

        Deque<Double> stack = new ArrayDeque<>();    // stack of fleet arrival times

        for (int[] car : cars) {
            double time = (double)(target - car[0]) / car[1];

            // Only form a new fleet if this car takes LONGER than the fleet ahead
            // (if it's faster/equal, it catches up and merges)
            if (stack.isEmpty() || time > stack.peek()) {
                stack.push(time);
            }
            // else: time <= top → this car merges into the leading fleet, don't push
        }

        return stack.size();
    }
}
// Time: O(n log n) — sorting dominates
// Space: O(n)
```

---

### Example B — Asteroid Collision (Striver L11)

**Problem:** Array of asteroids (positive = moving right, negative = moving left). They collide when a right-moving meets a left-moving. Larger destroys smaller; equal destroys both. Return surviving asteroids.

```
[5, 10, -5]  → [5, 10]  (10 > 5, so -5 is destroyed)
[8, -8]      → []       (equal, both destroyed)
[10, 2, -5]  → [10]     (-5 collides 2: 2 destroyed. -5 collides 10: -5 destroyed)
[-2, -1, 1, 2] → [-2,-1,1,2] (left-movers never meet right-movers behind them)
```

**Collision only happens when:**
```
TOP of stack is POSITIVE (moving right) AND current asteroid is NEGATIVE (moving left).
→ They are heading TOWARD each other.

No collision when:
  Both positive (same direction →)
  Both negative (same direction ←)
  Top is negative, current is positive (moving APART)
```

```java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        Deque<Integer> stack = new ArrayDeque<>();

        for (int ast : asteroids) {
            boolean survived = true;

            // Collision: stack top is positive, current is negative
            while (survived && !stack.isEmpty() && stack.peek() > 0 && ast < 0) {
                int top = stack.peek();

                if (top < -ast) {
                    // Stack top is SMALLER → it's destroyed, current survives
                    stack.pop();
                    // continue while loop to check next collision
                } else if (top == -ast) {
                    // Equal size → BOTH destroyed
                    stack.pop();
                    survived = false;  // current asteroid is also destroyed
                } else {
                    // top > -ast → current asteroid is SMALLER, destroyed
                    survived = false;
                }
            }

            if (survived) stack.push(ast);
        }

        // Build result from stack (stack is in order bottom=first, top=last)
        int[] result = new int[stack.size()];
        for (int i = result.length - 1; i >= 0; i--) {
            result[i] = stack.pop();
        }
        return result;
    }
}
// Time: O(n)  |  Space: O(n)
```

**Trace for [10, 2, -5]:**
```
ast=10: stack empty, no collision. push 10. stack=[10]
ast=2:  top=10>0, ast=2>0 → no collision (both right). push 2. stack=[10,2]
ast=-5: top=2>0, ast=-5<0 → COLLISION!
  top(2) < -ast(5) → 2 destroyed. pop. stack=[10]
  top=10>0, ast=-5<0 → COLLISION!
  top(10) > -ast(5) → -5 destroyed. survived=false. stop while.
  survived=false → don't push -5.

Result: [10] ✅
```

---

### Example C — Remove K Digits (Striver L14)

**Problem:** Remove k digits from a number string to make it the SMALLEST possible number.

```
num = "1432219", k = 3
Answer: "1219"   (remove 4, 3, 2 in that order)
```

**Key Insight — Greedy with Stack:**
```
To get the smallest number, we want SMALLER digits as early (leftmost) as possible.
When we see a digit SMALLER than the previous → removing the previous digit
makes the number smaller (fewer "large" digits in high-significance positions).

Use a stack: keep digits in NON-DECREASING order.
When current digit < top: POP the top (remove it), decrement k.

After the loop: if k > 0, remove from the END (the largest trailing digits).

Edge case: "0010" → strip leading zeros from result.
```

```java
class Solution {
    public String removeKdigits(String num, int k) {
        Deque<Character> stack = new ArrayDeque<>();

        for (char digit : num.toCharArray()) {
            // While stack not empty, k remains, and top > current: pop (remove larger)
            while (!stack.isEmpty() && k > 0 && stack.peek() > digit) {
                stack.pop();
                k--;
            }
            stack.push(digit);
        }

        // If k > 0, remove from end (stack top = least significant digits)
        while (k > 0) {
            stack.pop();
            k--;
        }

        // Build result (stack bottom is first digit, top is last)
        StringBuilder sb = new StringBuilder();
        for (char c : stack) sb.append(c);  // ArrayDeque iterates bottom to top? No...

        // Actually ArrayDeque.push adds to front, so iterate in reverse
        // Better: collect in array
        char[] arr = new char[stack.size()];
        for (int i = arr.length - 1; i >= 0; i--) arr[i] = stack.pop();
        String result = new String(arr);

        // Remove leading zeros
        int start = 0;
        while (start < result.length() - 1 && result.charAt(start) == '0') start++;
        return result.substring(start);
    }
}
// Time: O(n)  |  Space: O(n)
```

**Trace for "1432219", k=3:**
```
digit='1': stack empty. push. stack=['1']
digit='4': 4>1? no pop (4>1 means DON'T pop). push. stack=['1','4']
           Wait: we pop when top > current. top='4', current='3'? let's continue.
digit='4': '1' < '4', no pop. push '4'. stack=['1','4']
digit='3': top='4' > '3' → pop '4', k=2. top='1' < '3', stop. push '3'. stack=['1','3']
digit='2': top='3' > '2' → pop '3', k=1. top='1' < '2', stop. push '2'. stack=['1','2']
digit='2': top='2' ≤ '2', no pop. push '2'. stack=['1','2','2']
digit='1': top='2' > '1' → pop '2', k=0. k=0, stop. push '1'. stack=['1','2','1']
digit='9': k=0, no pop. push '9'. stack=['1','2','1','9']

k=0, no trimming.
Result: "1219" ✅
```

---

## How to Identify the Pattern

```
READ the problem. Ask these questions in order:

START
  │
  ▼
Does the problem involve DESIGNING a stack-based data structure
OR EVALUATING an arithmetic expression (RPN / postfix)?
  ├── YES → PATTERN 2 (Stack as Design)
  │         Min Stack: store (value, currentMin) pairs
  │         RPN: number→push, operator→pop two, compute, push result
  └── NO ↓

Does the problem involve checking if brackets/symbols are BALANCED/MATCHED?
  ├── YES → PATTERN 1 (Bracket Matching)
  │         Opening → push. Closing → pop and verify match. End → stack empty?
  └── NO ↓

Does the problem involve finding LARGEST RECTANGLE, AREA, or
CONTRIBUTION of each element based on its nearest smaller boundary?
  ├── YES → PATTERN 4 (Monotonic Stack: Area)
  │         Increasing stack → pop when shorter bar found
  │         Width = NSE_index - PSE_index - 1
  │         Use sentinel 0 at end to flush remaining stack
  └── NO ↓

Does the problem ask for the NEXT or PREVIOUS GREATER or SMALLER element?
OR find waiting time / span / similar "looking outward" from each element?
  ├── YES → PATTERN 3 (Monotonic Stack: NGE/PSE)
  │         NGE/NSE: pop on violation → popped element's answer = current
  │         PGE/PSE: top of stack before pushing = left-side answer
  │         Circular array: iterate 2n, use i % n
  └── NO ↓

Default → PATTERN 5 (Stack-Based Simulation / Greedy)
          Asteroids: collide when top>0 and current<0
          Car Fleet: sort by position desc, push time if > stack top
          Remove K Digits: pop when top > current and k > 0
```

---

## Problem → Pattern Mapping

| Problem | Pattern | Key Trick |
|---------|---------|-----------|
| Valid Parentheses | Pattern 1 | Opening→push, Closing→pop+verify, end→empty? |
| Min Stack | Pattern 2 | Store (value, currentMin) pairs |
| Evaluate RPN | Pattern 2 | Number→push, Operator→pop two, compute, push |
| Daily Temperatures | Pattern 3 | NGE with indices, answer = distance to NGE |
| Car Fleet | Pattern 5 | Sort by pos desc, push time if > top fleet |
| Largest Rectangle in Histogram | Pattern 4 | Monotonic increasing stack, sentinel 0 at end |
| Next Greater Element I / II | Pattern 3 | NGE; circular: iterate 2n |
| Previous Smaller Element | Pattern 3 | PSE: increasing stack, top before push = PSE |
| Stock Span | Pattern 3 | PGE: span = i - PGE_index |
| Asteroid Collision | Pattern 5 | Collision when top>0 and curr<0 |
| Remove K Digits | Pattern 5 | Pop top when top>curr and k>0 |
| Sum of Subarray Minimums | Pattern 4 | Contribution = arr[i] × left[i] × right[i] |

---

## Complexity Cheat Sheet

| Problem | Time | Space | Notes |
|---------|------|-------|-------|
| Valid Parentheses | O(n) | O(n) | Each char processed once |
| Min Stack | O(1) per op | O(n) | Store min alongside each value |
| Evaluate RPN | O(n) | O(n) | One pass through tokens |
| Daily Temperatures | O(n) | O(n) | Monotonic decreasing stack |
| Car Fleet | O(n log n) | O(n) | Sort dominates |
| Largest Rectangle | O(n) | O(n) | Each bar pushed/popped once |
| NGE / PSE | O(n) | O(n) | Each element pushed/popped once |
| Stock Span | O(n) | O(n) | PGE with index distance |
| Asteroid Collision | O(n) | O(n) | Each asteroid processed once |
| Remove K Digits | O(n) | O(n) | Each digit processed once |
| Sum of Subarray Mins | O(n) | O(n) | Two passes for left/right |

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Valid Parentheses — checking isEmpty AFTER pop
```java
// WRONG — pop() on empty stack throws exception!
if (stack.pop() != map.get(c)) return false;  // might throw if stack is empty

// CORRECT — check empty FIRST
if (stack.isEmpty() || stack.pop() != map.get(c)) return false;
```

### ❌ Mistake 2: RPN — wrong operand order for subtraction/division
```java
// WRONG — order matters for - and /!
int a = stack.pop();  // a is actually the SECOND operand (more recent)
int b = stack.pop();  // b is the FIRST operand
stack.push(a - b);    // should be b - a!

// CORRECT
int b = stack.pop();  // second operand (pop first)
int a = stack.pop();  // first operand (pop second)
stack.push(a - b);    // a - b is correct for "a b -"
```

### ❌ Mistake 3: Histogram — not adding sentinel 0 at the end
```java
// WRONG — bars remaining in stack after loop never get processed
for (int i = 0; i < n; i++) { ... }
// Bars [1,4,5] still in stack, their areas not computed!

// CORRECT — iterate to n (inclusive) with sentinel height 0
for (int i = 0; i <= n; i++) {
    int h = (i == n) ? 0 : heights[i];  // sentinel 0 flushes remaining
    // ...
}
```

### ❌ Mistake 4: Car Fleet — sorting in wrong order
```java
// WRONG — ascending position means you process farthest car last
Arrays.sort(cars, (a, b) -> a[0] - b[0]);  // ascending

// CORRECT — descending (closest to target first)
Arrays.sort(cars, (a, b) -> b[0] - a[0]);  // descending
// Process closest car first → stack represents fleets from front to back
```

### ❌ Mistake 5: NGE/Daily Temperatures — storing values instead of indices
```java
// WRONG — if you store values, you can't compute distance (for Daily Temps)
while (!stack.isEmpty() && stack.peek() < temp[i]) {
    int prevTemp = stack.pop();  // just the temp, not the index!
    // Can't compute answer[???] = i - ???;
}

// CORRECT — always store INDICES in the stack (get value via arr[index])
while (!stack.isEmpty() && temps[stack.peek()] < temps[i]) {
    int prevIdx = stack.pop();
    answer[prevIdx] = i - prevIdx;  // distance calculation needs index
}
```

### ❌ Mistake 6: Min Stack — updating min incorrectly on pop
```java
// WRONG — using a separate minStack that doesn't know when to update
Stack<Integer> main = new Stack<>();
Stack<Integer> mins = new Stack<>();
void push(int x) {
    main.push(x);
    if (x <= mins.peek()) mins.push(x);  // only push if new min
}
void pop() {
    int val = main.pop();
    if (val == mins.peek()) mins.pop();  // what if equal values?
}
// Fragile — edge cases with equal values

// CORRECT — store (value, currentMin) pairs: no separate stack needed
stack.push(new int[]{val, Math.min(val, stack.isEmpty() ? val : stack.peek()[1])});
```

### ❌ Mistake 7: Remove K Digits — forgetting leading zeros
```java
// WRONG — "0100" with result starting with 0 is not a valid number representation
return new String(arr);  // might return "0219" for some inputs

// CORRECT — strip leading zeros (but keep at least one digit)
int start = 0;
while (start < result.length() - 1 && result.charAt(start) == '0') start++;
return result.substring(start);
```

---

## 📋 Quick Reference Card

```
┌──────────────────────────────────────────────────────────────────────┐
│                     STACK PATTERNS QUICK CARD                        │
├────────────────────┬─────────────────────────────────────────────────┤
│ CORE RULE          │ Use ArrayDeque, NOT Stack class                  │
│                    │ Always check isEmpty() before peek()/pop()      │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 1          │ Opening bracket → push                          │
│ Bracket Matching   │ Closing bracket → pop + verify match            │
│                    │ End of string → stack must be empty             │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 2          │ Min Stack: push (val, min(val, top.min))        │
│ Design/Eval        │ RPN: pop b first, then a. Compute a OP b.       │
│                    │ Postfix order: b=second, a=first operand        │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 3          │ NGE → decreasing stack, pop when top < curr     │
│ Monotonic NGE/PSE  │ PGE → decreasing stack, top before push = PGE  │
│                    │ NSE → increasing stack, pop when top > curr     │
│                    │ PSE → increasing stack, top before push = PSE   │
│                    │ Always store INDICES, not values                │
│                    │ Circular → iterate 2n, use i % n               │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 4          │ Histogram: increasing stack + sentinel 0 at end │
│ Monotonic Area     │ On pop: height=arr[top], width=NSE-PSE-1        │
│                    │ Contribution: arr[i] × left[i] × right[i]      │
├────────────────────┼─────────────────────────────────────────────────┤
│ PATTERN 5          │ Asteroid: collide when top>0 && curr<0         │
│ Simulation/Greedy  │ Car Fleet: sort pos desc, push if time > top   │
│                    │ Remove K: pop when top>curr and k>0            │
│                    │           strip leading zeros at end            │
└────────────────────┴─────────────────────────────────────────────────┘
```

---

## 🔁 Core Building Blocks

```java
// ── 1. BRACKET MATCHING ───────────────────────────────────────────
Deque<Character> stack = new ArrayDeque<>();
Map<Character, Character> match = Map.of(')', '(', ']', '[', '}', '{');
for (char c : s.toCharArray()) {
    if (!match.containsKey(c)) stack.push(c);
    else if (stack.isEmpty() || stack.pop() != match.get(c)) return false;
}
return stack.isEmpty();

// ── 2. RPN EVALUATION ─────────────────────────────────────────────
Deque<Integer> stk = new ArrayDeque<>();
for (String t : tokens) {
    if ("+-*/".contains(t)) {
        int b = stk.pop(), a = stk.pop();
        stk.push(t.equals("+") ? a+b : t.equals("-") ? a-b :
                 t.equals("*") ? a*b : a/b);
    } else stk.push(Integer.parseInt(t));
}
return stk.pop();

// ── 3. NGE (NEXT GREATER ELEMENT) ─────────────────────────────────
int[] nge = new int[n]; Arrays.fill(nge, -1);
Deque<Integer> stk = new ArrayDeque<>(); // indices
for (int i = 0; i < n; i++) {
    while (!stk.isEmpty() && arr[stk.peek()] < arr[i]) nge[stk.pop()] = arr[i];
    stk.push(i);
}

// ── 4. LARGEST RECTANGLE IN HISTOGRAM ────────────────────────────
Deque<Integer> stk = new ArrayDeque<>();
int max = 0;
for (int i = 0; i <= n; i++) {
    int h = (i == n) ? 0 : heights[i];
    while (!stk.isEmpty() && heights[stk.peek()] >= h) {
        int ht = heights[stk.pop()];
        int w  = stk.isEmpty() ? i : i - stk.peek() - 1;
        max = Math.max(max, ht * w);
    }
    stk.push(i);
}

// ── 5. ASTEROID COLLISION ─────────────────────────────────────────
Deque<Integer> stk = new ArrayDeque<>();
for (int a : asteroids) {
    boolean alive = true;
    while (alive && !stk.isEmpty() && stk.peek() > 0 && a < 0) {
        if      (stk.peek() < -a)  stk.pop();
        else if (stk.peek() == -a) { stk.pop(); alive = false; }
        else                         alive = false;
    }
    if (alive) stk.push(a);
}

// ── 6. REMOVE K DIGITS ────────────────────────────────────────────
Deque<Character> stk = new ArrayDeque<>();
for (char d : num.toCharArray()) {
    while (!stk.isEmpty() && k > 0 && stk.peek() > d) { stk.pop(); k--; }
    stk.push(d);
}
while (k-- > 0) stk.pop();
// rebuild string + strip leading zeros
```

---

*Notes Version 1.0 — Covers all 6 NeetCode Stack problems + Striver's Stack & Queue series (L1–L15, L17) using 5 patterns.*
*Revise order: Pattern 1 (Bracket) → Pattern 2 (Design/RPN) → Pattern 3 (Monotonic NGE) → Pattern 4 (Histogram) → Pattern 5 (Simulation)*
