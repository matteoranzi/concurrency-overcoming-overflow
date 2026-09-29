# concurrency-overcoming-overflow
Project for Concurrency @unitn 2026-27

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

The following algorithm, here called *Overcoming Overflow* algorithm, is one such attempt.




![overcoming-overflow-algorithm](report/imgs/overcoming-overflow-algorithm.png)
*The Overcoming Overflow algorithm attempts to prevent integer overflow in Lamport's Bakery algorithm, but introduces a major bug: **it does not ensure mutual exclusion**.*



---







[^1]: [Leslie Lamport, “A New Solution of Dijkstra’s Concurrent Programming Problem,” Communications of the ACM, 17(8), August 1974, pp. 453–455](https://dl.acm.org/doi/epdf/10.1145/361082.361093)