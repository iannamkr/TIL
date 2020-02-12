# 4 The logic of tpyes. II. Curried functions

## 4.1 Functions that return functions

### 4.1.1 Motivation and first examples

특정 message를 출력하는 logger function을 구현한다고 할때, 간단한 logger function (`String => Unit`)은 아래와 같다.

```scala
val logger: String => Unit = { message => println(s"Info: $message") } 
logger("hello world")

INFO: hello world
```

이 함수는 message 와 상관없이 "INFO" prefix를 같는다. 이를 개선하려면 `prefix`를 인자로 받아 `prefix`를 포함하고 있는 새로운 `logger`를 리턴하는 것이다. 

즉 `logWith` 함수는 새로운 `logger`함수를 리턴해야하고, 해당 함수의 타입은 `String => Unit` 이다.

```scala
def logWith(prefix: String): (String => unit) = {
  message => println(s"$prefix: $message")
}
```

위처럼 `logWith`는 무명함수(nameless function)인 `message => println(...)`을 포함하고 있으며, type은 `String => Unit`이다. 

```scala
val info = logWith("INFO")
info: String => Unit = <function1>

val warn = logWith("WARN")
info: String => Unit = <function1>

info("hello")
INFO: hello

warn("goodbye")
WARN: goodbye
```

`warn` 함수는 항상 "warn" prefix를 갖는 message를 출력하며 그 이후 prefix는 절대 변경되지 않는다.
즉 `logWith`에 의해 생성된 `info`, `warn` 함수 `prefix` 값은 생성되는 시점에 결정(backed into) 된다. prefix는 무명함수 내의 local 상수로 취급되고, `logWith`에 의해 return 되기 때문이다.
 
`logWith(prefix)`에 의해 새로운 함수가 생성할때마다 전달된 `prefix` 값은 생성된 함수 내에 저장된다. 함수 body에 사용되는 값은 외부영역으로부터 독립된 복사본으로 존재하게되며, 이는 무명함수의 일반적인 특정으로 볼 수 있다. 

이를 함수의 body가 외부 유효범위(outer scope)로부터 닫혀있다(closes over)고 표현하며, 이러한 함수를 **closures** 라고 부르기도 한다. 무명함수가 외부 범위의 값을 "캡처"한다고 표현하는 것이 더 명확하다.
 
```scala
val f: (Int => Int) = {
  val p = 10
  val q = 20
  x => p + q * x
}
```
함수 `f`의 body는 `{ x => 10 + 20 * x }` 와 동일하다. 

---

### 4.1.2 Curried and uncurried function

```scala
logWith("INFO")("hello")
INFO: hello
```

`logWith("INFO")("hello")` 는 두 개의 인자를 받는 함수`logWith`로 보일 수 있지만, `logWith`는 String 타입의 단일 인자를 받는 함수로 정의되어있다. 이것은 모순이 아니다. 왜냐하면 `logWith("INFO")`는 추가적인 전달인자를 받는 함수를 리턴하기 때문에 `logWith("INFO")`, `logWith("INFO")("hello")` 처럼 한번에 하나씩 전달인자를 사용하는 것이 가능하다.

이처럼 인수에 함수를 적용할 수 있는 방법을 **curried** 함수라고 한다. 
`curried` 함수가 한번에 하나에 적용될 수 있는 반면 **uncurried** 함수는 한번에 모든 전달인자를 적용해야한다.

```scala
def prefixLog(prefix: String, meesage: String): Unit = println(s"$prefix: $message")
```

curried 함수인 `logWith`의 타입은 `String => (String => Unit)`이다. Scala 문법상 함수를 표기하는 화살표(=>)는 오른쪽에 그룹화되므로, 
`String => String => Unit`과 동일하다. 
> (String => String) => Unit 과는 엄연히 다르다. String => String 타입을 전달인자로 받아 Unit을 리턴한다는 의미이기 때문이다. 

일반적으로 curried 함수는 새로운 함수를 리턴하기 전까지 하나의 전달인자를 받아 다른 함수를 리턴하고, 다시 하나의 인자를 받아 또 다른 함수를 리턴하기를 반복하는 구조를 갖는다. 

따라서 curried 함수의 타입 시그니쳐는 `A => B => C ... => R => S`와 같으며, 이때 `A, B, ... ,R`을 **curried argunemt**라고 말하며 `S`가 최종 결과 타입이다. 

예를 들어 타입 표현식이 `A => B => C => D` 이고 A, B, C 타입 모두 curried argument라고 할때, D는 최종 결과 타입이다. 

