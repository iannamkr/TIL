# 2.5 Summary

![image](https://user-images.githubusercontent.com/13671946/73851710-5010bd00-4871-11ea-9979-0d437e4c7549.png)

# 2.5.1 Examples

## 2.5.1.1
Compute the smallest n such that f ( f ( f (... f (1)...) ≥ 1000, where the function f is
applied n times. Write this as a function taking f , 1, and 1000 as arguments. Test with f (x) = 2x + 1.

**solution**
```scala
val f = (x: Int) => 2 * x + 1
val n = Stream.iterate(1)(f).takeWhile(x => x < 1000).length
```

## 2.5.1.2

(a) For a given Stream[Int], compute the stream of the largest values seen so far.

```scala
def maxSoFar(xs: Stream[Int]): Stream[Int] = ???
```

(b) Compute the stream of k largest values seen so far (k is a given integer parameter).

```scala
def maxKSoFar(xs: Stream[Int], k: Int): Stream[Seq[Int]] = ???
```

**solution**
```scala
def maxSoFar(xs: Stream[Int]): Stream[Int] = {
  xs.scanLeft(xs.head) { case (max, x) => math.max(max, x) }.drop(1)
}
  
def maxKSoFar(xs: Stream[Int], k: Int): Stream[Seq[Int]] = {
  xs.scanLeft(Seq[Int]()) { case (s, x) => (s :+ x).sorted.take(k) }.drop(1)
}
```
