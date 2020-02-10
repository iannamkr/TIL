# Lists and trees: recursive disjunctive types 

## 3.3.1 Lists

```scala
sealed trait List[A]
final case class List0[A]() extends List[A]
final case class List1[A](x: A) extends List[A]
final case class List2[A](x1: A, x2: A) extends List[A]
```

---
- Base case: `case class List0[A]()`
- Inductive step: List(n) = (List(n-1), A)

```scala
sealed trait ListI[A]
final case class List0[A]() extends ListI[A]
final case class List1[A](prev: List0[A], x: A) extends ListI[A]
final case class List2[A](prev: List1[A], x: A) extends ListI[A]
```

```scala
sealed trait ListI[A]
final case class List0[A]()
final case class ListN[A](prev: ListI[A], x:A)
```


`ListI`가 empty인 경우의 type은 `List0()`에 해당하며, 한 개인 경우는 `ListN(List0(), x)`, 두 개인 경우는 
`ListN(ListN(List0(), x), y)이다. 이를 pattern-matching code로 구현한 함수 'headOption`을 작성하면 다음과 같다. 

```scala
@tailrec def headOption[A]: List[A] => Option[A] = {
  case List0() => None
  case ListN(List0(), x) => Some(x)
  case ListN(prev, _) => headOption(prev)
}
```

Scala library에서는 아래와 같이 구현되어있다.
```scala
sealed trait List[A]
final case class Nil extends List[Nothing]
final case class ::[A](head: A, tail: List[A]) extends List[A]
```


## 3.3.2 Tail recursion with `List`

`List` type은 귀납적 유도에 의해 정의되었으므로 `List`를 재귀적 용법을 사용하여 연산하는 것은 간단하다. 예로 아래는 `map` 함수이다.

```scala
def map[A, B](xs: List[A])(f: A => B): List[B] = xs match {
  case Nil => Nil
  case head :: tail => f(head) :: map(tail)(f)
}
```

- `map` 함수는 tail recursion 이 아니기 때문에 큰 list를 처리하는 데 문제가 발생할 수 있다. 


```scala
@tailrec 
def foldLeft[A, B](xs: List[A])(init: R)(f: (R, A) => R): R = 
  xs match {
    case Nil => init
    case head :: tail => 
      val newInit = f(init, head)
      foldLeft(tail)(newInt)(f)
  }

```

- 이에 반해 `foldLeft`는 case branch 가장 마지막에서 `foldLeft`를 호출하므로, tail recursion에 해당한다. 


```scala
def reverse[A](xs: List[A]): List[A] = 
  xs.foldLeft(Nil: List[A])((prev, x) => x :: prev)
  
def map[A, B](xs: List[A])(f: A => B): List[B] = 
  xs.foldLeft(Nil: List[B])((prev, x) => f(x) :: prev).reverse
```

-  이처럼 위에서 구현한 `foldLeft`를 활용하면 `map`을 tail recursion 방식으로 처리할 수 있다. 
