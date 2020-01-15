### Tuples and collections exmples

#### 2.1.5.1
For a given sequence xi , compute the sequence of pairs bi = (cos xi , sin xi ).

Hint: use .map, assume xs:Seq[Double].

```scala
xi.map{ x => math.cos(x), math.sin(x) }
```

#### 2.1.5.2
Count how many times cos xi > sin xi occurs in a sequence xi .

Hint: use .count, assume xs:Seq[Double].

```scala
xi.count { x => math.cos(x) > math.sin(x) }
```

#### 2.1.5.3
For given sequences ai and bi , compute the sequence of differences ci = ai − bi .

Hint: use .zip, .map, and assume as and bs are of type Seq[Double].

```scala
ai.zip(bi).map { case (a, b) => a - b } 
```

#### 2.1.5.4
In a given sequence pi , count how many times pi > pi+1 occurs.

Hint: use .zip and .tail.

```scala
pi.zip(pi.tail).count { case (a, b) => a > b }
```

#### 2.1.5.5
For a given k > 0, compute the sequence ci = max(bi−k, ..., bi+k).

```scala
bs.sliding(2 * k + 1).max(i => i.max)
```

#### 2.1.5.6 
Create a 10 ×10 multiplication table as a dictionary of type Map[(Int, Int), Int]. For
example, a 3 × 3 multiplication table would be given by this dictionary,

```scala
Map( (1, 1) -> 1, (1, 2) -> 2, (1, 3) -> 3, (2, 1) -> 2,
(2, 2) -> 4, (2, 3) -> 6, (3, 1) -> 3, (3, 2) -> 6, (3, 3) -> 9 )
```

Hint: use .flatMap and .toMap.

```scala
val s = List(1, 2, 3)
val result = s.map(x => s.map( y => ((x, y), x * y))).flatten.toMap[(Int, Int), Int]
val result = s.flatMap(x => s.map( y => ((x, y), x * y))).toMap[(Int, Int), Int]
```

#### 2.1.5.7 
For a given sequence x_i, compute the maximum of all of the numbers x, x^2, cosx, sinx

Hint: use .flatMap, .max.

```scala
// xi.map(s => s).max
// xi.map(s => s*s).max
// xi.map(s => math.cos(s)).max
// xi.map(s => math.sin(s)).max

// xi.map(s => Seq(s, s*s, math.cos(s), math.sin(s))).flatten.max
xi.flatMap(s => Seq(s, s*s, math.cos(s), math.sin(s))).max
```
