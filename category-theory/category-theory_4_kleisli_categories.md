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
- `string` 값을 input parameter로 전달하는 것은 불편함

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
- funtionality piggyback을 통해 log aggregation에 대한 역할이 각 function으로부터 완전히 분리되었다. 
- funtionality piggyback을 위해 함수들의 return type을 embellishing 하는 것은 매우 유용함을 확인하였다.
- ! 그러나 모든 프로그램을 이런 스타일로 작성하는 것은 헬이다. -> **function composition 그 자체를 추상화시켜야한다.**
