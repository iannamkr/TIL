### Tuples and collections exmples
---
#### 2.1.5.1
For a given sequence `xi` , compute the sequence of pairs `bi = (cos xi , sin xi )`.

**Hint**: use `.map`, assume `xs:Seq[Double]`.

**Solution**
```scala
xi.map{ x => math.cos(x), math.sin(x) }
```
---
#### 2.1.5.2
Count how many times `cos xi` > `sin xi` occurs in a `sequence xi` .

**Hint**: use `.count`, assume `xs:Seq[Double]`.

**Solution**
```scala
xi.count { x => math.cos(x) > math.sin(x) }
```
---
#### 2.1.5.3
For given sequences `ai` and `bi` , compute the sequence of differences `ci = ai − bi` .

**Hint**: use `.zip`, `.map`, and assume as and bs are of type `Seq[Double]`.

**Solution**
```scala
ai.zip(bi).map { case (a, b) => a - b } 
```
---
#### 2.1.5.4
In a given sequence `pi` , count how many times `pi > pi+1` occurs.

**Hint**: use `.zip` and `.tail`.

**Solution**
```scala
pi.zip(pi.tail).count { case (a, b) => a > b }
```
---
#### 2.1.5.5
For a given k > 0, compute the sequence `ci = max(bi−k, ..., bi+k)`.

**Solution**
```scala
bs.sliding(2 * k + 1).max(i => i.max)
```
---
#### 2.1.5.6 
Create a 10 ×10 multiplication table as a dictionary of type `Map[(Int, Int), Int]`. For
example, a 3 × 3 multiplication table would be given by this dictionary,

```scala
Map( (1, 1) -> 1, (1, 2) -> 2, (1, 3) -> 3, (2, 1) -> 2,
(2, 2) -> 4, (2, 3) -> 6, (3, 1) -> 3, (3, 2) -> 6, (3, 3) -> 9 )
```

**Hint**: use `.flatMap` and `.toMap`.

**Solution**
```scala
val s = List(1, 2, 3)
val result = s.map(x => s.map( y => ((x, y), x * y))).flatten.toMap[(Int, Int), Int]
val result = s.flatMap(x => s.map( y => ((x, y), x * y))).toMap[(Int, Int), Int]
```
---
#### 2.1.5.7 
For a given `sequence x_i`, compute the maximum of all of the numbers `x`, `x^2`, `cosx`, `sinx`

**Hint**: use `.flatMap`, `.max`.

**Solution**
```scala
// xi.map(s => s).max
// xi.map(s => s*s).max
// xi.map(s => math.cos(s)).max
// xi.map(s => math.sin(s)).max

// xi.map(s => Seq(s, s*s, math.cos(s), math.sin(s))).flatten.max
xi.flatMap(s => Seq(s, s*s, math.cos(s), math.sin(s))).max
```
---
#### 2.1.5.8
From a dictionary of type `Map[String, String]` mapping names to addresses, and
assuming that the addresses do not repeat, compute a dictionary of type `Map[String, String]` mapping
the addresses back to names.

**Hint**: use `.map` and `.toMap`

**Solution**
```scala
xi.map{ case (name, address) => (address, name) }.toMap
```
---
#### 2.1.5.9
Write the solution of Example 2.1.5.8 as a function with type parameters Name and
Addr instead of the fixed type String.

**Solution**
```scala
def getReverseDict(dic: Map[Name, Address]): Map[Address, Name] = {
  dic.map { case (name, address) => (address, name) }.toMap
}
```

---
#### 2.1.5.10
Given a sequence `words:Seq[String]` of words, compute a sequence of type `Seq[ (Seq[String], Int) ]`, where each inner sequence contains all the words having the same length, paired with the integer value showing that length. So, the input `Seq("the", "food", "is", "good")` should produce the output

```Seq( (Seq("is"), 2), (Seq("the"), 3), (Seq("food", "good"), 4) )```

**Solution**
```scala
// words.groupBy(s => s.length).toSeq.map { case (len, word) => (word, len) }
// words.groupBy(_.length).toSeq.map { case (len, word) => (word, len) }
words.groupBy(_.length).toSeq.map(_.swap)
```

