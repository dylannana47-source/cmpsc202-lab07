# Lab 07: The Integer-Valued Knapsack Problem

**Name:** _Marshall Obeng Benne ______________________  

## Introduction
In this lab, you will explore different variations of the Knapsack problem and design a dynamic programming solution for one of them. You may work in small groups or individually, but everyone should write up their own answers to the questions below. 

Suppose we're going hiking. We have items $1, \ldots, n$ that we could pack in multiples with corresponding values $v_1, \ldots, v_n$ and weights $w_1, \ldots, w_n$. For example, we might have a water bottle (weight 1, value 2) and a sandwich (weight 2, value 5). Consider the following three problem variations:

1. Maximize value subject to capacity $W$, where you can pack at most one of each item.
2. Maximize value subject to capacity $W$, where you can pack a positive integer value of each item.
3. Minimize weight subject to minimum value $V$, where you can pack a positive integer value of each item.

---

## Part 1: Dynamic Programming Fundamentals

**Question:** Which of the three problem variations can be solved using the dynamic programming approaches we've studied in lecture? Explain your reasoning.

*Your Answer:*
All three variations can be solved with dynamic programming. Variations 1 and 2 have an optimal-substructure property: a solution contains an optimal solution for the smaller capacity remaining after selecting an item. Variation 2 simply considers an item repeatedly, while variation 1 prevents repetition by also keeping track of which items have been used. Variation 3 has the analogous optimal-substructure property for the weight required to reach a value threshold, so it can also be solved by dynamic programming while tracking total value.

## Part 2: The Naive Recursive Algorithm

Below is a naive recursive algorithm intended to solve one of the variations listed above.

```text
Function MaxValue(W):
    if W == 0:
        return 0
        
    max_val = 0
    for i from 1 to n:
        if w_i <= W:
            current_val = v_i + MaxValue(W - w_i)
            if current_val > max_val:
                max_val = current_val
                
    return max_val
```

**Question:** Which of the three problem variations (1, 2, or 3) does this algorithm solve? How do you know? Explain your reasoning.

*Your Answer:*
This algorithm solves variation 2, the unbounded knapsack problem. It selects an item $i$ if $w_i \leq W$, adds that item's value, and then recursively solves the remaining capacity $W-w_i$. Because the selected item is not removed or marked as used, the same item can be selected again. It therefore allows any positive integer quantity of each item, subject to the knapsack capacity.

## Part 3: Recursion Tree

Consider the following simple example:
* **Knapsack Capacity:** $W = 3$
* **Item 1:** Weight $w_1 = 1$, Value $v_1 = 2$
* **Item 2:** Weight $w_2 = 2$, Value $v_2 = 5$
* **Item 3:** Weight $w_3 = 1$, Value $v_3 = 3$

**Question:** Draw the full recursion tree for `MaxValue(3)` using the example above. Label the remaining capacity at each node and indicate which item is chosen along each branch.

*Your Answer (take a picture and upload it):*

```text
MaxValue(3)
├── choose item 1 -> MaxValue(2)
│   ├── choose item 1 -> MaxValue(1)
│   │   ├── choose item 1 -> MaxValue(0)
│   │   └── choose item 3 -> MaxValue(0)
│   ├── choose item 2 -> MaxValue(0)
│   └── choose item 3 -> MaxValue(1)
│       ├── choose item 1 -> MaxValue(0)
│       └── choose item 3 -> MaxValue(0)
├── choose item 2 -> MaxValue(1)
│   ├── choose item 1 -> MaxValue(0)
│   └── choose item 3 -> MaxValue(0)
└── choose item 3 -> MaxValue(2)
    ├── choose item 1 -> MaxValue(1)
    │   ├── choose item 1 -> MaxValue(0)
    │   └── choose item 3 -> MaxValue(0)
    ├── choose item 2 -> MaxValue(0)
    └── choose item 3 -> MaxValue(1)
        ├── choose item 1 -> MaxValue(0)
        └── choose item 3 -> MaxValue(0)
```

Each branch label is the item chosen, and the capacity after that choice is shown in the recursive call.


## Part 4: Recurrence Relation

**Question:** Derive a mathematical recurrence relation for $OPT(W)$, the maximum value you can pack into a knapsack of capacity $W$, based on the naive recursive algorithm. Be sure to state your base case(s) and explain how you arrived at your formula.

*Your Answer:*

\[
\operatorname{OPT}(W)=
\begin{cases}
0, & W=0,\\
\displaystyle\max_{\substack{1\leq i\leq n\\w_i\leq W}}
\left(v_i+\operatorname{OPT}(W-w_i)\right), & W>0.
\end{cases}
\]

The base case is that a knapsack with capacity zero has value zero. For a positive remaining capacity $W$, the recurrence considers every item that fits, adds its value to the optimal value obtainable with capacity $W-w_i$, and keeps the greatest total. The recurrence has no item-used constraint, so an item may be chosen more than once.


## Part 5: Bottom-Up Dynamic Programming

Instead of using the slow recursive approach, we can build a table bottom-up. 

**Question:** Use your recurrence relation from Part 4 to fill in the following table for the example provided in Part 3 ($w_1=1, v_1=2$ and $w_2=2, v_2=5$). At each step, record the optimal value and the specific item chosen to achieve that value. 

| Capacity ($W$) | Optimal Value ($OPT$) | Item Chosen |
|:---:|:---|:---|
| **0** | 0 | None |
| **1** | 2 | Item 1 |
| **2** | 5 | Item 2 |
| **3** | 7 | Item 1 (tied with Item 2) |
| **4** | 10 | Item 2 |
| **5** | 12 | Item 1 (tied with Item 2) |
| **6** | 15 | Item 2 |

*Explain how you calculated the row for $W=6$*

For Item 1: $2+\operatorname{OPT}(5)=2+12=14$.
For Item 2: $5+\operatorname{OPT}(4)=5+10=15$.
Thus, $\operatorname{OPT}(6)=15$, and Item 2 is chosen.


## Part 6: Traceback

**Question:** Using the "Item Chosen" column from the table you completed in Part 5, trace back to find the exact quantities of each item to pack that maximize the value. What is the final optimal packing configuration for $W=6$ and the maximum value?

*Your Answer:*
Choosing Item 2 at capacity $6$ leaves capacity $4$. Choosing Item 2 again leaves capacity $2$, and choosing Item 2 a final time leaves capacity $0$. Therefore, the optimal packing is **three copies of Item 2**, with maximum value **$3(5)=15$**.
