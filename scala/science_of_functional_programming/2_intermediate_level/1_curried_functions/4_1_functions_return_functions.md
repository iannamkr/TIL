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

