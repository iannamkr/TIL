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


## 3.3.3 Binary trees

recursive disjuntive type 인 4개의 tree (binary tree, rose tree, regular-shaped tree, abstract syntax tree)에 대해 알아보고자 한다. 


```scala
sealed trait Tree2[A]
final case class Leaf[A](a: A) extends Tree2[A]
final case class Branch[A](x: Tree2[A], y: Tree2[A]) extends Tree2[A]
```

아래는 위에서 정의한 `Tree2`의 예시이다.

![image](https://user-images.githubusercontent.com/13671946/74150752-ea944600-4c4d-11ea-879a-fb26696c0df2.png)


```scala
Branch(Branch(Leaf("a1"), Leaf("a2")), Leaf("a3"))
Branch(Branch(Leaf("a1"), Branch(Leaf("a2"), Leaf("a3"))), Branch(Leaf("a4"), Leaf("a5")))
```


`Tree2` type에 대한 `foldLeft`는 아래와 같다.

```scala
def foldLeft[A, B](t: Tree2[A])(init: R)(f: (R, A) => R): R = t match {
  case Leaf(a) => f(init, a)
  case Branch(t1, t2) => 
    val r1 = foldLeft(t1)(init)(f)
    foldLeft(t2)(r1)(f)
}

val t: Tree2[String] = Branch(Branch(Leaf("a1"), Leaf("a2")), Leaf("a3"))
foldLeft(t)("")(_ + " " + _)

res0: String = " a1 a2 a3"
```

이때 `foldLeft`는 왼쪽/오른쪽 branch에 각각 호출되어야하므로 accumulator를 통한 tail-recursive를 구현할 수 없다.

