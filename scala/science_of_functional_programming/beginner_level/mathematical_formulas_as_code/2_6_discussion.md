# 2.6 Discussion

## 2.6.1 Total and partial functions

- Scala에서 함수는 전함수(total) 또는 부분정의함수(partial)이다. 
- 전함수 항상 결과를 연산하는 반면 부분정의함수는 특정 값에 따라 연산이 실패할 수 있다.

아래는 대표적인 부분정의함수인 `.max`이다.

```scala
scala> Seq(1).tail
res0: Seq[Int] = List()
scala> res0.max
java.lang.UnsupportedOperationException: empty.max
  at scala.collection.TraversableOnce$class.max(TraversableOnce.scala:229)
  at scala.collection.AbstractTraversable.max(Traversable.scala:104)
  ... 32 elided
```

- `.max`함수는 non-empty sequence의 경우에만 동작하며, 빈 sequence에 대해서는 exception을 뱉는다.
- 부적절한 input에 의한 runtime error는 치명적이나, 항상 발생할 가능성이 있다. 


```scala
def h(p: (Int, Int)): Int = p match { case (x, 0) => x }

scala> h( (1,0) )
res0: Int = 1

scala> h( (1,2) )
  scala.MatchError: (1,2) (of class scala.Tuple2$mcII$sp)
    at .h(<console>:12)
    ... 32 elided
```
- 위와 같은 pattern matching expression의 경우 tuple의 두 번째 값이 0 인 경우에만 동작하므로 부분정의함수이다. 
- 가능한 functional programing 에서는 위와 같은 match error를 만들지 않도록 코드가 작성되야한다.

```scala
p match {
  case (x, 0) => ...    // matches (x, y) patterns
  case _      => ...    // matches everything
}
```
- Pattern matching은 tuple의 사이즈가 정확하다면, 즉 Pattern이 같다면 infallible (never fails at run time) 해야한다.
( 만약 (x, y, z)와 같은 pattern에 대한 matching 을 수행하는 경우, tuple에 대한 크기만 정확하다면 절대 실패할 수 없다. )
- Pattern matching 은 "match-all"을 의미하는 `_` pattern을 구현함으로써 infallible하게 만들 수 있다.

## 2.6.2 Scope and shadowing of pattern matching variables
- Pattern matching 은 변수들이 항상 pattern matching expression의 우측에 정의되는, locally scoped variable을 사용한다. 
아래의 예시를 보자.

```scala
def f(x: (Int, Int)): Int = x match { case (x, y) => x + y }
scala> f( (2,4) )

res0: Int = 6
```

- Scope: 함수 f의 전달인자 x의 타입은 tuple인데 반해 case문 안의 변수 x는 이름은 같지만 타입은 Int이다.
Int 타입 변수 x는 x + y expression 안에 locally scoped 된 변수이다. 엄연히 서로 다르다. 

- Shadowing: 위와 반대로 tuple 타입 변수 x는 case expression 안에서는 invisible하다. 
Tuple 타입 변수 x는 Int 타입 변수 x에 의해 shadow 되어졌다고 표현한다.

따라서 이러한 문제(?)를 회피하는 방법은 서로 다른 을 사용하는 것이다.

```scala
def f(x: (Int, Int)): Int = x match { case (a, b) => a + b }

scala> f( (2,4) )
res0: Int = 6
```


## 2.6.3 Lazy values and sequences. Iterators and streams

사전에 sequence의 길이를 알 수 없는 경우, 새로운 sequence를 생성하기 위해 streams를 사용한다. 

```scala
scala> val p = Stream.iterate(1)(_ + 1)
p: Stream[Int] = Stream(1, ?)
```

이 경우 위 stream에 대한 종료 조건이 정의되지 않았으므로, 이 streams는 infinite sequence이다. 
다만 엄밀하게는 infinite라기 보다는 부분적으로 연산되어진다(partially computed) 라고 표현하는 것이 적절하다. 

array와 stream의 가장 중요한 차이점은 stream의 element는 처음에 모두 연산되는 것이 아닌 필요에 따라 계산되어진다는 것이고 
반면에 array는 모두 사전에 계산되어 즉시 사용될 수 있다. 

