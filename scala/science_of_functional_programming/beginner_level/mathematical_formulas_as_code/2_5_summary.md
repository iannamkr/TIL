# 2.5 Summary

![image](https://user-images.githubusercontent.com/13671946/73851710-5010bd00-4871-11ea-9979-0d437e4c7549.png)

# 2.5.1 Examples

## 2.5.1.1
Compute the smallest n such that f ( f ( f (... f (1)...) ≥ 1000, where the function f is
applied n times. Write this as a function taking f , 1, and 1000 as arguments. Test with f (x) = 2x + 1.

```scala
val f = (x: Int) => 2 * x + 1
val n = Stream.iterate(1)(f).takeWhile(x => x < 1000).length
```