---
#### 2.1.7.1
Find all pairs `i`, `j` within (0, 1, ..., 9) such that `i + 4 ∗ j > i ∗ j`.

**Hint**: use `.flatMap` and `.filter`.

**Solution**
```scala
s.flatMap{ i => s.map { j => (i + 4 * j, i * j) } }.filter { case (x, y) => x > y }
```
---
#### 2.1.7.2
Same task as in Exercise 2.1.7.1, but for `i`, `j`, `k` and the condition `i + 4 ∗ j + 9 ∗ k > i ∗ j ∗ k`.

**Solution**
```scala
s.flatMap{ i => s.flatMap { j => s.map { k => (i + 4 * j + 9 * k, i * j * k) } } }.filter { case (x, y) => x > y }
```
---
#### 2.1.7.3 
Given two sequences `p: Seq[String]` and `q: Seq[Boolean]` of equal length, 
compute a `Seq[String]` with those elements of `p` for which the corresponding element of `q` is true.

**Hint**: use `.zip`, `.map`, `.filter`.

**Solution**
```scala
val p: Seq[String] = Seq("a", "b", "c", "d", "e")
val q: Seq[Boolean] = Seq(true, true, false, false, true)

p.zip(q).filter(_._2).map(_._1)
// List(a, b, e)
```
---
#### 2.1.7.4 :exclamation:
Convert a `Seq[Int]` into a `Seq[(Int, Boolean)]` where the Boolean value is true when the element is followed by a larger value. 

For example, the input sequence `Seq(1,3,2,4)` is to be converted into `Seq((1,true),(3,false),(2,true),(4,false))`. (The last element, 4, has no following element.)

**Solution**
```scala
val p: Seq[Int] = Seq(1,3,2,4)
val q: Seq[(Int, Boolean)] = p.zip(p.tail :+ 0).map { case (x, y) => (x, x < y) }
```
---
#### 2.1.7.5
Given `p:Seq[String]` and `q:Seq[Int]` of equal length and assuming that values in `q` do
not repeat, compute a `Map[Int, String]` mapping numbers from `q` to the corresponding strings from `p`.

**Solution**
```scala
val p: Seq[String] = Seq("a", "b", "c", "d", "e")
val q: Seq[Int] = Seq(4, 0, 3, 2, 1)

q.flatMap(i => p.zipWithIndex.filter(_._2 == i).map(j => (j._1 -> i))).toMap
// Map(e -> 4, a -> 0, b -> 1, c -> 2, d -> 3)
```

---
#### 2.1.7.7
Given `p: Seq[String]` and `q: Seq[Int]` of equal length, compute a `Seq[String]` that contains the strings from p ordered according to the corresponding numbers from q. 

For example, if `p = Seq("a", "b", "c")` and `q = Seq(10, -1, 5)` then the result must be `Seq("b", "c", "a")`.

**Solution**
```scala
val p = Seq("a", "b", "c")
val q = Seq(10, -1, 5)

p.zip(q).sortWith(_._2 < _._2).map(_._1)
```
---
#### 2.1.7.8
Write the solution of Exercise 2.1.7.7 as a function with type parameter `S` instead of the fixed type String. The required type signature and a sample test:

```scala
def reorder[S](p: Seq[S], q: Seq[Int]): Seq[S] = ???

scala> reorder(Seq(6.0,2.0,8.0,4.0), Seq(20,10,40,30))
res0: Seq[Double] = List(2.0, 6.0, 4.0, 8.0)
```

**Solution**
```scala
def reorder[S](p: Seq[S], q: Seq[Int]): Seq[S] = {
    p.zip(q).sortWith(_._2 < _._2).map(_._1)
}

println(reorder(Seq(6.0,2.0,8.0,4.0), Seq(20,10,40,30)))
// List(2.0, 6.0, 4.0, 8.0)
```
---
#### 2.1.7.9
Given a `Seq[(String, Int)]` showing a list of purchased items (where item names may repeat), compute a `Map[String, Int]` showing the total counts: 