![image](https://user-images.githubusercontent.com/13671946/73759174-e92acf80-47ae-11ea-9b81-fae762245d07.png)

- `lazy value`는 다른 expression 에서 사용되는 시점에 연산이 된다. scala에서는 `lazy val` 키워드로 선언할 수 있다. 
- 한번 연산이 된 이후에는 `lazy value`는 memory에 상주하며 재연산되지 않는다. 

- `on-call value`는 매번 사용될때마다 재연산을 한다. scala에서는 `def` 로 선언된다.
- scala의 모든 collection type (Lists, Array, Set, Map ... )은 eager이다. 또한 eager collection의 모든 element 역시 사전에 연산된다. 

- `stream`은 `lazy collection`이다. `stream`의 모든 element들은 최초로 필요한 시점에 연산되며, 그 이후에는 memory에 상주하여 재연산되지 않는다.

### `.view` 함수
- `.view`함수를 사용하여 Eager collection 을 `iterator`로 변환할 수 있다. 
- `iterator`는 collection들과 유사하게 동작하고 `.map`, `.filter`의 기능도 쓸수있다. 
- 다만 차이점은 `iterator`의 경우 이전 연산된 값을 memory에 저장하지 않는다. 

```scala
scala> (1 to 10).flatMap(x => 1 to 3000000).max
java.lang.OutOfMemoryError: GC overhead limit exceeded

scala> (1 to 10).view.flatMap(x => 1 to 3000000).max
res0: Int = 3000000
```

위 첫 번째 예제와 같이 `.flatMap` 연산은 임시 collection을 생성하고 이는 memory를 많이 소모한다. 
`.view` 함수는 collection을 `iterator`로 변환해주므로 메모리 제약 조건을 제외하고 동작 방식은 stream와 유사하다. 

### `Iterator` 클래스

- Scala library 는 `Iterator` 클래스를 제공하며, `Stream`과 유사하게 동작하는 여러 함수를 지원한다.
- **그러나 Iterator는 수학적인 맥락에서 `value`로 취급될 수 없다.**

```scala
scala> val iter = (1 until 10).toIterator
iter: Iterator[Int] = non-empty iterator

scala> iter.toList // Look at the elements of ‘iter‘.
res0: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

scala> iter.toList // Look at those elements again...??
res1: List[Int] = List()

scala> iter
res2: Iterator[Int] = empty iterator
```

- 위 수행결과에서 보다시피 `iter.toList` 는 서로 다른 결과를 도출한다. 두 번째 수행 시에는 빈 List를 return 한다.
- 수학의 영역에서는 얼만큼 반복해서 재연산을 하더라도 동일한 input에 대해서 항상 결과는 동일해야한다. 
- 이를 참조투명성(**referential transparency**) 또는 순수함수(**functional purity**)라고 표현한다.  
- 또한 f(x) 에서 x는 함수가 연산된 이후에도 그 값은 x로 항상 동일하다. 이러한 처리 방식을 **value semantics**이라 한다. identity가 아닌 값 자체에 의미를 두는 것이다.

> In computer science, having value semantics (also value-type semantics or copy-by-value semantics) means for an object that only its value counts, not its identity.[1][2] If the concept is fully applied, value semantics implies immutability of the object.[3]

- Scala의 `Iterator`는 `mutable`하기 때문에 겉으로는 문제 없어 보이는 코드이지만 수학적 규칙에 위배된, 잘못된 코드를 작성하기 쉽다. 
```scala
scala> val s = (1 until 10).toIterator
s: Iterator[Int] = non-empty iterator

scala> val t = s.zip(s).toList
java.util.NoSuchElementException: next on empty iterator
```

- Scala에서는 `.toStream` 을 통해 `Stream`으로 변환할 수 있고 이는 **value semantics**를 준수하므로 위와 같은 오류를 해결할 수 있다.
- `Iterator` 대신 `Stream` 이나 `.view` 함수를 통해 필요에 따라 lazy 또는 on-call collection을 구현할 수 있다. 

```scala
scala> val iter = (1 until 10).toIterator
iter: Iterator[Int] = non-empty iterator

scala> val str = iter.toStream
str: Stream[Int] = Stream(1, ?)

scala> str.toList
res0: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

scala> str.toList
res1: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

scala> str.zip(str).toList
res2: List[(Int, Int)] = List((1,1), (2,2), (3,3), (4,4), (5,5), (6,6), (7,7), (8,8), (9,9))
```


