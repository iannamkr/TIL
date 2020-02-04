# 2.4 Transforming a sequence into another sequence

- `.map`, `.zip` 함수는 sequence를 sequence로 변환한다. 그러나 이러한 함수들은 수학적 귀납법을 통한 일반적 변환 방법을 제공하지 못한다. 
- 예를 들어 주어진 sequence로부터 부분합을 연산하는 경우, 이에 대학 수식과 수학적 귀납식은 아래와 같다.

<a href="https://www.codecogs.com/eqnedit.php?latex=b_k&space;=&space;\sum_{i=0}^{k-1}&space;x_i" target="_blank"><img src="https://latex.codecogs.com/gif.latex?b_k&space;=&space;\sum_{i=0}^{k-1}&space;x_i" title="b_k = \sum_{i=0}^{k-1} x_i" /></a>

  - (base case) <a href="https://www.codecogs.com/eqnedit.php?latex=b_0&space;=&space;0" target="_blank"><img src="https://latex.codecogs.com/gif.latex?b_0&space;=&space;0" title="b_0 = 0" /></a>
  
  - (inductive step) <a href="https://www.codecogs.com/eqnedit.php?latex=Given\&space;b_k\&space;,&space;b_{k&plus;1}&space;=&space;b_k&space;&plus;&space;x_k\&space;for\&space;k=0,1,2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Given\&space;b_k\&space;,&space;b_{k&plus;1}&space;=&space;b_k&space;&plus;&space;x_k\&space;for\&space;k=0,1,2" title="Given\ b_k\ , b_{k+1} = b_k + x_k\ for\ k=0,1,2" /></a>
  
  
- Scala library 함수인 `.scanLeft`는 일반적인 sequence-to-sequence transformation 을 지원한다.

```scala
def partialSum(xs: Seq[Int]): Seq[Int] = xs.scanLeft(0){ (x, y) => x + y }

scala> partialSums(Seq(1, 2, 3, 4))
res0: Seq[Int] = List(0, 1, 3, 6, 10)
```
- 첫 번째 argument는 base case에 해당한다.
- 두 번째 argument는 inductive step에 해당하는 updater function에 해당한다.
- 일반적으로 이전 sequence의 type은 다음 sequence의 type과 다른 경우가 많으며, `.scanLeft`의 결과는 초기 sequence보다는 크다. (base case에서 초기 값을 제공하기 때문이다.)


```scala
def scanLeft[A, B](xs: Seq[A])(b0: B)(next: (B, A) => B)
    : Seq[B] = {
  val init: (B, Seq[B]) = (b0, Seq(b0))
  val (_, result) = xs.foldLeft(init){ case ((b, seq), x) =>
    val newB = next(b, x)
    (newB, seq ++ Seq(newB))
  }
  result
}
```
- 위 코드는 `.scanLeft`를 `.foldLeft`로 바꾸어 구현한 코드이다. 


# 2.5 Summary

![image](https://user-images.githubusercontent.com/13671946/73755401-4e7bc200-47a9-11ea-9d7c-62e78fb1895e.png)


- 재귀식을 포함하고 있는 수식을 연산하는 방법
  - sequence to single value, `aggregation` or `folding`
  - new sequence from single value, `unfolding`
  - transforming sequence into new sequence
- accumulator 를 사용한 tail recursion의 활용
- type parameter를 사용한 function의 구현
