# 1.7 Discussion

### 1.7.1 함수 지향 프로그래밍 패러다임(Functional programming as a paradigm)
- 함수형 프로그래밍은 프로그래머가 코드를 작성함에 있어 선택 가능한 다양한 방식 중의 하나이다.
- FP(Functional Programming)에서 가장 중요한 기본 원칙은 코드를 수학 표현식(a mathematical expression or formula)으로 작성하는 것이다.
- FP는 개발자로 하여금 추측(guessing)이 아닌 수학자/과학자와 같이 체계적이고 논리적인 추론(logical reasoning)을 통해 코드를 작성하도록 한다.
- 오랜시간 FP 커뮤니티에서는 여러 프로그래밍 문제를 해결하기 위해 함수형 도구(e.g .map, .filter ...)들을 만들어왔으며, 그 결과 실제 세계의 다양한 문제를 해결하는데 유용함을 입증하였다. 

### 1.7.2 함수형 프로그래밍 언어 (Functional programming languages) 
- FP는 프로그래밍 방식으로써 모든 언어를 통해 적용할 수 있다. 그러나 몇몇 언어는 완벽한 FP 기술을 적용하는데 한계가 있다.
- Python, Ruby의 경우 map/reduce operation 등 제한적인 기능만 제공하며, 이외의 추가적인 FP기능을 사용하기 위해서는 많은 코드가 필요하다. 또한 이 역시 error 없이 작성하는 것은 매우 힘들다. 
- Haskell, Ocamel의 경우 설계에서부터 진보된 FP를 제공하기 위해 개발된 언어이다. 
- ML, F#, Scala, Swift, Elm, PureScript의 경우 Haskell, Ocamel과는 다른 목표를 지향하지만 FP 프로그래밍을 하는 데에는 충분한 기능을 제공한다.  

### 1.7.3 'variable' 의 수학적 의미 
- FP에서의 variable은 수학에서 사용되는 변수와 매우 유사하다. 

- in mathematics 
  - 수학에서의 변수는 함수의 전달인자로 사용된다. 
  - 수학에서의 변수는 수식 내에서 값이 변경되지 않는다는 불변성의 규칙이 있다. 무명함수의 전달인자 역시 불변하다.
  - 수학에서의 변수는 정의된 수식(expression) 안에서 이름상수(named constant)로 취급된다.
  - 함수의 전달인자 x의 값에 따라 함수 f의 결과는 바뀔 수 있으나, 전달된 x의 값은 함수 f가 처리되는 동안에는 변경되지 않는다.
  - 수식 ```x^2 + x``` 에는 x의 값이 정수인지 아닌지 또는 연산 중간 type이 vector 인지 아닌지 알려주지 않는다. 왜냐하면 수식의 모든 변수는 사전에 그 타입이 검증된다. (The types of all variables are checked in advance)

- FP에서는 변수에 대하여 수학과 동일한 관점을 채택하였다.
    - 변수는 **불변하는 named constant**이며, 그 타입 역시 변하지 않는다. 
    - 타입은 "모든 가능한 값의 집합"으로 정의되며, 함수의 각 전달인자들은 반드시 타입을 가져야한다. 
    - 프로그래밍 언어의 컴파일러는 프로그램이 실행되기 이전에 자동적으로 모든 전달인자의 타입을 확인한다. 즉 부적절한 타입을 전달인자로 호출하면 컴파일되지 않는다.
- FP에서의 변수는 재사용이 가능한 표현식을 의미한다.

<a href="https://www.codecogs.com/eqnedit.php?latex=let\&space;z&space;=&space;\frac{x-y}{x&plus;y},&space;cosz&space;&plus;&space;cos2z&space;&plus;&space;cos3z" target="_blank"><img src="https://latex.codecogs.com/gif.latex?let\&space;z&space;=&space;\frac{x-y}{x&plus;y},&space;cosz&space;&plus;&space;cos2z&space;&plus;&space;cos3z" title="let\ z = \frac{x-y}{x+y}, cosz + cos2z + cos3z" /></a>
 
z는 첫 번째 수식이 수행된 뒤에도 여전히 불변하며, 그 타입 역시 고정된다. 

### 1.7.4 loop 없는 반복연산 (Iteration without loops)
- FP에서 또 다른 중요한 특징은 loop를 작성하지 않고 반복을 다루는 것이다.
- 반복적인 연산작업(Iterative computations)은 수학에서는 어디에나 존재한다. 

<a href="https://www.codecogs.com/eqnedit.php?latex=\sqrt{\frac{1}{n-1}&space;\sum_{i=1}^{n}&space;\sum_{j=1}^{n}&space;s_{i}s_{j}&space;-&space;\frac{1}{n(n-1)}&space;\left&space;(&space;\sum_{i=1}^{n}s_{i}&space;\right&space;)^{2}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sqrt{\frac{1}{n-1}&space;\sum_{i=1}^{n}&space;\sum_{j=1}^{n}&space;s_{i}s_{j}&space;-&space;\frac{1}{n(n-1)}&space;\left&space;(&space;\sum_{i=1}^{n}s_{i}&space;\right&space;)^{2}}" title="\sqrt{\frac{1}{n-1} \sum_{i=1}^{n} \sum_{j=1}^{n} s_{i}s_{j} - \frac{1}{n(n-1)} \left ( \sum_{i=1}^{n}s_{i} \right )^{2}}" /></a>

