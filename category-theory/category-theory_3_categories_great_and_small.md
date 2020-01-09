# Table of contents
1. No Objects
2. Simple Graphs
3. Orders
4. Monoid as Set
5. Monoid as Category


## 1. No Objects
- zero object & zero morphism
- empty set makes sense, then how about an empty category?

## 2. Simple Graphs
- categories by connecting objects with arrows
  - 1. an identity arrow at each node
  - 2. any two composable arrows
  - ** Every time you add a new arrow, you have to consider its composition 
  with any other arrow (except for the identity arrows) and itsetlf
- `free category`
  - ![image](https://user-images.githubusercontent.com/13671946/71914103-39b50a00-31bc-11ea-9cc2-1b0e68e17246.png)
  - composition: ![image](https://user-images.githubusercontent.com/13671946/71914076-2ace5780-31bc-11ea-9748-f24665140117.png)

  - 1. all object for every node in the graph
  - 2. all possible chains of composable graph edges as morphisms

## 3. Orders
- A category is a relation between object: **the relation of being less than or equal**
- `preorder`: a < b and b < c then a < c (transitivity)
- `partial order`: a < b and b < a then a = b (anti-symmetry)
- `total order` or `linear order`: 두 object 간에 한 방향으로 비교가 가능한 경우
  - If a ≤ b and b ≤ a then a = b (antisymmetry)
  - If a ≤ b and b ≤ c then a ≤ c (transitivity)
  - a ≤ b or b ≤ a (totality)

- home-set `C(a, b)`
  - A set of morphisms(arrow) from object a to object b in a category C
  - Every home-set in a `preorder` is empty or singleton
    - home-set C(a,a), morphism from a to a must be a singleton, containing only the identity, in any preorder
  - **preorder may have cycles but forbidden in a partial order**
  - **Sorting algorithms, such as quick sort, bubble sort, merge sort, etc can only work correctly on total orders.**
  - **partial orders can be sorted using topological sort**

## Monoid as Set

- Monoid는 놀랍도록 간단하지만 매우 강력한 개념이다. 덧셈, 곱셈 등의 기본적인 수학의 기초이며 프로그래밍 어디에서도 찾을 수 있다. 
- `strings`, `lists`, `foldable data structure`, `futures in concurrent programming`, `events in functional reactive programming` 등등
- 전통적으로 모노이드는 이항연산을 갖춘 set으로 정의되며 결합법칙을 따르고 항등원을 갖는다.
- 결합법칙: (a + b) + c = a + (b + c) 
- 항등원: 0 + a = a, a + 0 = a 
- !교환법칙: a + b = b + a, 덧셈은 교환법칙이 성립하지만, monoid의 조건은 아니다.

### 예시
```
> (5 + 6) + 10 == 5 + (6 + 10)
True
> (5 * 6) * 10 == 5 * (6 * 10)
True
> ("Hello" ++ " ") ++ "world!" == "Hello" ++ (" " ++ "world!")
True

> 255 + 0 == 255 && 0 + 255 == 255
True
> 255 * 1 == 255 && 1 * 255 == 255
True
> [1,2,3] ++ [] == [1,2,3] && [] ++ [1,2,3] == [1,2,3]
True
```
monoid의 예
- Type `Int`에 대해 곱셈연산과 이에 대한 항등원 1
- Type `String` 에 대해 `String.concat` 연산과 이에 대한 항등원 `String.empty`
- Type `List`에 대해 `++` 연산과 이에 대한 항등원 `List.empty`
- Type `Boolean`에 대해 `&&` 연산과 이에 대한 항등원 `true`
- Type A => A 에 대한 morphism에 대해 `andThen` 연산 (function composition)과 항등원 _(x: A) => x_ (identity function)


### Examples in Scala

#### 1. 
```scala
import cats.Monoid
import cats.implicits._

// 0
Monoid[Int].empty
// 3
Monoid[Int].combine(1,2)

// 55
(1 to 10).toList.combineAll
(1 to 10).fold(Monoid[Int].empty)((a,b) => a.combine(b))
(1 to 10).fold(0)(_ + _)

/*
------------ Monoid[Int].combine -------------
            |    |    |    |    |    |    |    |    |    |
            v    v    v    v    v    v    v    v    v    v
((((((((((0 + 1) + 2) + 3) + 4) + 5) + 6) + 7) + 8) + 9) + 10)
          ^
          |_ Monoid[Int].empty
*/


```scala
// Map[Long, Int] = Map()
Monoid[Map[Long, Int]].empty

// Map[Long,Int] = Map(1 -> 110, 2 -> 999)
Monoid[Map[Long, Int]].combine(Map(1L -> 10), Map(1L -> 100, 2L -> 999))
```


#### 2.
- Input
```
List(
  Map(
    (Date(2016, 1, 4), cologne, pigeon) -> 37,
    (Date(2016, 1, 11), bonn, starling) -> 64,
    (Date(2016, 1, 18), cologne, bullfinch) -> 54
  ),
  Map(
    (Date(2016, 1, 18), cologne, bullfinch) -> 60,
    (Date(2016, 1, 18), bonn, bullfinch) -> 2,
    (Date(2016, 1, 25), dusseldorf, bullfinch) -> 75
  ),
  Map(
    (Date(2016, 1, 4), cologne, pigeon) -> 30
  )
)
```

- Output
```
Map(
  (Date(2016, 1, 4), cologne, pigeon) -> 67,
  (Date(2016, 1, 11), bonn, starling) -> 64,
  (Date(2016, 1, 18), cologne, bullfinch) -> 114,
  (Date(2016, 1, 18), bonn, bullfinch) -> 2,
  (Date(2016, 1, 25), dusseldorf, bullfinch) -> 75
)
```
- Solution
```scala
def aggregate(birdCounts: List[Map[(Date, Location, Species), Int]]) = 
  birdCounts
    .flatMap(_.toList)
    .groupBy(_._1)
    .map({ case (k,v) => (k, v.map(_._2).sum) })
```

```scala
import cats.implicits._

birdCounts.combineAll
```