Scala 에서 여러 전달인자 그룹(전달인자가 여러 개의 괄호쌍으로 표현됨)을 받는 함수는 curried function 으로 볼 수 있다.

```scala
def map[A, B](xs: Seq[A])(f: A = > B): Seq[B]
def fmap[A, B](f: A => B)(xs: Option[A]): Option[B]
def foldLeft[A, R](xs: Seq[A])(init: R)(update: (R, A) => R): R
```
위 함수의 타입 시그니쳐로 재작성된 함수는 아래와 같다.

```scala
def map[A, B]: Seq[A] => (A => B) => Seq[B]
def fmap[A, B]: A => B => Option[A] => Option[B]
def foldLeft[A, R]: Seq[A] => R => ((R, A) => R) => R
```
> (A => B) 처럼 함수타입의 curried argument는 중괄호가 필요하다.

요약하면 `logWith`와 같은 curried 함수는 아래와 같이 세 가지 방법으로 표현할 수 있다. 모두 동일하다.
```scala
def logWith(prefix: String)(message: String): Unit = println(s"$prefix: $message")
def logWith2(prefix: String): String => Unit = { message => println(s"$prefix: $message") }
def logWith3: String => String => Unit => { prefix => message => println(s"$prefix: $message") }
```
> 코드의 가독성을 위해 무명함수는 다음처럼 중괄호를 사용하여 작성하는 것이 좋다.  `x => y => expr === { x => { y => expr } }`

---

### 4.1.3 Equivalence of curried and uncurried functions

타입 시그니쳐가 `Int => Int => Int`인 curried 함수가 있을 때, 이 함수는 `integer` 타입을 전달인자로 받아, `integer` 타입을 인자로 하는 uncurried 함수를 리턴한다.

```scala
def f1(x: Int): Int => Int = { y => x - y }
```

위 함수는 x 값을 받아 `y => x - y` 을 리턴하며, 그 함수 타입은 `Int => Int` 이다. 이 함수는 아래와 동일하게 작성 가능하다.

```scala
val f1: Int => Int => Int = { x => y => x - y }
```

이를 두 개의 전달인자를 받는 함수 f2와 비교해보자.
```scala
def f2(x: Int, y: Int): Int = x - y
```

```scala
f1(20)(4)
res0: Int = 16

val r1 = f1(20)
r1: Int => Int = <function1>
r1(4)
res2: Int = 16

f2(20, 4)
res3: Int = 16
```
`f2`는 반드시 두 개의 인자를 모두 필요로하지만, `f1`은 하나씩 인자를 적용할 수 있다.  

이처럼 가능한 모든 인자를 적용하지 않고 일부만 적용한 curried 함수를 **partial application** 이라고 한다. 
반대로 가능한 모든 인자를 적용한 curried 함수를 **saturated application** 이라고 한다. 

curried function
- partial application
- saturated application

```scala
val r2: Int => Int = f2(20, _)
r2: Int => Int = <function1>
```

- 함수 `r2`는 첫 번째 인자만 부분적용하였다. 
- 반면에 `r2`는 `r1`과 동일한 함수이다. `r2`의 리턴 값은 r1와 동일하기 때문이다. `Int => Int`

좀 더 직관적인 partial application 문법은 아래와 같다.

```scala
val r3: Int => Int = { x => f2(20, x) }
r3: Int => Int = <function1>
```

---

위에서 살펴본 함수 `f1`, `f2`는 정의는 다르나 연산 결과는 동일하다. 

```scala
val f1: Int => Int => Int = { x => y => x - y }
def f2(x: Int, y: Int): Int = x - y

def f2new(x: Int, y: Int): Int = f1(x)(y)
def f1new: Int => Int => Int = { x => y => f2(x, y) }
```
- `f1new`, `f2new` 두 함수는 연산 결과는 동일하나 엄밀하게 같은 함수는 아니다. 

`A => B => C => ... => R => S`와 같은 타입 시그니쳐를 갖는 curried 함수는 
`(A, B, C, ... , R) => S` 타입 시그니쳐를 갖는 uncurried 함수와 동일하게 동작한다.  

다만 curried 함수는 한 번에 하나씩 인자를 처리하는 반면 uncurried 함수는 모든 인자를 받아 처리한다. 

Scala 에서는 curried <=> uncurried 간의 변환을 위한 함수를 제공한다.

```scala
val f1c = (f2 _).curried
f1c: Int => (Int => Int) = <function1>

val f2u = Function.uncurried(f1c)
f2u: (Int, Int) => Int = <function2>
```
