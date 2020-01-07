# Table of contents
1. No Objects
- zero object & zero morphism
- empty set makes sense, then how about an empty category?

2. Simple Graphs
- categories by connecting objects with arrows
  - 1. an identity arrow at each node
  - 2. any two composable arrows
  - ** Every time you add a new arrow, you have to consider its composition 
  with any other arrow (except for the identity arrows) and itsetlf
- `free category`
  - ![image](https://user-images.githubusercontent.com/13671946/71914103-39b50a00-31bc-11ea-9cc2-1b0e68e17246.png)
  - composition: ![image](https://user-images.githubusercontent.com/13671946/71914076-2ace5780-31bc-11ea-9748-f24665140117.png)

  - 1. all object for every node in the graph
  - 2. all possible chains of composable graph edges as morphisms

3. Orders
- A category is a relation between object: **the relation of being less than or equal**
- `preorder`: a < b and b < c then a < c (transitivity)
- `partial order`: a < b and b < a then a = b (anti-symmetry)
- `total order` or `linear order`: 두 object 간에 한 방향으로 비교가 가능한 경우
  - If a ≤ b and b ≤ a then a = b (antisymmetry)
  - If a ≤ b and b ≤ c then a ≤ c (transitivity)
  - a ≤ b or b ≤ a (totality)

- home-set `C(a, b)`
  - A set of morphisms(arrow) from object a to object b in a category C
  - Every home-set in a `preorder` is empty or singleton
    - home-set C(a,a), morphism from a to a must be a singleton, containing only the identity, in any preorder
  - ** preorder may have cycles but forbidden in a partial order **
  - ** Sorting algorithms, such as quick sort, bubble sort, merge sort, etc can only work correctly on total orders.
  - ** partial orders can be sorted using topological sort **

  

4. Monoid as Set
5. Monoid as Category
