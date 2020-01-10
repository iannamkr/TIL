# 1.1 Translating mathematics into code
1.1.1 First examples

1.1.2 Nameless functions

1.1.3 Nameless functions and bound variables


---


### 1.1.1 First examples
#### Example 1.1.1.1: **Factorial of 10**

<a href="https://www.codecogs.com/eqnedit.php?latex=\coprod_{k=1}^{10}&space;k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\coprod_{k=1}^{10}&space;k" title="\coprod_{k=1}^{10} k" /></a>
```scala
(1 to 10).product
```

#### Example 1.1.1.2: **Factorial as a function**

<a href="https://www.codecogs.com/eqnedit.php?latex=f(n)&space;=&space;\coprod_{k=1}^{n}&space;k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?f(n)&space;=&space;\coprod_{k=1}^{n}&space;k" title="f(n) = \coprod_{k=1}^{n} k" /></a>

```scala
def f(n: Int) = (1 to n).product
```


### 1.1.2 Nameless functions

<a href="https://www.codecogs.com/eqnedit.php?latex=n&space;\to&space;\coprod_{k=1}^{n}&space;k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?n&space;\to&space;\coprod_{k=1}^{n}&space;k" title="n \to \coprod_{k=1}^{n} k" /></a>

_a function that maps n to the product of all k where k goes from 1 to n_

```scala
(n: Int) => (1 to n).product
```

```scala
// 3628800
val fact = (n: Int) => (1 to n).product
fact(10)

((n: Int) => (1 to n).prodct)(10)

// 322687002
((n: Int) => n*n*n + n*n)(12345) = 12345*12345*12345 + 12345*12345
{ val n = 12345; n*n*n + n*n } 
```

#### Example 1.1.2.1: **prime numbers**
<a href="https://www.codecogs.com/eqnedit.php?latex=isprime(n)&space;=&space;\forall&space;k\in&space;[2,&space;n-1].&space;n\neq&space;0&space;\mod&space;k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?isprime(n)&space;=&space;\forall&space;k\in&space;[2,&space;n-1].&space;n\neq&space;0&space;\mod&space;k" title="isprime(n) = \forall k\in [2, n-1]. n\neq 0 \mod k" /></a>

> In Scala, it is strongly recommended (although often not mandatory) to specify the return type of named functions.

```scala
def is_prime(n: Int): Boolean = (2 to n-1).forall(k => n % k != 0)
```


### 1.1.3 Nameless functions and bound variables
- expression 안에서 정의된 지역 변수를 속박 변수 (bound variable)라고 한다.
- expression 안에서 사용되나, 외부에서 정의된 변수를 자유 변수 (free variable)라고 한다.

- 다음의 수식처럼 <a href="https://www.codecogs.com/eqnedit.php?latex=\forall&space;k&space;:&space;p(k),&space;\exists&space;k&space;:&space;p(k),&space;\sum_{k=a}^{b}&space;f(k),&space;\int_{0}^{1}k^{2}dk,&space;\lim_{n&space;\to&space;\infty}&space;f(n)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\forall&space;k&space;:&space;p(k),&space;\exists&space;k&space;:&space;p(k),&space;\sum_{k=a}^{b}&space;f(k),&space;\int_{0}^{1}k^{2}dk,&space;\lim_{n&space;\to&space;\infty}&space;f(n)" title="\forall k : p(k), \exists k : p(k), \sum_{k=a}^{b} f(k), \int_{0}^{1}k^{2}dk, \lim_{n \to \infty} f(n)" /></a> 속박변수는 다양한 형태로 사용하기 때문에 이를 code로 작성할때는 속박 변수(bound variable)에 유의해야한다.
- 수식의 속박 변수를 scala의 nameless function으로 작성하면, 원래 의도했던대로 동작할 것이다.
- 아래는 예시이다.
  - <a href="https://www.codecogs.com/eqnedit.php?latex=\forall&space;k&space;\in&space;[1,n]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\forall&space;k&space;\in&space;[1,n]" title="\forall k \in [1,n]" /></a>
  ```scala
  (1 to n).forall(k => p(k))
  ```
  - 이때, nameless function <a href="https://www.codecogs.com/eqnedit.php?latex=k&space;\rightarrow&space;p(k)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?k&space;\rightarrow&space;p(k)" title="k \rightarrow p(k)" /></a> 는 named function p 와 동일하므로 아래와 같이 간략화할 수 있다.
  ```scala
  (1 to n).forall(p)
  ```
  
  


