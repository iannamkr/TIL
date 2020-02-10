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
