# Table of contents
1. The Writer Category
2. Writer in Haskell
3. Kleisli Categories


## Kleisli Categories

```cpp
string logger;
bool negate(bool b) {
  logger += "Not so! ";
  return !b;
}
```
- side effects 가 존재하는 함수이다.


#### 개선 1.
```cpp
pair<bool, string> negate(bool b, string logger) {
  return make_pair(!b, logger + "Not so! ");
}
```
- global mutable state를 제거하여 pure function으로 변경
- 하지만 `string` 값을 input parameter로 전달하는 것은 불편함

#### 개선 2.

```cpp
vector<string> words(string s) {
  vector<string> result{""};
  for (auto i = begin(s); i != end(s); ++i) {
    if (isspace(*i))
      result.push_back("");
    else
      result.back() += *i;
  }
  return result;
}

template<class A>
using Writer = pair<A, string>;

// turns lower case characters to upper case
Writer<string> toUpper(string s) {
    string result;
    int (*toupperp)(int) = &toupper;
    transform(begin(s), end(s), back_inserter(result), toupperp);
    return make_pair(result, "toUpper ");
}

// splits a string into a vector of strings, breaking it on whitespace boundaries
Writer<vector<string>> toWords(string s) {
    return make_pair(words(s), "toWords ");
}
```

```cpp
// compose two functions into another embellished function
// uppercase a string + splits it into words 

Writer<vector<string>> process(string s) {
  auto p1 = toUpper(s);
  auto p2 = toWords(p1.first);
  return make_pair(p2.first, p1.second + p2.second);
}
```
1. 대문자로 변환하는 함수, 문자열을 단어로 쪼개는 함수를 compose하여 log aggregation을 수행한다.
2. funtionality piggyback을 통해 log aggregation에 대한 역할이 각 function으로부터 분리되었다. 
3. funtionality piggyback을 위해 함수들의 return type을 embellishing 하는 것은 매우 유용함을 확인하였다.

_그러나 모든 프로그램을 composable하게 작성하는 것은 헬이다._ **function composition 그 자체를 추상화시켜야한다.**


### 1. The Writer Category
```cpp
pair<bool, string> isEven(int n) {
  return make_pair(n % 2 == 0, "isEven ");
}

pair<bool, string> negate(bool b) {
  return make_pair(!b, "Not so! ");
}

pair<bool, string> isOdd(int n) {
  pair<bool, string> p1 = isEven(n);
  pair<bool, string> p2 = negate(p1.first);

  return make_pair(p2.first, p1.second + p2.second);
}
```
- 짝수인지 판별하는 `isEven` morphism
- 로그를 출력하는 `negate` morphism

다음은 위 morphism을 통한 composition 과정이다.

1. 첫 번째 `isEven` morphism을 수행하고
2. (1)의 결과 pair로부터 첫번째 component(bool)를 통해 두 번째 `isOdd` morphism을 호출한다. 
3. (1)의 결과 pair의 두번째 component(string)와 (2)의 결과 pair의 두 번째 component(string)를 concanate한다.

위 composition 과정을 C++ high order cuntion을 통해 추상화하면 아래와 같다.

```cpp
template<class A, class B, class C>
function<Writer<C>(A)> compose(function<Writer<B>(A)> m1,
                               function<Writer<C>(B)> m2)
{
    return [m1, m2](A x) {
        auto p1 = m1(x);
        auto p2 = m2(p1.first);
        return make_pair(p2.first, p1.second + p2.second);
    };
}

Writer<vector<string>> process(string s) {
    return compose<string, string, vector<string>>(toUpper, toWords)(s);
}
```
- C++14-comliant compiler의 경우 lambda function을 지원하므로 아래와 같이 변경할 수 있다.
```cpp
auto const compose = [](auto m1, auto m2) {
    return [m1, m2](auto x) {
        auto p1 = m1(x);
        auto p2 = m2(p1.first);
        return make_pair(p2.first, p1.second + p2.second);
    };
};

Writer<vector<string>> process(string s) {
    return compose(toUpper, toWords)(s);
}
```

identity morphism은 아래와 같다.
```cpp
Writer<A> identity(A);

template<class A> Writer<A> identity(A x) {
    return make_pair(x, "");
}
```


위에서 정의한 category가 category의 조건을 충족하는지 검증해보자
```cpp
make_pair(p2.first, p1.second + p2.second);
```
- `p2.first`는 regular function composition으로 결합(associative) 가능하다.
- `p1.second + p2.second`는 concat이며, concatenation은 결합(associative) 가능하다.
```
 ("Hello" ++ " ") ++ "world!" == "Hello" ++ (" " ++ "world!")
```
