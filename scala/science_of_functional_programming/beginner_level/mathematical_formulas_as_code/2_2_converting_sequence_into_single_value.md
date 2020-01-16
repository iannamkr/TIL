# 2.2 Converting a sequence into a single value

- `.map`, `.zip`을 활용하여 다양한 sequence 문제를 해결할 수 있으나 sequence 의 총 합을 구하는 문제는 해결할 수 없다. 
(`.map`, `.zip`, `.filter`와 같은 함수는 sequence로부터 새로운 sequence를 연산한다.)

- Scala에서는 sequence으로부터 single value 를 계산하는 `.count`, `.length`, `.max` 와 같은 함수를 제공하지만 
이 함수들을 활용해도 `.sum`을 구현할 수 없다.

- `.map`, `sum` 등으로 해결할 수 없는 또 다른 문제로는 부동소수점 연산이 있다.

```scala
def digitsToDouble(ds: Seq[Char]): Double = ???

scala> digitsToDouble(Seq(’2’, ’0’, ’4’, ’.’, ’5’))
res0: Double = 204.5

def digitsToInt(ds: Seq[Int]): Int = {
  val n = ds.length
  // Compute a sequence of powers of 10, e.g. [1000, 100, 100, 1].
  val powers: Seq[Int] = (0 to n - 1).map(k => math.pow(10, n - 1 - k).toInt)
  // Sum the powers of 10 with coefficients from ‘ds‘.
  (ds zip powers).map { case (d, p) => d * p }.sum
}
scala> digitsToInt(Seq(2,4,0,5))
res0: Int = 2405
```

### 2.2.1 집계 함수의 귀납정의 (Inductive definitions of aggregation functions)

수학적 귀납법은 이전 값을 통해 다음 값을 계산하기 위해 사용되는 방법 중 하나이다. 
수학적 귀납법을 통해 일련의 sequence로부터 single value를 연산하는 함수를 정의하기 위해서는 아래 2 단계를 구현해야한다.

1. 귀납의 기본 (The base case of the induction)
- 전달인자의 값이 empty sequence 인 경우, 함수 f가 어떤 값을 리턴해야하는지 정의해야 한다.
만약 함수 f 가 non-empty sequence에만 동작 한다면, 단일 element를 갖는 sequence `Seq(x)` 에 대한 리턴 값을 명시해야한다. 

2. 귀납적 증명 (The inductive step)
- 귀납적 가정(induction hypothesis)에 의해 함수 f 가 임의의 sequence xs에 대해 이미 계산을 했다면, 
xs에 x 가 추가된 하나 이상의 sequence xs (`xs ++ Seq(x)`) 에 대해 함수 f는 어떻게 동작할 것인가?
이를 위해 우리는 이미 f(xs)의 값이 계산되어졌다는 가정으로 f(xs ++ Seq(x))를 어떻게 연산할 것인지 구체화해야한다. 

위 두 연산이 가능해야 임의의 sequence에 따라 함수 f가 정의될 수 있다. 이것이 귀납이 수학에서 동작하는 방식이며, FP에서도 같은 원리로 동작한다.

> 귀납법의 방법은 증명해야 할 두 경우를 요구합니다. 기본 경우(base case) (또는, 때때로 기초(basis))라고 불리는, 첫 번째 경우는 속성이 숫자 0에 대해 유지된다는 것을 증명합니다. 귀납법 단계(induction step)라고 불리는, 두 번째 경우는, 만약 속성이 하나의 자연수 n에 대해 유지되면, 그것은 다음 자연수 n + 1에 대해 유지됨을 증명합니다. 이런한 두 단계는 모든 자연수 n = 0, 1, 2, 3, ...에 대해 속성 P(n)을 확증합니다. 기본 단계는 영으로 시작할 필요가 없습니다. 흔히 그것은 숫자 일로 시작하고, 그리고 그것이 임의의 자연수로 시작하여, 시작 번호보다 크거나 같은 모든 자연수에 대한 속성의 참을 확증할 수 있습니다. -https://dawoum.ddns.net/wiki/Mathematical_induction

#### `.sum`

1. _For an empty sequence..._
- empty sequence 인 경우 총 합은 0이다. `Seq().sum == 0`

2. _If the result is already known..._
- sequence xs에 대한 결과를 알고 있다면, element x 가 추가된 xs의 값은 `xs.sum + x` 와 동일할 것이다.

따라서, 이를 code로 작성하면 `(xs ++ Seq(x)).sum == xs.sum + x` 이다.


#### `digitsToInt` 

1. _For an empty sequence..._
- empty sequence 인 경우 값은 0이다.

2. _If the result `digitsToInt(xs)` already known..._
- `digitsToInt(xs)` 의 결과 값을 미리 알고 있다면 아래 식을 만족한다.
```scala
digitsToInt(xs ++ Seq(x)) == digitsToInt(xs) * 10 + x
```

### `.length`
1. _For an empty sequence..._
- empty sequence 인 경우 값은 0이다.

2. _If the result `xs.length` already known..._
```scala
(xs ++ Seq(x)).length == xs.length + 1
```


