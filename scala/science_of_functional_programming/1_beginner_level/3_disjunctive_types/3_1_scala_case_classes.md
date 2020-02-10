# 3.1 Scala's case classes

## 3.1.1 Tuple type with names

만약 동일한 type의 tuple이 프로그램 여기저기서 사용되는 경우 어떤 문제가 발생할까? 실수로 programmer가 값을 잘못 사용할 수 있다. 
또한 이렇게 발생한 문제의 원인을 찾는 것도 매우 어렵다.

위 문제를 해결하기 위해 Scala에서는 type alias를 통해 tuple에 이름을 부여할 수 있다.

```scala
type MySockTuple = (Double, String)
type PaymentTuple = (Double, String)

val s: MySockTuple = (10.5, "white")
val p: PaymentTuple = (25.0, "restaurant")
```

Scala에서의 `case class`는 이름을 갖는 tuple이라고 볼 수 있다. `case class`는 편리하고 에러를 방지할 수 있는 여러 기능을 제공한다. 

```scala
case class Person(firstName: String, lastName: String, age: Int)

val noether = Person("Emmy", "Noether", 137)
noether.firstName
noether.age

val p = Person(140, "Einstein", "Albert")
<console>:13: error: type mismatch;
```

`case class`는 다른 `case class` 타입을 가질 수 있다.

```scala
case class BagOfSocks(sock: MySock, count: Int)
val bag = BagOfSocks(MySock(10.5, "white"), 6)

bag.sock.size
// 10.5
```

## 3.1.2 Case classes with type parameters

`Type class`는 type parameter들로 정의된다. 예를 들어 `MySock` class에 `size`, `color`를 추가하여 확장된 `MySock` class를 만든다고 할때 
아래와 같이 정의할 수 있다.

```scala
case class MySock_Int(size: Double, color: String, value: Int)
case class MySock_Boolean(size: Double, color: String, value: Boolean)
```

그러나 아래처럼 매개변수화클래스(parameterized class)를 정의하는 것이 더 효율적이다.
```scala
case class MySockX[A](size: Double, color: String, value: A)

val s = MySockX(10.5, "white", 123)
```

위 예제에서 `123`은 `Int` type이기 때문에 `MySockX[A]`는 자동적으로 `Int` type에 setting 된다. 다시 말해 `Int` type 뿐만 아니라
MySockX[String] 등 다양한 타입을 정의할 수 있다.

만약 `MySockX[A]`를 parameter로 사용하는 경우, 아래와 같이 정의한다.

```scala
def fitsMe[A](sock: MySockX[A]): Boolean = (sock.size >= 10.5 && sock.size <= 11.0)

fitsMe(MySockX(10.5, "blue", List(1, 2, 3)) // Type parameter A = List[Int].
```

물론 `Case class`는 여러 개의 type parameter를 가질 수 있다 .

```scala
case class Complicated[A, B, C, D](x: (A, A), y: (B, Int) => A, z: C => C)
```

이처럼 `type parameter`를 포함하고 있는 type을 `type constructor`라고 한다. `type constructor`는 
`MySockX`, `Complicated`와 같은 새로운 type을 주어진 `type parameter`를 통해 생성한다. 


## 3.1.3 Tuples with one part and with zero parts

아래는 tuple과 case class를 예제를 통해 비교한 표이다.

![image](https://user-images.githubusercontent.com/13671946/74024551-7d7e7780-49e5-11ea-908f-537a17175cb7.png)

Scala에서는 빈 `case class`를 위한 추가적인 문법을 제공한다.
```scala
case object C
```

`case class C()`와 `case object C`의 차이점은 다음과 같다.

- `case object`는 `type parameter`를 가질 수 없는 반면 `case class`는 가질 수 있다. 
```scala
case class C[X, Y, Z]()
```

- `case object`는 딱 한번 memory에 할당되는 반면 `case class C()`는 `C()`가 연산될때마다 새롭게 생성된다.  


## 3.1.4 Pattern matching for case classes

Scala에서는 다음의 두 경우에 pattern matching을 수행한다.

- 정의를 분해하는 경우: _val pattern = ..._
- `case` 표현식을 사용하는 경우: _case pattern => ..._

```scala
case class MySock(size: Double, color: String)
case class BackOfSocks(sock: MyCock, count: Int)

def printBag(bag: BagOfSocks): String = {
  val BackOfSocks(MySock(size, color), count) = bag // De-structure the ‘bag‘.
  s"bag has $count socks of size $size"
}

def fitsMe(bag: BagOfSocks): Boolean = bag match {
  case BagOfSocks(MySock(size, _), _) => (size >= 10.5 && size <= 11.0)
}

val bag = BagOfSocks(MySock(10.5, "white"), 6)
printBag(bag)

res0: String = bag has 6 white socks of size 10.5
```