위 수식은 i, j의 값에 따라 반복적으로 계산된다. 그러나 그 어디에도 "이 수식을 10번 반복하시오" 라는 문장은 없다.

<a href="https://www.codecogs.com/eqnedit.php?latex=(x-1)(x^{2}&space;&plus;&space;x&space;&plus;&space;1)&space;=&space;x^{3}&space;-&space;1" target="_blank"><img src="https://latex.codecogs.com/gif.latex?(x-1)(x^{2}&space;&plus;&space;x&space;&plus;&space;1)&space;=&space;x^{3}&space;-&space;1" title="(x-1)(x^{2} + x + 1) = x^{3} - 1" /></a>

실제로, 위 수식을 10 번 반복적으로 수행하는 것은 무의미하다.

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum_{i=1}^{n},&space;\&space;\&space;\prod_{i=1}^{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum_{i=1}^{n},&space;\&space;\&space;\prod_{i=1}^{n}" title="\sum_{i=1}^{n}, \ \ \prod_{i=1}^{n}" /></a>

loop 대신에 수학자들은 위와 같은 기호를 사용하는데 이는 수학에서 반복적인 연산작업을 의미하기 때문이다. 이러한 연산은 수학적 유도 과정을 통해 정의된다. 

**함수형 프로그래밍 방식은 수학적 유도 과정을 코드로 변환하기 위한 도구로 발전해왔다.** .map, .filter, .sum 등은 반복적인 연산과정을 구현한 함수들이다. 이러한 연산자들은 loop 없이도 매우 유연하게 반복적인 연산을 수행하는 코드를 작성 가능하게 한다. 

개발자들은 .map, .filter, .sum 등과 같은 이미 정의된 반복함수들을 통해 loop 작성을 library에 위임하며, library들은 이렇게 작성된 함수를 machine 코드로 변환한다. 아마도 변환된 machine code는 loop를 포함하겠지만, 개발자는 알 필요가 없다.


### 1.7.5 수학적 표기법에서의 무명함수
- **수학에서의 함수는 임의의 set에서 다른 set로의 맵핑이다.**
- 함수는 이름을 반드시 필요로 하지 않으며, **단지 맵핑만 정의될 뿐이다.**
- 그러나 무명함수는 전통적인 수학적 표기법에서 매우 광범위하게 사용된다.
- 무명함수는 FP에서 매우 중요한 것으로 개발자가 코드를 직관적이고 일관성 있게 작성하도록 한다. 
- 무명함수는 함수 범위(scope) 밖에서는 볼 수 없는 바인딩 변수를 포함한다. 이러한 특징은 수학적 규칙으로부터 따라온 것이다.

<a href="https://www.codecogs.com/eqnedit.php?latex=f(x)&space;=&space;\int_{0}^{x}&space;\frac{dx}{1&plus;x}\&space;;&space;\&space;f(x)&space;=&space;\int_{0}^{x}&space;\frac{dz}{1&plus;z}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?f(x)&space;=&space;\int_{0}^{x}&space;\frac{dx}{1&plus;x}\&space;;&space;\&space;f(x)&space;=&space;\int_{0}^{x}&space;\frac{dz}{1&plus;z}" title="f(x) = \int_{0}^{x} \frac{dx}{1+x}\ ; \ f(x) = \int_{0}^{x} \frac{dz}{1+z}" /></a>

예로 수학적 규칙은 적분변수를 다시 명명하더라도 두 수식은 동일한 함수 f 이다. 

프로그래밍에서는 변수의 이름을 바꿀 수 있는 유일한 경우는 함수의 전달인자 뿐이다.
```scala
(x: Double) => 1.0 / 1 + x
(z: Double) => 1.0 / 1 + z

integreation(0, x, { z => 1.0 / (1 + z ) } )
```

다음은 합계를 구하는 전통적인 수학적 표기법의 예시이다.

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum_{k=0}^{x}&space;\frac{1}{1&plus;k}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum_{k=0}^{x}&space;\frac{1}{1&plus;k}" title="\sum_{k=0}^{x} \frac{1}{1+k}" /></a>

위 수식에서 k는 Σ 기호에 바인딩된 변수이다. 그러나 적분은 특별한 기호인 "d"에 따라 바운딩 된다. 이러한 불일치성은 아래와 같이 무명함수를 사용함으로써 명확해진다.

<a href="https://www.codecogs.com/eqnedit.php?latex=denote\&space;summation\&space;by\&space;\sum_{0}^{x}(k\rightarrow&space;\frac{1}{1&plus;k})\&space;instead\&space;of\&space;\sum_{k=0}^{x}&space;\frac{1}{1&plus;k}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?denote\&space;summation\&space;by\&space;\sum_{0}^{x}(k\rightarrow&space;\frac{1}{1&plus;k})\&space;instead\&space;of\&space;\sum_{k=0}^{x}&space;\frac{1}{1&plus;k}" title="denote\ summation\ by\ \sum_{0}^{x}(k\rightarrow \frac{1}{1+k})\ instead\ of\ \sum_{k=0}^{x} \frac{1}{1+k}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=denote\&space;integration\&space;by\&space;\int_{0}^{x}(z\rightarrow&space;\frac{1}{1&plus;z})\&space;instead\&space;of\&space;\int_{0}^{x}&space;\frac{dz}{1&plus;z}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?denote\&space;integration\&space;by\&space;\int_{0}^{x}(z\rightarrow&space;\frac{1}{1&plus;z})\&space;instead\&space;of\&space;\int_{0}^{x}&space;\frac{dz}{1&plus;z}" title="denote\ integration\ by\ \int_{0}^{x}(z\rightarrow \frac{1}{1+z})\ instead\ of\ \int_{0}^{x} \frac{dz}{1+z}" /></a>

위 수식은 아래와 같이 scala 함수로 작성 가능하다. 
```scala
summation(0, x, { y => 1.0 / (1 + y) } )

def summation(a: Int, b: Int, g: Int => Double): Double = (a to b).map(g).sum
```

적분의 경우에는 좀 더 복잡하다. 다음은 [Simpson's rule](https://en.wikipedia.org/wiki/Simpson%27s_rule) 을 Scala로 line-by-line 작성한 것이다

- Simpson's rule

<a href="https://www.codecogs.com/eqnedit.php?latex=integration(a,&space;b,&space;g,&space;e)&space;=&space;\frac{\sigma}{3}(g(a)&space;&plus;&space;g(b)&space;&plus;&space;4s_{1}&space;&plus;&space;2s_{2})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?integration(a,&space;b,&space;g,&space;e)&space;=&space;\frac{\sigma}{3}(g(a)&space;&plus;&space;g(b)&space;&plus;&space;4s_{1}&space;&plus;&space;2s_{2})" title="integration(a, b, g, e) = \frac{\sigma}{3}(g(a) + g(b) + 4s_{1} + 2s_{2})" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=where\&space;n&space;=&space;2\left&space;\lfloor&space;\frac{b-a}{\epsilon}&space;\right&space;\rfloor,\&space;\&space;\sigma_{x}&space;=&space;\frac{b-a}{n}," target="_blank"><img src="https://latex.codecogs.com/gif.latex?where\&space;n&space;=&space;2\left&space;\lfloor&space;\frac{b-a}{\epsilon}&space;\right&space;\rfloor,\&space;\&space;\sigma_{x}&space;=&space;\frac{b-a}{n}," title="where\ n = 2\left \lfloor \frac{b-a}{\epsilon} \right \rfloor,\ \ \sigma_{x} = \frac{b-a}{n}," /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex={s}_{1}&space;=&space;\sum_{k=1,3,...,n-1}^{\&space;}g(a&space;&plus;&space;k{\sigma}_{x}),\&space;\&space;{s}_{2}&space;=&space;\sum_{k=2,4,...,n-2}^{\&space;}g(a&space;&plus;&space;k{\sigma}_{x})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?{s}_{1}&space;=&space;\sum_{k=1,3,...,n-1}^{\&space;}g(a&space;&plus;&space;k{\sigma}_{x}),\&space;\&space;{s}_{2}&space;=&space;\sum_{k=2,4,...,n-2}^{\&space;}g(a&space;&plus;&space;k{\sigma}_{x})" title="{s}_{1} = \sum_{k=1,3,...,n-1}^{\ }g(a + k{\sigma}_{x}),\ \ {s}_{2} = \sum_{k=2,4,...,n-2}^{\ }g(a + k{\sigma}_{x})" /></a>

> simpson's rule은 수칙 해석에서 뉴턴-코츠 법칙의 한 경우로 적분의 근사값을 구하는데 쓰인다.
![image](https://user-images.githubusercontent.com/13671946/72363361-15b37480-3738-11ea-8931-6b456a75b1b1.png)


```scala
def integration(a: Double, b: Double, g: Double => Double, eps: Double): Double = {
  val n: Int = 2 * ((b - a) / eps).toInt
  val delta_x = (b - a) / n

  val s1 = (1 to (n-1) by 2).map { k => g(a + k * delta_x) }.sum
  val s2 = (2 to (n-2) by 2).map { k => g(a + k * delta_x) }.sum

  delta_x / 3 * ( g(a) + g(b) + 4*s1 + 2 * s2 )
}

integration(0, 5, x=> x*x*x*x, eps = 0.01)
integration(0, 7, x=> x*x*x*x*x*x, eps = 0.01)
```

