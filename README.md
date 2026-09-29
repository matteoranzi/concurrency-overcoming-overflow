#### TL;DR
**TLA+ and Owicki-Gries analysis of a variant of Lamport's Bakery algorithm that mitigates integer overflow**.\
Starting from a flawed variant of the Bakery algorithm that doesn't ensure mutual exclusion, a further variant is proposed and formally shown to guarantee:
- mutual exclusion (main focus of the demonstration) {to_verify}
- deadlock freedom {to_verify}
- first-come, first-served {to_verify}

---



In 1974, Leslie Lamport published an article called *A New Solution of Dijkstra's Concurrent Programming Problem*[^1], where he proposed an algorithm to handle mutual exclusion between «$N$ *processors*» , that «*allows the system to continue to operate despite the failure of any individual component*» and «*is a first-come-first-served method*».

> *When a processor wants to enter its critical section, it first executes a loop-free block of code &ndash; i.e. one with a fixed number of execution steps. It is then guaranteed to enter its critical section before any other processor which later requests service.*


![Lamport's Mutual Exclusion Algorithm](report/imgs/lamport-mutual-exclusion-algorithm.png)
*Lamport's Bakery algorithm &mdash; Communications of the ACM, 17(8), August 1974, pp. 453–455.*

> Consider $N$ asynchronous computers communicating with each other only via shared memory. Each computer runs a cyclic program with two parts &ndash; a *critical section* and a *noncritical section*. 
>
> [...] the following conditions are satisfied:
> 1. At any time, at most one computer may be in its critical section.
> 2. Each computer must eventually be able to enter its critical section (unless it halts).
> 3. Any computer may halt in its noncritical section.
> 
> Moreover, no assumptions can be made about the running speeds of the computers.

---

As stated by Lamport himself in the *Further Remarks* section:

> If there is always at least one processor in the bakery, then the value of $number[i]$ can become arbitrarily large.
> 
> This problem cannot be solved by any simple scheme of cycling through a finite set of integers.

Even though «practical considerations will place an upper bound on the value of $number[i]$ in any real application» various attempts were made to address the issue of integer overflow.

The following algorithm, here called *Overcoming Overflow* algorithm (OO), is one such attempt.

![overcoming-overflow-algorithm](report/imgs/overcoming-overflow-algorithm.png)
*The Overcoming Overflow algorithm attempts to prevent integer overflow in Lamport's Bakery algorithm, but introduces a major bug: **it does not ensure mutual exclusion**.*

### Counterexample: Overcoming Overflow algorithm does not ensure mutual exclusion

//TODO

## Variant of the Overcoming Overflow algorithm
The *OO* variant differs from the original only in the comparison operator on line $04$

$$
\texttt{label[temp1]} \leq \texttt{label[temp2]}
$$

That is, $\leq$ is used instead of $\lt$.

The core idea is that in the original *OO* the condition at line $04$ is in a reversed lexicographical order compared to the one defined at line $12$: 
the combination of the $\texttt{for}$ loop at line $03$ and the inner selection of the highest label's index via the $\lt$ operator, implicitly creates a lexicographical order where labels with a greater index are prioritised. Contrary to the lexicographical condition at line $12$: $(label[j], j) \ll (label[i], i)$

Essentially, it is as if in the *doorway interval* we have the following lexicographically order:

$$
(a, b) \lt (c, d) \text{ if } a \lt c, \text{ or if } a=c \text { and } b \gt d 
$$

While in the *waiting interval* we have:

$$
(a, b) \lt (c, d) \text{ if } a \lt c, \text{ or if } a=c \text { and } b \lt d 
$$

Hence, assuming equal max label value, in the *doorway interval* we are referencing as "latest element" the ones with a smaller index, since in the $\texttt{for}$ loop at line $03$, the $\lt$ condition considers **only** the index of the first-occurence (lower), while in the *waiting interval* we are considering as "latest element" the ones with a bigger index.

**The following section will demonstrate that such simple edit will garantee mutual exclusion**.


---

# References

[^1]: [Leslie Lamport, “A New Solution of Dijkstra’s Concurrent Programming Problem,” Communications of the ACM, 17(8), August 1974, pp. 453–455](https://dl.acm.org/doi/epdf/10.1145/361082.361093)