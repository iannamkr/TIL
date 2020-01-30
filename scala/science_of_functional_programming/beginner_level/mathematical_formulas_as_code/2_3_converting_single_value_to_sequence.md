# 2.3 Converting a single value into a sequence

- 집계(aggregation)는 sequence를 single value로 `fold` 하거나 변형한다. 
- 집계의 반대는 `unfolding`이며, single value를 sequence로 변환한다.
- `unfolding`의 경우 사전에 sequence의 길이를 정의할 수 없다. 이러한 종류의 sequence를 `stream`이라고 한다.
- `stream`은 `List`, `Array` 처럼 element가 모두 미리 연산되고, 저장되는 것이 아니라 필요한 경우에 한하여 연산된다.
- `unfolding` 연산은 계속 다음 element를 연산하며, 이것이 `stream`을 생성하고 특정 조건에 의해 종료되기 위해 `.takeWhile`을 `stream`에 적용한다.
- 최종적으로 truncate된 `stream`은 list나 다른 타입의 sequence로 변환된다. 이러한 방법을 통해 사전에 길이가 정의되지 않은 sequence를 생성할 수 있다. 

```scala
Stream.iterate(2) { x => x + 10 }
res0: Stream[Int] = Stream(2, ?)

Stream.iterate(2) { x => x + 10 }.take(6).toList
res1: List[Int] = List(2, 12, 22, 32, 42, 52)
```

- 다음 element 값을 확인하기 위해서는 정해진 크기의 list로 변환하여 stream을 종료시킨다. 
- 만약 `.take`, `.takeWhile`과 같이 크기를 제한하지 않고 `.toList`로 evaluate하는 경우 `stream`은 memory가 소진될때까지 element를 연산한다.
- 다른 sequence와 같이 `stream`도 `.map`, `.filter`, `.flatmap`과 같은 함수가 정의되어있다. 

**example**
```scala
def digitsOf(n: Int): Seq[Int] = 
  if (n == 0) Seq(0) else {
    Stream.iterate(n)(_ / 10)
      .takeWhile(_ != 0)
      .map(_ % 10)
      .toList.reverse
  }

digitsOf(2405)
res0: Seq[Int] = List(2, 4, 0, 5)
```
