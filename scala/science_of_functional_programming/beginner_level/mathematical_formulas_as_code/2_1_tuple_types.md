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
