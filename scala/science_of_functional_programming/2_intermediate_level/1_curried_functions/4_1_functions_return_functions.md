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
 
`logWith(prefix)`에 의해 새로운 함수가 생성할때마다 전달된 `prefix` 값은 생성된 함수 내에 저장된다. 함수 body에 사용되는 값은 외부영역으로부터 독립된 복사본으로 존재하게되며, 이는 무명함수의 일반적인 특정으로 볼 수 있다. 이를 함수의 body는 외부 유효범위(outer scope)로부터 닫혀있다(closes over)고 표현하기도하며, 이러한 함수를 **closures** 라고 부르기도 한다. 
 
무명함수는 외부 유효범위(outer scope)으로부터 값을 획득(capture)한다. 
 
```scala
val f: (Int => Int) = {
  val p = 10
  val q = 20
  x => p + q * x
}
```

함수 `f`의 body는 `{ x => 10 + 20 * x }` 와 동일하다. 
