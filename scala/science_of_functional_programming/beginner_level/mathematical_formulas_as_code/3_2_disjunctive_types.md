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
