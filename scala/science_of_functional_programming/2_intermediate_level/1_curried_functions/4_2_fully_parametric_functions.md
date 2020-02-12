# 4 The logic of tpyes. II. Curried functions

## 4.2 Fully parametric functions

```scala
def map[A, B](xs: Seq[A])(f: A => B): Seq[B]
def filter[A](xs: Seq[A])(p: A => Boolean): Seq[A]
```

`map`, `filter` 함수에서 `A`, `B`는 type parameter로 사용된 반면에 `Seq`, `Boolean` 은 지정된 type으로 정의되었다. 
위 예시와 달리 경우에 따라서 모든 type을 type parameter로 정의할 수 있다. 

이처럼 모든 인자가 type parameter로 표현된 함수를 **fully parametric** 하다고 하며, 동 함수는 모든 타입을 type parameter 로 처리한다. 
다시 말해 fully parametric 함수들은 `Int`, `String` 처럼 지정된 타입의 값을 수용하지 않는다. 

따라서 fully parametric 함수는 전달인자의 타입이 type signature에 맞다고 가정하며, type 에 대한 어떤 정보도 사용하지 않는다. 

다음 예시를 보자

```scala
def cos_sin(p: (Double, Double): (Double, Double) = p match {
  case (x, y) =>
    val r = math.sqrt(x * x + y * y)
    (x / r, y / r)
}

def swap(p: (Double, Double)): (Double, Double) = p match {
  case (x, y) => (y, x)
}
```

`swap` 을 fully parmetric 함수로 변환하면 아래와 같다.
```scala
def swap[A, B](p: (A, B)): (B, A) = p match {
  case (x, y) => (y, x)
}
```

`swap` 함수의 경우 tuple (A, B) 에 대해 교환하는 연산이므로 모든 타입 A, B 에 동일하게 적용될 수 있으므로, 함수의 수정없이 
fully parmetric 함수로 변환 가능하다. 

반면에 `cos_sin` 함수의 경우는 특정한 `Double` 타입에 대한 연산을 수행하므로 type을 일반화할 수 없다. 따라서 fully parametric 함수가 될 수 없다.

일반적으로 fully parametric 함수의 모든 전달인자는 type parameter로 묶여 있거나, `(A, B)` 처럼 type parameter의 조합으로 사용된다. 

---

### 4.2.1 Examples. Function composition
