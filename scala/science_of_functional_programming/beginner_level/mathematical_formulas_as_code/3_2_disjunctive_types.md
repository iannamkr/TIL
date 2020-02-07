# 3.2 Disjunctive types

## 3.2.1 Motivation and first example

다음 이차방정식 <a href="https://www.codecogs.com/eqnedit.php?latex=x^2&space;&plus;&space;bx&space;&plus;&space;c&space;=&space;0" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x^2&space;&plus;&space;bx&space;&plus;&space;c&space;=&space;0" title="x^2 + bx + c = 0" /></a> 
근에 해당하는 type 을 `RootsOfQ` (= the real roots of a quadratic equation) 라고 정의할 때,

- `RootsOfQ`는 다음 중 하나의 타입에 속한다.
  - 근이 없는 경우: `()`
  - 근이 한 개인 경우: `Double`
  - 근이 두 개인 경우: `(Double, Double)`

이를 Scala `case class`로 정의하면 아래와 같다.

- `case class NoRoots()`
- `case class OneRoot(x: Double)`
- `case class TwoRoots(x: Double, y: Double)`

```scala
sealed trait RootsOfQ

final case class NoRoots() extends RootsOfQ
final case class OneRoot(x: Double) extends RootsOfQ
final case class TwoRoots(x: Double, y: Double) extends RootsOfQ
```

위처럼 "sealed trait / final case class" 문법은 정해진 `case class` 전체 집합에서 하나의 선택된 type을 정의하는데, 이러한 type을 
**disjunctive type** 또는 **union type** 이라고 한다.

## 3.2.2 Solved examples: Pattern matching for disjunctive types

**disjunctive type**인 `RootsOfQ`가 가질 수 있는 경우의 수는 `(NoRoots, OneRoot, TwoRoots)`과 같다.
`RootsOfQ` type의 value를 생성하는 방법은 위 3개의 `case class`중 하나를 선택하는 것이다. 

```scala
def f(r: RootsOfQ): String = r match {
  case NoRoots() => "no real roots"
  case OneRoots(r) => s"one real roots: $r"
  case TwoRoots(x, y) => s"real roots: ($x, $y)"
}

val x: RootsOfQ = OneRoot(2.0)
f(x)

res0: String = "one real root: 2.0"
```

`disjunctive types`을 사용하는 경우 match/case 문은 반드시 발생 가능한 모든 경우를 포함해야한다. 

```scala
def g(x: RootsOfQ): String = x match {
  case OneRoot(r) => s"one real root: $r"
}

// warning: match may not be exhaustive.
It would fail on the following inputs: NoRoots(), TwoRoots(_, _)
```

함수 `g`는 부분함수(partial function)이므로 `OneRoot(...)`인 경우에만 동작할 것이다.


## 3.2.3 Standard disjunctive types: Option, Either, Try

Scala library에서 사용되고 있는 disjunctive type (union type)은 `Option`, `Either`, `Try`가 있다.

### The `Option` Type
`Option` type은 다음의 1) 빈 tuple, 2) 1-element tuple 두 가지 type을 갖는 distunctive type 이다.  

```scala
sealed trait Option[T]
final case class object None extends Option[Nothing]
final case class object Some[T](t: T) extends Option[T]
```

`Option` type은 `None()`과 같이 빈 tuple에 해당하는 `case object`를 갖는다. Scala에서 `case object`는 type parmeter를
가질 수 없기 때문에 `None`는 특별한 type인 `Nothing`을 갖는다. 아무 값을 가지고 있지 않기 때문에 `void type`이라고도 불린다.

> scalaz같은 library에서는 빈 option value에 대해서 다음과 같이 구현되어있기도 하다.
> `final case class None[T]() extends Option[T]`

### Using `Option` with collections

많은 Scala library에서 제공하는 method들은 `Option`을 리턴한다. 다음은 sequence에서 사용되는 `Option`타입의 예이다.
`.find`, `.headOption`, `.lift`, `.get`

```scala
(1 to 10).find(_ > 5)
res: Option[Int] = Some(6)

(1 to 10).find(_ > 10) // No element is > 10.
res: Option[Int] = None

(10 to 100).lift(0)
res: Option[Int] = Some(10)

(10 to 100).lift(1000) // No element at index 1000.
res: Option[Int] = None

Seq(1, 2, 3).headOption
res: Option[Int] = Some(1)

Seq(1, 2, 3).filter(_ > 10).headOption
res: Option[Int] = None
```

`.find(p)`는 `.filter(p).headOption`과 결과는 동일하나, `.find(p)`가 더 빠르다.
`.get` 는 dictionary를 위한 함수이다.

```scala
Map(10 -> "a", 20 -> "b").get(10)
res: Option[String] = Some(a)

Map(10 -> "a", 20 -> "b").get(30)
res: Option[String] = None

Map(10 -> "a", 20 -> "b")(10)
res: String = a

Map(10 -> "a", 20 -> "b")(30)
java.util.NoSuchElementException: key not found: 30

Seq(10, 20, 30)(0)
res: Int = 10

Seq(10, 20, 30)(5)
java.lang.IndexOutOfBoundException: 5
```
