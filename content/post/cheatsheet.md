---
title: "Cheatsheet"
date: 2021-07-26T21:05:30+02:00
summary: ""
categories: []
draft: true
---

[lmao](#Cryptography-Cheatsheet)
[lmao](#second)

# Cryptography Cheatsheet

## RSA stuff

### Algorithm

* choose primes $p, q$
* $n = pq$
* $\lambda(n) = lcm(\lambda(p), \lambda(q))$
  * $\lambda(p) = \varphi(p) = p-1$ when $p$ is prime
  * $\lambda(n) = lcm(p-1, q-1)$
  * $lcm(a, b) = \frac{|ab|}{gcd(a, b)}$
* choose $e$ coprime to $\lambda(n)$, e.g. 65537
* $d \equiv e^{-1}\ (\textrm{mod}\ n)$
* public key: $e, n$
* private key: $d$
#### encryption

$c \equiv m^e\ (\textrm{mod}\ n)$

#### decryption
$m \equiv c^d\ (\textrm{mod}\ n)$

{{< highlight python >}}
# modular multiplicative inverse
pow(base, -1, mod)

def gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a 

def lcm(a, b):
    return a * b // gcd(a, b)

from functools import reduce
# lcm for more than 2 values
def lcmm(*args):
    return reduce(lcm, args)

{{< /highlight >}}

## Diffie-Hellman stuff

### Discrete Logarithm problem
$b^k = a$ is hard to solve for $k$

#### Baby-step Giant-step algorithm

#### Pohlig-Hellman algorithm

## Math stuff

### Groups

Definition (lifted verbatim from Wikipedia):

A group is a set ${\displaystyle G}$ together with a binary operation on ${\displaystyle G}$, here denoted
"$\cdot$", that combines any two elements ${\displaystyle a}$ and ${\displaystyle b}$ to form an element
of ${\displaystyle G}$, denoted ${\displaystyle a\cdot b}$, such that the following three requirements, known as group axioms,
are satisfied:

* Associativity
* Identity element
* Inverse element

## Numbers to bytes and vice versa

{{< highlight python >}}
# PyCryptodome Library -- pip install pycryptodome
from Crypto.Util.number import long_to_bytes, bytes_to_long
{{< /highlight >}}



