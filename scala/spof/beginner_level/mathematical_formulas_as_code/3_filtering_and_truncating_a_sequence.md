# 1.3 Filtering and truncating a sequence

scala에는 .sum, .product, .map, .forall, .max, .min, .exists, .size, .filter, .takeWhile 등의 
매우 유용한 standard library들을 제공한다.

```scala
// 30
List(10, 20, 30).max
// 10
List(10, 20, 30).min
// 3
List(10, 20, 30).size
// List[Int] = List(1, 2, 4, 5)
List(1, 2, 3, 4, 5).filter(k => k % 3 != 0)
// List[Int] = List(1, 2)
List(1, 2, 3, 4, 5).takewhile(k => k % 3 != 0)
```

**transformation** function: List 로부터 List를 return하는 함수이다. 
- .filter, .map

**aggregation** function: List로부터 single value를 return하는 함수이다. 
- .sum, .max, .min

> transformation, aggregation 함수를 함께 chaining하여 program을 개발하는 방법을 map/reduce style이라고 한다.
