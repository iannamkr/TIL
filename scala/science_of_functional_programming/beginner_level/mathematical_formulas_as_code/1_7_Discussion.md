# 1.7 Discussion

## 1.7.1 함수 지향 프로그래밍 패러다임(Functional programming as a paradigm)
- 함수형 프로그래밍은 프로그래머가 코드를 작성함에 있어 선택 가능한 다양한 방식 중의 하나이다.
- FP(Functional Programming)에서 가장 중요한 기본 원칙은 코드를 수학 표현식(a mathematical expression or formula)으로 작성하는 것이다.
- FP는 개발자로 하여금 추측(guessing)이 아닌 수학자/과학자와 같이 체계적이고 논리적인 추론(logical reasoning)을 통해 코드를 작성하도록 한다.
- 오랜시간 FP 커뮤니티에서는 여러 프로그래밍 문제를 해결하기 위해 함수형 도구(e.g .map, .filter ...)들을 만들어왔으며, 그 결과 실제 세계의 다양한 문제를 해결하는데 유용함을 입증하였다. 

## 1.7.2 함수형 프로그래밍 언어 (Functional programming languages) 
- FP는 프로그래밍 방식으로써 모든 언어를 통해 적용할 수 있다. 그러나 몇몇 언어는 완벽한 FP 기술을 적용하는데 한계가 있다.
- Python, Ruby의 경우 map/reduce operation 등 제한적인 기능만 제공하며, 이외의 추가적인 FP기능을 사용하기 위해서는 많은 코드가 필요하다. 또한 이 역시 error 없이 작성하는 것은 매우 힘들다. 
- Haskell, Ocamel의 경우 설계에서부터 진보된 FP를 제공하기 위해 개발된 언어이다. 
- ML, F#, Scala, Swift, Elm, PureScript의 경우 Haskell, Ocamel과는 다른 목표를 지향하지만 FP 프로그래밍을 하는 데에는 충분한 기능을 제공한다.  

## 1.7.3 'variable' 의 수학적 의미 
- FP에서의 variable은 수학에서 사용되는 변수와 매우 유사하다. 

- in mathmatics 
  - 수학에서의 변수는 함수 인자로 사용된다. 
  - 수학에서의 변수는 수식 내에서 값이 변경되지 않는다는 불변성의 규칙이 있다.(nameless function 에서의 인자 역시 불변하다.)
  - 즉 수학에서의 변수는 정의된 수식(expression) 안에서 이름상수(named constant)로 취급된다.
  - 인자 x의 값에 따라 함수 f의 결과는 바뀔 수 있으나, 전달된 x는 함수 f가 처리되는 동안에는 변경되지 않는다.
  - 수식 ```x^2 + x```에는 x의 값이 정수인지 아닌지 표현하지 않으며, 연산 중간에 type이 vector 인지도 알려주지 않는다. 왜냐하면 수식에서 모든 변수는 타입은 미리 확인되어진다.

- in FP
  - FP는 수학의 변수가 따르는 규칙을 그대로 적용하였다. 
  - 변수는 **불변하는 이름상수**이며 타입 역시 변하지 않는다. 
 
  - 


