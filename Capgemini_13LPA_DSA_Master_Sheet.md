# Capgemini Exceller 2026--27 --- 13 LPA DSA Master Sheet (C++)

**Technical assessment:** 18 November 2026\
**Technical interview:** 19 November 2026\
**Language:** C++\
**Preparation window:** 13--17 November 2026

A focused revision checklist based on the uploaded Capgemini DSA sheet.
The original problem selection is retained, with additional high-yield
coverage for heaps, queues, recursion/backtracking, graphs and DP.

> **Priority guide:** P0 = highest priority; P1 = important; P2 =
> additional coverage.\
> The source sheet labels some questions as Capgemini PYQs; those labels
> are retained as supplied, but the questions have not been
> independently verified as official previous-year questions.

------------------------------------------------------------------------

## How to use this README

-   [ ] Attempt each problem independently before checking a solution.
-   [ ] Explain brute force → bottleneck → optimal approach.
-   [ ] State time and space complexity.
-   [ ] Dry-run at least one normal case and one edge case.
-   [ ] Write clean C++ using STL.
-   [ ] Revise solved patterns instead of mechanically increasing
    problem count.

## 1. Arrays & Hashing --- P0

  -----------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                        Difficulty       Key pattern
  ---------------------- ------------------------------------------------------------------------ ---------------------- ------------------
          \[ \]          [Two Sum](https://leetcode.com/problems/two-sum/)                                 Easy          `unordered_map`,
                                                                                                                         single-pass lookup

          \[ \]          [Group Anagrams](https://leetcode.com/problems/group-anagrams/)                  Medium         Frequency array /
                                                                                                                         sorted key

          \[ \]          [Top K Frequent                                                                  Medium         Bucket sort /
                         Elements](https://leetcode.com/problems/top-k-frequent-elements/)                               min-heap

          \[ \]          [Product of Array Except                                                         Medium         Prefix and suffix
                         Self](https://leetcode.com/problems/product-of-array-except-self/)                              products

          \[ \]          [Longest Consecutive                                                             Medium         `unordered_set`,
                         Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)                          sequence-start
                                                                                                                         check

          \[ \]          [Subarray Sum Equals                                                             Medium         Prefix sum +
                         K](https://leetcode.com/problems/subarray-sum-equals-k/)                                        frequency map

          \[ \]          Move All Zeros to End                                                             Easy          Two pointers,
                                                                                                                         in-place

          \[ \]          Find Equilibrium / Pivot Index                                                    Easy          Total sum minus
                                                                                                                         left prefix

          \[ \]          Count Frequency of Array Elements                                                 Easy          Hash map /
                                                                                                                         in-place counting
  -----------------------------------------------------------------------------------------------------------------------------------------

**C++ revision:** `unordered_map`, `unordered_set`, `map` vs
`unordered_map`, prefix sums, frequency counting, collisions and
average/worst-case complexity.

## 2. Two Pointers & Sliding Window --- P0

  --------------------------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                                            Difficulty       Key pattern
  ---------------------- -------------------------------------------------------------------------------------------- ---------------------- -------------
          \[ \]          [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)                                   Easy          Left/right
                                                                                                                                             pointers

          \[ \]          [3Sum](https://leetcode.com/problems/3sum/)                                                          Medium         Sorting + two
                                                                                                                                             pointers

          \[ \]          [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)                Medium         Greedy two
                                                                                                                                             pointers

          \[ \]          [Best Time to Buy and Sell                                                                            Easy          Running
                         Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)                                              minimum

          \[ \]          [Longest Substring Without Repeating                                                                 Medium         Variable
                         Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)                          sliding
                                                                                                                                             window

          \[ \]          [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)                   Hard          Dynamic
                                                                                                                                             window +
                                                                                                                                             frequency

          \[ \]          Max Sum Subarray of Distinct Elements of Size K                                                      Medium         Fixed
                                                                                                                                             window +
                                                                                                                                             frequency map

          \[ \]          [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)                             Hard          Two pointers
                                                                                                                                             / leftMax and
                                                                                                                                             rightMax
  --------------------------------------------------------------------------------------------------------------------------------------------------------

**C++ revision:** fixed vs variable windows, expanding/shrinking,
last-seen index, duplicate handling and frequency maps inside windows.

## 3. Strings & Matrix --- P1

  ---------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                           Difficulty       Key pattern
  ---------------------- ----------------------------------------------------------- ---------------------- -----------------
          \[ \]          [Rotate Image](https://leetcode.com/problems/rotate-image/)         Medium         Transpose +
                                                                                                            reverse rows

          \[ \]          [Spiral                                                             Medium         Four boundaries
                         Matrix](https://leetcode.com/problems/spiral-matrix/)                              

          \[ \]          [Set Matrix                                                         Medium         In-place markers
                         Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)                          

          \[ \]          Encode and Decode Strings                                           Medium         Length-prefixed
                                                                                                            encoding

          \[ \]          Run-Length String Compression                                        Easy          Two pointers

          \[ \]          Move All `#` Characters to Front                                     Easy          Single pass /
                                                                                                            backward
                                                                                                            overwrite

          \[ \]          Check If One String Is Rotation of Another                           Easy          `s1 + s1`,
                                                                                                            substring search
  ---------------------------------------------------------------------------------------------------------------------------

**Revision:** string traversal, character frequency, `substr()`,
`find()`, `stoi()`, matrix boundaries and in-place transformations.

## 4. Binary Search --- P0

  -----------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                             Difficulty       Key pattern
  ---------------------- ----------------------------------------------------------------------------- ---------------------- -------------
          \[ \]          [Binary Search](https://leetcode.com/problems/binary-search/)                          Easy          Standard
                                                                                                                              binary search

          \[ \]          [Search in Rotated Sorted                                                             Medium         Identify
                         Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)                                sorted half

          \[ \]          [Find Minimum in Rotated Sorted                                                       Medium         Compare `mid`
                         Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)                          with `high`

          \[ \]          [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)             Medium         Binary search
                                                                                                                              on answer

          \[ \]          First and Last Occurrence in Sorted Array                                              Easy          Lower/upper
                                                                                                                              bound

          \[ \]          Allocate Minimum Number of Pages                                                      Medium         Binary search
                                                                                                                              on answer
  -----------------------------------------------------------------------------------------------------------------------------------------

**Revision:** `low`, `high`, `mid`, lower/upper bound, rotated arrays,
monotonic search spaces, feasibility checks and `long long` bounds.

## 5. Linked Lists --- P0

  ------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                        Difficulty       Key pattern
  ---------------------- ------------------------------------------------------------------------ ---------------------- -------------
          \[ \]          [Reverse Linked                                                                   Easy          Three
                         List](https://leetcode.com/problems/reverse-linked-list/)                                       pointers

          \[ \]          [Merge Two Sorted                                                                 Easy          Dummy head
                         Lists](https://leetcode.com/problems/merge-two-sorted-lists/)                                   

          \[ \]          [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)             Easy          Floyd's
                                                                                                                         algorithm

          \[ \]          [Remove Nth Node From End of                                                     Medium         Fast pointer
                         List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)                          `n` steps
                                                                                                                         ahead

          \[ \]          Find Middle of Singly Linked List                                                 Easy          Fast/slow
                                                                                                                         pointers

          \[ \]          [Linked List Cycle                                                               Medium         Find cycle
                         II](https://leetcode.com/problems/linked-list-cycle-ii/)                                        entry

          \[ \]          [Reorder List](https://leetcode.com/problems/reorder-list/)                      Medium         Middle +
                                                                                                                         reverse +
                                                                                                                         merge
  ------------------------------------------------------------------------------------------------------------------------------------

**Revision:** node structure, pointer reassignment, dummy nodes, cycle
detection, null checks and memory management basics.

## 6. Stacks & Queues --- P1

  ----------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                            Difficulty       Key pattern
  ---------------------- ---------------------------------------------------------------------------- ---------------------- -------------
          \[ \]          [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)                 Easy          Stack

          \[ \]          [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)              Medium         Monotonic
                                                                                                                             stack

          \[ \]          Next Greater Element to Right                                                        Medium         Monotonic
                                                                                                                             stack

          \[ \]          [Implement Stack Using                                                                Easy          Queue
                         Queues](https://leetcode.com/problems/implement-stack-using-queues/)                                

          \[ \]          [Implement Queue Using                                                                Easy          Stack
                         Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)                                

          \[ \]          [Min Stack](https://leetcode.com/problems/min-stack/)                                Medium         Auxiliary
                                                                                                                             stack

          \[ \]          [Evaluate Reverse Polish                                                             Medium         Stack
                         Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)                          
  ----------------------------------------------------------------------------------------------------------------------------------------

**Revision:** LIFO vs FIFO, `stack`, `queue`, `deque`, monotonic stacks,
next greater/smaller element and amortized complexity.

## 7. Heaps & Priority Queues --- P1

  ------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                        Difficulty       Key pattern
  ---------------------- ------------------------------------------------------------------------ ---------------------- -------------
          \[ \]          [Kth Largest Element in an                                                       Medium         Min-heap of
                         Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)                          size K

          \[ \]          [Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)             Easy          Max-heap

          \[ \]          [K Closest Points to                                                             Medium         Heap
                         Origin](https://leetcode.com/problems/k-closest-points-to-origin/)                              

          \[ \]          [Top K Frequent                                                                  Medium         Frequency +
                         Elements](https://leetcode.com/problems/top-k-frequent-elements/)                               heap

          \[ \]          [Find Median from Data                                                            Hard          Two heaps
                         Stream](https://leetcode.com/problems/find-median-from-data-stream/)                            
  ------------------------------------------------------------------------------------------------------------------------------------

**C++ revision:** - `priority_queue<int>` is a max-heap. -
`priority_queue<int, vector<int>, greater<int>>` is a min-heap. - Know
custom comparators, heap push/pop and `O(log N)` operations.

## 8. Bit Manipulation & Mathematics --- P1

  ---------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                        Difficulty       Key pattern
  ---------------------- -------------------------------------------------------- ---------------------- --------------
          \[ \]          [Single                                                           Easy          XOR
                         Number](https://leetcode.com/problems/single-number/)                           

          \[ \]          [Number of 1                                                      Easy          Brian
                         Bits](https://leetcode.com/problems/number-of-1-bits/)                          Kernighan

          \[ \]          [Counting                                                         Easy          DP + bit
                         Bits](https://leetcode.com/problems/counting-bits/)                             manipulation

          \[ \]          Prime Number Check                                                Easy          Divisors up to
                                                                                                         square root

          \[ \]          Sieve of Eratosthenes                                            Medium         Boolean sieve
  ---------------------------------------------------------------------------------------------------------------------

**Revision:** AND, OR, XOR, NOT, shifts, XOR cancellation,
set/clear/toggle bits and `n & (n - 1)`.

## 9. Recursion & Backtracking --- P1

  ------------------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                               Difficulty       Key pattern
  ---------------------- ------------------------------------------------------------------------------- ---------------------- ------------------
          \[ \]          [Subsets](https://leetcode.com/problems/subsets/)                                       Medium         Include/exclude

          \[ \]          [Combination Sum](https://leetcode.com/problems/combination-sum/)                       Medium         Backtracking

          \[ \]          [Permutations](https://leetcode.com/problems/permutations/)                             Medium         Visited array

          \[ \]          [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)             Medium         Constraint-based
                                                                                                                                recursion

          \[ \]          [Letter Combinations of a Phone                                                         Medium         Recursive choices
                         Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)                          
  ------------------------------------------------------------------------------------------------------------------------------------------------

**Revision:** base cases, recursion trees, state restoration,
backtracking and recursive time complexity.

## 10. Dynamic Programming --- P1

  -----------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                             Difficulty       Key pattern
  ---------------------- ----------------------------------------------------------------------------- ---------------------- -------------
          \[ \]          [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)                      Easy          1D DP

          \[ \]          [House Robber](https://leetcode.com/problems/house-robber/)                           Medium         State
                                                                                                                              transition

          \[ \]          [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)                   Medium         Kadane's
                                                                                                                              algorithm

          \[ \]          [Coin Change](https://leetcode.com/problems/coin-change/)                             Medium         Unbounded
                                                                                                                              knapsack

          \[ \]          [Longest Increasing                                                                   Medium         DP / binary
                         Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)                          search
  -----------------------------------------------------------------------------------------------------------------------------------------

**Revision:** memoization vs tabulation, state definition, base cases,
transitions and space optimization. For Kadane's algorithm, handle
all-negative arrays correctly; do not assume the answer is zero.

## 11. Binary Trees & BST --- P1

  -------------------------------------------------------------------------------------------------------------------------------------------------
           Done          Problem                                                                                     Difficulty       Key pattern
  ---------------------- ------------------------------------------------------------------------------------- ---------------------- -------------
          \[ \]          [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)                        Easy          DFS

          \[ \]          [Maximum Depth of Binary                                                                       Easy          DFS
                         Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)                                           

          \[ \]          [Lowest Common Ancestor of a                                                                  Medium         BST
                         BST](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)                          properties

          \[ \]          [Binary Tree Level Order                                                                      Medium         BFS
                         Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)                                 

          \[ \]          [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)              Easy          DFS

          \[ \]          [Same Tree](https://leetcode.com/problems/same-tree/)                                          Easy          Recursion

          \[ \]          [Validate Binary Search                                                                       Medium         Range
                         Tree](https://leetcode.com/problems/validate-binary-search-tree/)                                            validation
  -------------------------------------------------------------------------------------------------------------------------------------------------

**Revision:** preorder, inorder, postorder, recursive DFS, iterative
BFS, queue traversal, BST properties, height and depth.

## 12. Graph Fundamentals --- P2

  -----------------------------------------------------------------------------------------------------------------
                Done               Topic / problem                                              Pattern
  -------------------------------- ------------------------------------------------------------ -------------------
               \[ \]               Graph representation                                         Adjacency list /
                                                                                                matrix

               \[ \]               BFS traversal                                                Queue

               \[ \]               DFS traversal                                                Recursion

               \[ \]               Number of connected components                               DFS / BFS

               \[ \]               [Number of                                                   Grid DFS / BFS
                                   Islands](https://leetcode.com/problems/number-of-islands/)   

               \[ \]               Cycle detection in an undirected graph                       DFS / BFS
  -----------------------------------------------------------------------------------------------------------------

**Revision:** directed vs undirected, weighted vs unweighted, visited
arrays, connected components, grid traversal and basic cycle detection.

------------------------------------------------------------------------

## 13. C++ STL & Interview Checklist

### STL checklist

-   [ ] `vector`
-   [ ] `string`
-   [ ] `pair`
-   [ ] `unordered_map`
-   [ ] `map`
-   [ ] `unordered_set`
-   [ ] `set`
-   [ ] `stack`
-   [ ] `queue`
-   [ ] `deque`
-   [ ] `priority_queue`
-   [ ] `sort()`
-   [ ] `reverse()`
-   [ ] `lower_bound()`
-   [ ] `upper_bound()`
-   [ ] `accumulate()`

### Complexity quick reference

  Operation                   Typical complexity
  ------------------------- --------------------
  Vector indexed access                   `O(1)`
  Vector insertion at end       `O(1)` amortized
  Map search                          `O(log N)`
  Unordered map search            `O(1)` average
  Set search                          `O(log N)`
  Heap push/pop                       `O(log N)`
  Binary search                       `O(log N)`
  Comparison sorting                `O(N log N)`

Unordered containers can degrade to `O(N)` in the worst case due to hash
collisions.

### Interview explanation framework

For every problem:

1.  Understand the problem and constraints.
2.  Explain brute force.
3.  State brute-force time and space complexity.
4.  Identify the bottleneck.
5.  Explain the optimal pattern and why it works.
6.  Write clean C++.
7.  Dry-run with an example.
8.  Test edge cases.

**Edge cases to check:** empty input, one element, duplicates, negative
numbers, all-equal elements and maximum constraints.

------------------------------------------------------------------------

## 14. Final Revision Schedule

### 13 November --- Foundation & Hashing

-   [ ] Arrays and hashing
-   [ ] Prefix sums
-   [ ] Binary search
-   [ ] DBMS revision
-   [ ] C++ STL

### 14 November --- Core Patterns

-   [ ] Two pointers revision
-   [ ] Sliding window revision
-   [ ] Fast & slow pointers
-   [ ] Linked lists
-   [ ] Stacks and queues

### 15 November --- Additional Patterns

-   [ ] Heap and priority queue
-   [ ] Recursion and backtracking
-   [ ] Binary trees
-   [ ] Binary search on answer
-   [ ] Selected DP problems

### 16 November --- Technical Preparation

-   [ ] Graph fundamentals
-   [ ] Arrays and hashing revision
-   [ ] C++ concepts and STL
-   [ ] SQL and DBMS
-   [ ] GenAI fundamentals
-   [ ] Project explanation

### 17 November --- Final Mock & Revision

-   [ ] One timed coding mock
-   [ ] HackerRank practice
-   [ ] Pseudocode practice
-   [ ] Revise important templates
-   [ ] Revise complexity analysis
-   [ ] Technical interview dry runs
-   [ ] Project architecture revision
-   [ ] GenAI and CS fundamentals revision

### 18 November --- Technical Assessment

-   [ ] Read questions and constraints carefully
-   [ ] Start with familiar problems
-   [ ] Test edge cases
-   [ ] Manage assessment time
-   [ ] Stay calm

### 19 November --- Technical Interview

-   [ ] Introduce yourself confidently
-   [ ] Explain brute force and optimal approaches
-   [ ] Dry-run clearly
-   [ ] Explain C++ STL choices
-   [ ] Explain projects and design decisions
-   [ ] Answer follow-up questions thoughtfully

------------------------------------------------------------------------

## 15. Resources

-   [NeetCode 150](https://neetcode.io/practice)
-   [LeetCode](https://leetcode.com/problemset/)
-   [HackerRank](https://www.hackerrank.com/)
-   [Pseudocode
    Practice](https://pseudo-studio.in/pseudocode-practice-questions)
-   [IndiaBix](https://www.indiabix.com/)

------------------------------------------------------------------------

## Final commitment

**Target:** Capgemini Exceller --- 13 LPA\
**Primary language:** C++\
**Preparation window:** 13--17 November 2026

The objective is not to complete every question mechanically. It is to
become capable of solving unfamiliar problems using the patterns already
learned.

**Brute Force → Optimization → Implementation → Dry Run → Complexity
Analysis**

No unnecessary resource switching. No panic-driven preparation.
Consistent, focused execution.

*Waheguru Ji Mehar Karan.*