### `.max`
1. _For a one-element sequence..._
- Seq(x).max == x

2. _If the result `xs.max` already known..._
```scala
(xs ++ Seq(x)).max = math.max(xs.max, x)
```


### `.count`
1. _For an empty sequence..._
- Seq().count(p) == 0

2. _If the result `xs.count` already known..._
```scala
(xs ++ Seq(x)).count(p) == xs.count(p) + c
```

위와 같은 수학적 귀납정의를 code로 작성하는 데는 두 가지 방법이 있다. 첫 번째는 재귀함수를 작성하는 것이다. 
두 번째는 `foldLeft`, `reduce` 같은 standard library function을 사용하는 것이다. 

대부분의 경우는 standard library function을 사용하는 것이 좋지만 때때로 명시적 재귀함수를 사용하는 것이 더 명료할때도 있다. 


### 2.2.2 재귀를 통한 함수의 구현 (Implementing functions by recursion)

- 수학적 귀납을 코드로 작성할때 가장 먼저 판단해야할 것은 귀납에서의 `base case`와 `induction step` 조건을 찾는 것이다. 

```scala
def sum(s: Seq[Int]): Int = if (s == Seq()) 0 else {
   val x = s.head 
   val xs = s.tail 
   sum(xs) + x 
}
```

위 예제에서는 if/else를 통해 `base case`와 `induction step`을 분리하였다. 

1. _base case
```scala
Seq() == 0
```

2. `inductive step`
```scala
(xs ++ Seq(x)).sum == xs.sum + x
```
해설) sum 연산이서는 더하는 순서는 중요하지 않다. 그러나 연산의 순서는 중요할 수 있다. 따라서 `inductive step`에서는 `s = Seq(s) ++ xs` 또는 
`s = xs ++ Seq(x)` 로 나눌 지 선택해야한다. 

위와 같은 재귀적 접근 방식은 sequence가 충분히 큰 경우 자칫 "stack overflow" 문제를 발생시킬 수 있다. 다음 장에서는 "tail recursion"을 통해
"stack overflow" 문제를 해결하는 방법을 설명할 것이다. 

두 번째 문제는 재귀적으로 호출되는 함수에 `base case`를 확인하는 코드와 sequence s를 subsequnce xs와 element x로 분리하는 코드가 계속적으로 반복하는 것이다. 이러한 반복적인 코드는 scala library function을 사용하여 대체할 수 있다. 


### 2.2.3 Tail recursion
다음은 위에서 언급한 "stack overflow"를 발생하는 재귀함수의 예이다.
```scala
def lengthS(s: Seq[Int]): Int =
    if (s == Seq()) 0
    else 1 + lengthS(s.tail)

scala> lengthS((1 to 1000).toList)
res0: Int = 1000

scala> val s = (1 to 100000).toList
s: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22,
    23, 24, 25, 26, 27, 28, 29, 30, 31, 32, ...

scala> lengthS(s)
java.lang.StackOverflowError
  at .lengthS(<console>:12)
  at .lengthS(<console>:12)
  at .lengthS(<console>:12)
  at .lengthS(<console>:12)
...
```
```
lengthS(Seq(1, 2, ..., 100000))
= 1 + lengthS(Seq(2, ..., 100000))
= 1 + (1 + lengthS(Seq(3, ..., 100000)))
= ...
```

위와 같은 "stack overflow" 문제를 회피하는 방법은 `tail recursion` 트릭을 사용하는 것이다. `tail recursion`을 사용한다는 의미는
모든 재귀 호출이 함수의 끝 위치에서 발생하도록 코드를 다시 작성하는 것이다. 다시 말해서 각 재귀호출은 그 자체가 마지막 연산이어야한다는 것이다. 

```scala
def lengthT(s: Seq[Int], res: Int): Int =
    if (s == Seq())
        res
    else
        lengthT(s.tail, 1 + res)
```
해설) 위 코드에서는 모든 branch가 항상 추가적인 재귀호출 없이 고정적인 값을 리턴한다. 
재귀호출에서 `lengthT`함수가 `1 + res`와 같은 sub-expression을 갖는 것은 문제가 되지 않는다. 왜냐하면 모든 sub-expression은 `lengthT`함수가 재귀적으로 호출되기 이전에 계산되기 때문이다. 

**tail-recursion 에서는 수 많은 if/else, match/case branch가 존재할 수 있다. 그러나 모든 재귀호출은 항상 마지막으로 반환된 식이어야 한다.** scala compiler에서는 이를 자동으로 확인해주는 annotation인 `@tailrec`을 제공한다.

```scala
@tailrec def lengthT(s: Seq[Int], res: Int): Int = 
    if (s == Seq()) res
    else lengthT(s.tail, 1 + res)
```
```
lengthT(Seq(1,2,3), 0)
= lengthT(Seq(2,3), 1 + 0) // = lengthT(Seq(2,3), 1)
= lengthT(Seq(3), 1 + 1) // = lengthT(Seq(3), 2)
= lengthT(Seq(), 1 + 2) // = lengthT(Seq(), 3)
= 3
```
해설) 
