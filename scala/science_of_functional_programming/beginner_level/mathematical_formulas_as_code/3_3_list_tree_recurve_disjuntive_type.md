# Lists and trees: recursive disjunctive types 

## Lists

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




