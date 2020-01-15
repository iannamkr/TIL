# 2.2 Converting a sequence into a single value

- `.map`, `.zip`을 활용하여 다양한 sequence 문제를 해결할 수 있으나 sequence 의 총 합을 구하는 문제는 해결할 수 없다. 
(`.map`, `.zip`, `.filter`와 같은 함수는 sequence로부터 새로운 sequence를 연산한다.)

- Scala에서는 sequence으로부터 single value 를 계산하는 `.count`, `.length`, `.max` 와 같은 함수를 제공하지만 
이 함수들을 활용해도 `.sum`을 구현할 수 없다.

- `.map`, `sum` 등으로 해결할 수 없는 또 다른 문제로는 부동소수점연산이다.

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


