# 1.4 Solved examples

## 1.4.1 Aggregation


### 1.4.1.1
<a href="https://www.codecogs.com/eqnedit.php?latex=is\_prime(n)&space;=&space;\forall&space;k&space;\in&space;[2,&space;n-1],&space;k^2&space;\leq&space;n,&space;n&space;\neq&space;0\&space;mod\&space;k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?is\_prime(n)&space;=&space;\forall&space;k&space;\in&space;[2,&space;n-1],&space;k^2&space;\leq&space;n,&space;n&space;\neq&space;0\&space;mod\&space;k" title="is\_prime(n) = \forall k \in [2, n-1], k^2 \leq n, n \neq 0\ mod\ k" /></a>

```scala
def is_prime(n: Int): Boolean = (2 to n-1).takeWhile(k => k*k <= n).forall(k => n % k != 0)
```
---

### 1.4.1.2

<a href="https://www.codecogs.com/eqnedit.php?latex=\prod_{k\in&space;[1,&space;10]}^{\&space;}&space;\left&space;|&space;sin(k&space;&plus;&space;2)&space;\right&space;|" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\prod_{k\in&space;[1,&space;10]}^{\&space;}&space;\left&space;|&space;sin(k&space;&plus;&space;2)&space;\right&space;|" title="\prod_{k\in [1, 10]}^{\ } \left | sin(k + 2) \right |" /></a>
```scala
(1 to 10).map(k => math.abs(math.sin(k + 2))).product
```
---

### 1.4.1.3

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum_{k\in&space;[1,&space;10];\&space;cos\&space;k>0}^{\&space;}\sqrt{cos\&space;k}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum_{k\in&space;[1,&space;10];\&space;cos\&space;k>0}^{\&space;}\sqrt{cos\&space;k}" title="\sum_{k\in [1, 10];\ cos\ k>0}^{\ }\sqrt{cos\ k}" /></a>
```scala
(1 to 10).filter(k => math.cos(k) > 0).map(k => math.sqrt(math.cos(k))).sum
```
---

### 1.4.1.4

<a href="https://www.codecogs.com/eqnedit.php?latex=average(s)&space;=&space;\frac{1}{n}\sum_{t=0}^{n-1}&space;s_{t}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?average(s)&space;=&space;\frac{1}{n}\sum_{t=0}^{n-1}&space;s_{t}" title="average(s) = \frac{1}{n}\sum_{t=0}^{n-1} s_{t}" /></a>
```scala
def average(s: List[Double]): Double = s.sum / s.size
```
---

### 1.4.1.5

<a href="https://www.codecogs.com/eqnedit.php?latex=wallis(n)&space;=&space;\frac{2}{1}\frac{2}{3}\frac{4}{3}\frac{4}{5}\frac{6}{5}\frac{7}{8}\cdots&space;\frac{2n}{2n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?wallis(n)&space;=&space;\frac{2}{1}\frac{2}{3}\frac{4}{3}\frac{4}{5}\frac{6}{5}\frac{7}{8}\cdots&space;\frac{2n}{2n&plus;1}" title="wallis(n) = \frac{2}{1}\frac{2}{3}\frac{4}{3}\frac{4}{5}\frac{6}{5}\frac{7}{8}\cdots \frac{2n}{2n+1}" /></a>

- define helper function `wallis_frac(i)` computes the ith fraction. the method .toDouble converts integers to Double numbers

<a href="https://www.codecogs.com/eqnedit.php?latex=wallis(n)&space;=&space;\frac{2}{1}\frac{2}{3}\frac{4}{3}\frac{4}{5}\frac{6}{5}\frac{7}{8}\cdots&space;\frac{2n}{2n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?wallis(n)&space;=&space;\frac{2}{1}\frac{2}{3}\frac{4}{3}\frac{4}{5}\frac{6}{5}\frac{7}{8}\cdots&space;\frac{2n}{2n&plus;1}" title="wallis(n) = \frac{2}{1}\frac{2}{3}\frac{4}{3}\frac{4}{5}\frac{6}{5}\frac{7}{8}\cdots \frac{2n}{2n+1}" /></a>

= <a href="https://www.codecogs.com/eqnedit.php?latex=wallis(n)&space;=&space;\prod_{n=1}^{\infty}&space;\frac{2n}{2n-1}\cdot&space;\frac{2n}{2n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?wallis(n)&space;=&space;\prod_{n=1}^{\infty}&space;\frac{2n}{2n-1}\cdot&space;\frac{2n}{2n&plus;1}" title="wallis(n) = \prod_{n=1}^{\infty} \frac{2n}{2n-1}\cdot \frac{2n}{2n+1}" /></a>

```scala
def wallis_frac(n: Int): Double = (2 * n).toDouble/(2 * n - 1)*(2 * n)/(2 * n + 1)
def wallis(n: Int) = (1 to n).map(wallis_frac).product
```
---

### 1.4.1.6

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum_{k=1}^{\infty}&space;\frac{1}{k^{2}}&space;=&space;\frac{\pi^2}{6}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum_{k=1}^{\infty}&space;\frac{1}{k^{2}}&space;=&space;\frac{\pi^2}{6}" title="\sum_{k=1}^{\infty} \frac{1}{k^{2}} = \frac{\pi^2}{6}" /></a>

1. First, define a function of n that computes a partial sum of that series until k = n.
2. Then compute the partial sum for a large value of n and compare with the limit value.
```scala
// val n = Integer.MAX_VALUE
val n = 100000
(1 to n).map(k => 1.0 / k / k).sum
4 * math.atan(1)
// assert((1 to n).map(k => 1.0 / k / k).sum == 4 * math.atan(1))
```

---

### 1.4.1.7

<a href="https://www.codecogs.com/eqnedit.php?latex=\prod_{k=1}^{\infty}\left&space;(&space;1&space;-&space;\frac{x^2}{k^2}&space;\right&space;)&space;=&space;\frac{sin\pi}{\pi&space;x}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\prod_{k=1}^{\infty}\left&space;(&space;1&space;-&space;\frac{x^2}{k^2}&space;\right&space;)&space;=&space;\frac{sin\pi}{\pi&space;x}" title="\prod_{k=1}^{\infty}\left ( 1 - \frac{x^2}{k^2} \right ) = \frac{sin\pi}{\pi x}" /></a>

- Compute this product up to k = n for x = 0.1 with a large value of n, say n = 105, and compare with the right-hand side:

```scala
(n: Int, x: Double) => (1 to n).map(k => 1.0 - x*x/k/k).product

val pi = 4 * math.atan(1)
math.sin(pi) * x / pi * x 
```

---

### 1.4.1.8

-Define a function p that takes a list of integers and a function f: Int => Int, and returns the largest value of f (x) among all x in the list.

```scala
def p(s: List[Int], f: Int => Int): Int = s.map(f).max
```

---

## 1.4.2 Transformation

### 1.4.2.1

- Given a list of lists, s: List[List[Int]], select the inner lists of size at least 3. The result must be again of type List[List[Int]].

```scala
(s: List[List[Int]]): List[Int] = s.filter(k => k.size >= 3)
```

---

### 1.4.2.2
Find all integers k ∈ [1, 10] such that there are at least three different integers j, 
where 1 ≤ j ≤ k, each j satisfying the condition j2 > 2k.

```scala
(1 to 10).filter(k => (1 to 10).filter(j * j > 2 * k).size >= 3)
```