e.g. for the input

```scala
Seq(("apple", 2), ("pear", 3), ("apple", 5))
```

the output must be

```scala
Map("apple" -> 7, "pear" -> 3)
```

**Solution**
```scala
val p = Seq(("apple", 2), ("pear", 3), ("apple", 5))
val q = Map("apple" -> 7, "pear" -> 3)
  
p.groupBy(_._1).mapValues(_.map(_._2).sum)
// Map(pear -> 3, apple -> 7)
```
---
#### 2.1.7.10
Given a Seq[List[Int]], compute a newSeq[List[Int]] where each inner list contains
three largest elements from the initial inner list (or fewer than three if the initial inner list is shorter).

**Hint**: use `.map`, `.sortBy`, `.take`.

**Solution**
```scala
val p: Seq[List[Int]] = Seq(List(3,5,2,7,9,1), List(14,16,11,12,17), List(26,21,29,27,25))

val q: Seq[List[Int]] = p.map(s => s.sortBy(s => s)(Ordering[Int].reverse).take(3))
//List(List(9, 7, 5), List(17, 16, 14), List(29, 27, 26))
```
---
#### 2.1.7.11
(a) Given two sets `p:Set[Int]` and `q:Set[Int]`, compute a set of type `Set[(Int, Int)]` as the Cartesian product of the sets `p` and `q`; that is, the set of all pairs `(x, y)` where x is an element from the set `p` and `y` is an element from the set `q`.

(b) Implement this computation as a function with type parameters `I`, `J` instead of `Int`. The required type signature and a sample test:

```scala
def cartesian[I,J](p: Set[I], q: Set[J]): Set[(I, J)] = ???

scala> cartesian(Set("a", "b"), Set(10, 20))
res0: Set[(String, Int)] = Set((a,10), (a,20), (b,10), (b,20))
```
**Hint**: use `.flatMap` and `.map` on sets.

**Solution (a)**
```scala
val p: Set[String] = Set("a", "b")
val q: Set[Int] = Set(10, 20)

val cartesian = p.flatMap(i => q.map(j => (i, j)))
```

**Solution(b)**
```scala
val p: Set[String] = Set("a", "b")
val q: Set[Int] = Set(10, 20)

def cartesian[I, J](p: Set[I], q: Set[J]): Set[(I, J)] = {
    p.flatMap(i => q.map(j => (i, j)))
}
```
---
#### 2.1.7.12
Given a Seq[Map[Person, Amount]], showing the amounts various people paid on
each day, compute a Map[Person, Seq[Amount]], showing the sequence of payments for each person.
Assume that Person and Amount are type parameters. The required type signature and a sample test:

```scala
def payments[Person, Amount](data: Seq[Map[Person, Amount]]): Map[Person, Seq[Amount]] = ???
// On day 1, Tarski paid 10 and Gödel paid 20. On day 2, Church paid 100 and Gentzen paid 50, etc.

scala> payments(Seq(Map("Tarski" -> 10, "Gödel" -> 20), Map("Church" -> 100, "Gentzen" -> 50),
    Map("Tarski" -> 50), Map("Banach" -> 15, "Gentzen" -> 35)))
res0: Map[String, Seq[Int]] = Map(Genzten -> List(50, 35), Church -> List(100), Banach -> List(15),
    Tarski -> List(10, 50), Gödel -> List(20))
```
**Hint**: use `.flatMap`, `.groupBy`, `.mapValues` on dictionaries.

**Solution**

```scala
val p = Seq(Map("Tarski" -> 10, "Gödel" -> 20), Map("Church" -> 100, "Gentzen" -> 50), Map("Tarski" -> 50), Map("Banach" -> 15, "Gentzen" -> 35))

p.flatten.groupBy(_._1).map { case (k, v) => (k, v.map(_._2)) }
// Map(Gentzen -> List(50, 35), Church -> List(100), Banach -> List(15), Gödel -> List(20), Tarski -> List(10, 50))
```
