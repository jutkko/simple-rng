# simple-rng
[![Build Status](https://travis-ci.org/jutkko/simple-rng.svg?branch=master)](https://travis-ci.org/jutkko/simple-rng)

# Preface
Random numbers are too important to be ignored. I wonder why is it not studied
in my undergraduate course? Is it too hard? Is it too obvious (hell no!)? Or
it is well understood (probably but not to fresh college students)?

One of my favorite sentences while reading the RNG chapter in The Art of
Computer Programming
> Random numbers should not be generated with a method chosen at random.
> Some theory should be used.

# Notes from reading the chapters
## Linear Congruential Method
Most of the today's random number generators are of the form of the following
scheme introduced by D. H. Lehmer in 1949.

```
X_(n+1) = (a*X_n+c) mod m
```

Where

```
m, the modulus                  m > 0
a, the multiplier               0 <= a < m
c, the increment                0 <= c < m
X_0, the starting value (seed)  0 <= X_0 < m
```

The `X_n` are the random numbers from this sequence. The modulus will constraint
the range of `X_n` to be within `[0, m)`, and we can derive other types of
random numbers using this as a starting point.

The choice of the above parameters affects the nature of the generated sequence
greatly. For example, when `c` is `0`, it is a special case when the algorithm
generates the numbers quicker than when `c` is not `0`. Furthermore, the term
*multiplicative congruential method* is used to refer the scheme when `c` is
`0`.

### Multiplicative Congruential Method
It is obvious that `X_0` cannot be `0` in this case. And the new scheme becomes

```
X_(n+1) = a*X_n mod m
```

Which is equivalent to

```
X_n = a^n*X_0 mod m
```

Therefore the period for this is essentially the smallest `k` which

```
a^k = 1 mod m
```

[Euler's Theorem][Euler's Theorem] states that if `a` and `m` are coprime, then

```
a^(phi(m)) = 1 mod m
```

Where `phi(m)` is Euler's Totient function. For prime `m`s, we know that `phi(m)`
is `m-1`. For `a`s that have `k = phi(m)` we know that the sequence will have
the period of `m`, such `a`s are called a primitive root modulo `m`. If we are
solely interested in long period sequences, we shall use an `m` that has
primitive roots and one of its primitive roots.

This can be achieved by taking an `m` which is `1`, `2`, `4` or of the form
`2^x*p^y`, where `p` is an odd prime `0 <= x <= 1` and `y >= 1`.

Provided the simplicity of this algorithm, it is considered to be generating
numbers that don't pass the [Spectral Test][Spectral Test]. One famous example
for this algorithm in practice is the [RANDU][RANDU] machine from IBM.

### Mixed Congruential Method
The real power for the congruential method comes into play when `c != 0`. The
goal for random number generations is usually have better speed, period and
randomness.  Taking modulus of a large number can be inefficient, so the choice
of `m` can affect how quickly we can generate the sequence. We shall cover the
way we assess the randomness of a sequence later in these notes.

#### Choice of Modulus
The choice of modulus `m` will directly affect the sequence's period and the
speed of sequence generation. One convenient choice for it can be `2^32` because
it is the size of a word on a machine and modulo arithmetics can be optimised
in this case. To see more discussions on this topic, go to 3.2.1.1 in TAOCP.

#### Choise of Multiplier

Having chosen `m`, we only have that many options to choose `a`. To choose `a`
where we achieve the period of length `m`, we need to have the help from
Theorem A

```
c is coprime to m
b = a-1 is multiple of p, for every prime p dividing m
b is a multiple of 4, if m is a multiple of 4
```

Confused? See more confusing proofs in 3.2.1.2 of TAOCP.

#### Potency
Just to make the things more complicated, long period does not mean that the
sequence generated is random enough. Here we will introduce the concept of
potency. For linear congruential sequences with maximum period is the least
positive integer `s` such that

```
b^s = 0 mod m
```

Where `b = a-1`

We know that such `s` exists, since `b` is a multiple of every prime dividing m.
In order to have sufficiently randomness, we usually require the array of
numbers to have at least potency of `5`.

## Verification of Randomness
Needs to be done. Too lazy to do it now.

# Reference
- Random number generators in Chapters 3.1, 3.2 of The Art of Computer Programming
- Tricks about testing random numbers in Chapter 3.3 of The Art of Computer Programming
- Multiplicative congruential method from [Math Explorers' Club](http://www.math.cornell.edu/~mec/Winter2009/Luo/Linear%20Congruential%20Generator/linear%20congruential%20gen1.html)
- [LFSR](http://pdfserv.maximintegrated.com/en/an/AN4400.pdf)
- Golang rng [source](https://golang.org/src/math/rand/rng.go)

[Euler's Theorem]: https://en.wikipedia.org/wiki/Euler%27s_theorem
[RANDU]: https://en.wikipedia.org/wiki/RANDU
[Spectral Test]: https://en.wikipedia.org/wiki/Spectral_test

# Project52
This is a project from my [Project52](https://github.com/jutkko/project52).
