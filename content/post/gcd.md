---
title: "Greatest common divisor"
date: 2021-03-26T01:22:19+01:00
summary: "A proof for the euclidean algorithm"
categories: ["Mathematics", "Cryptography"]
draft: false
---

# Proof of the euclidean algorithm

```
function gcd(a, b) {
    while b != 0
        t = b
        b = a % b
        a = t
    return a
}

function gcd(a, b) {
    if b == 0
        return a
    else
        return gcd(b, a % b)
}
```

Okay, so above there is some pseudocode for the euclidean algorithm for calculating the greatest common divisor
of two integers. Now this algorithm is pretty short and simple, but if you're like me, you will not be able
to remember it as long as you don't know why it works. So let's find out.

We define $gcd(a, b)$ as the greatest common divisor of a and b. We can assume that $b$ is always less than
$a$ because $gcd(a, b) = gcd(b, a)$. Sure, $a = b$ is another possibilty, but it is fairly obvious that $gcd(a, a) = a$, so I will
disregard this case for now.
The recursive algorithm tells us that (1) $gcd(a, 0) = a$ and (2) $gcd(a, b) = gcd(b, r)$ where $r$ is the remainder of
the long division $a / b$. We can prove that this is true. We first split up $a$ into an integer multiple of $b$ and a reminder $r$:
$$a = bq + r$$

We take some integer $p$ that divides both $a$ and $b$, so $p | a, p | b$. This implies that $p | (a - bq)$ and
since $a - bq = r$, $p|r$. Now we take another integer $e$ so that $e|b, e|r$. We conclude that $e|(bq + r)$ and therefore
also divides $a$. That means that every integer that divies $a$ and $b$ also divides $b$ and $r$ and vice versa. And when
the common divisors of $a$ and $b$ are also the common divsors of $b$ and $r$, the greatest common divisors are also equal, therefore:
$$gcd(a,b) = gcd(b,r)$$

Now, unfortunately, in order to prove the algorithm, we have to formalize it. The algorithm is a sequence of steps (duh!),
the steps are defined like this:

$$r_{i-2} = r_{i-1}q_{i} + r_{i} \quad 0 \leq r_{i} \lt r_{i-1}$$

Where $r_{-2} = a$ and $r_{-1} = b$. We know that
$$gcd(r_{i-2}, r_{i-1}) = gcd(r_{i-1}, r_{i}) \quad \forall i = 1,2,3,...$$
by applying what we have just proven. Now assuming there is an integer $t$ so that
$r_{t+1} = 0$ then $gcd(r_{t}, r_{t+1}) = gcd(r_{t}, 0) = r_{t}$. By our equation from above
we know that $gcd(r_{t}, r_{t+1}) = gcd(r_{-2}, r_{-1}) = gcd(a, b)$. Therefore if such a $t$ exists,
our algorithm produces the correct solution. We can easily see that there must be an $r_{t+1} = 0$,
since $r_{i}$ is strictly decreasing for growing i. Therefore, the euclidean algorithm works... yay!
