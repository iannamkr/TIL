# 1.2 Aggregating data from sequence

아래의 수식을 scala로 변환해보자.

<a href="https://www.codecogs.com/eqnedit.php?latex=count&space;\&space;event(L)&space;=&space;\sum_{k\in&space;L}&space;is\_even(k)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?count&space;\&space;event(L)&space;=&space;\sum_{k\in&space;L}&space;is\_even(k)" title="count \ event(L) = \sum_{k\in L} is\_even(k)" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=is\_even(k)&space;=&space;\left\{\begin{matrix}&space;1&space;&&space;if\&space;k=0\&space;mod\&space;2\\&space;0&space;&&space;otherwise&space;\end{matrix}\right." target="_blank"><img src="https://latex.codecogs.com/gif.latex?is\_even(k)&space;=&space;\left\{\begin{matrix}&space;1&space;&&space;if\&space;k=0\&space;mod\&space;2\\&space;0&space;&&space;otherwise&space;\end{matrix}\right." title="is\_even(k) = \left\{\begin{matrix} 1 & if\ k=0\ mod\ 2\\ 0 & otherwise \end{matrix}\right." /></a>

---

우선 아래와 같이 `is_even`의 helper function을 정의할 수 있다.

```scala
def is_even(k: Int): Int = (k % 2) match {
  case 0 => 1
  case _ => 0
}

// using nameless function
def is_even = (k: Int) => if (k % 2 == 0) 1 else 0
```

---

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum_{k\in&space;L}&space;is\_even(k)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum_{k\in&space;L}&space;is\_even(k)" title="\sum_{k\in L} is\_even(k)" /></a>

다음 함수는 아래 두 단계로 실행되며, 각 단계는 .map, .sum 또는 .product로 변환할 수 있다.

1. 리스트 L 의 각 element에 대해 `is_even`함수를 적용한다.
2. (1)의 결과를 합산한다.

```scala
def count_even = (s: List[Int]) => s.map(is_even).sum
```

`is_even` 함수를 사용하지 않고 nameless function을 사용해도 된다.

```scala
def count_even(s: List[Int]): Int = s
  .map { k => if ( k % 2 == 0) 1 else 0 }
  .sum
```

```scala
// 2
count_even(List(1, 2, 3, 4, 5))

// 5
count_even(List(1, 2, 3, 4, 5).map(x => x * 2))
```
