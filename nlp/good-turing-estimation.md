# Good-Turing estimation


- 언어 모델의 확률 추정에 있어 무작위성(randomness) 논리를 간과하게 되는 경우 학습된 모델이 쓸모 없게 되는 경우가 발생한다.
데이터 양을 늘리더라도 표본의 부족으로 인해 통계언어 모델에서의 제로 확률은 발생하기 마련이다.

- 이에 1953년 어빙 존 굿은 신뢰할 수 있는 통계 데이터는 신뢰하고 신뢰할 수 없는 통계 데이터는 확률을 할인하여 추정하며, 
보이지 않은 사건에 대해서도 할인된 확률을 부여하였는데, 이러한 재추정 공식을 굿-튜링 측정(Good-Turing estimation) 이라 한다.

- 보이지 않은 사건의 경우 확률값을 0으로 하는 것이 아닌, 확률의 총량에서 매우 적은 확률을 분배한다. 확률의 총합은 1이므로 
보이지 않은 사건에 부여된 확률값 만큼 보이는 사건의 확률의 총합이 줄어들기 때문에, 데이터의 신뢰성에 따라 보이는 확률의 할인 정도를 판단한다.

---
단어 집합 ```X = {the, bad, cat}``` 에 대하여 코퍼스 W 가 다음과 같을 때 ```the bad cat the cat```, 코퍼스 W 에서 r번 발생한 단어의 갯수를 N(r) 이라 한다. 
이때 empirical probablity는 아래과 같다. 

- N(0) = 1 (0번 발생한 단어는 1개 => dog)
- N(1) = 1 (1번 발생한 단어는 1개 => bad)
- N(2) = 2 (2번 발생한 단어는 2개 => cat, the)

이를 일반적인 수식으로 표현하면 다음과 같다.

<a href="https://www.codecogs.com/eqnedit.php?latex=N&space;=&space;\sum_{r=1}^{\infty&space;}rN_r" target="_blank"><img src="https://latex.codecogs.com/gif.latex?N&space;=&space;\sum_{r=1}^{\infty&space;}rN_r" title="N = \sum_{r=1}^{\infty }rN_r" /></a>


이때 r 이 충분히 작은 경우 이를 신뢰할 수 없으므로 이를 하향 조정하기 위해 r 대신 d(r) 을 사용한다.

<a href="https://www.codecogs.com/eqnedit.php?latex=d_r=(r&plus;1)\cdot&space;\frac{N_{r&plus;1}}{N_r}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?d_r=(r&plus;1)\cdot&space;\frac{N_{r&plus;1}}{N_r}" title="d_r=(r+1)\cdot \frac{N_{r+1}}{N_r}" /></a>

따라서 다음과 같이 된다. 

<a href="https://www.codecogs.com/eqnedit.php?latex=N&space;=&space;\sum_{r=0}^{\infty}d_rN_r" target="_blank"><img src="https://latex.codecogs.com/gif.latex?N&space;=&space;\sum_{r=0}^{\infty}d_rN_r" title="N = \sum_{r=0}^{\infty}d_rN_r" /></a>


또한 주어진 단어가 코퍼스 W 에서 r 번 나타날 확률을 θ(r) 이라 할때, 굿-튜링 측정식은 다음과 같다.

<a href="https://www.codecogs.com/eqnedit.php?latex=\hat{\Theta&space;\left&space;(&space;r&space;\right&space;)}&space;=&space;\frac{1}{N}(r&plus;1)\frac{N_{r&plus;1}}{N_r}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\hat{\Theta&space;\left&space;(&space;r&space;\right&space;)}&space;=&space;\frac{1}{N}(r&plus;1)\frac{N_{r&plus;1}}{N_r}" title="\hat{\Theta \left ( r \right )} = \frac{1}{N}(r+1)\frac{N_{r+1}}{N_r}" /></a>



> 추가적인 수학적 증명은 링크를 참조한다. https://www.cs.cornell.edu/courses/cs6740/2010sp/guides/lec11.pdf


단어 집합 ```X = {apple, banana, carrots, dates, eggs, frogs, grapes}``` 에 대하여 
코퍼스 W 가 다음과 같을 때 ```apple apple apple banana banana dates dates eggs eggs eggs frogs grapes grapes```
empirical probablity와 Good-Turing estimation `θ(r)` 는 아래와 같다.

- empirical probablity
  - N(0) = 1 (carrots)
  - N(1) = 1 (frogs)
  - N(2) = 3 (banana, dates, grapes)
  - N(3) = 2 (apple, eggs)

- Good-Turing estimation
  - θ(0) = (1/13) x (0 + 1) x (1/1) = 1/13 = 0.07692
  - θ(1) = (1/13) x (1 + 1) x (3/1) = 6/13 = 0.4615
  - θ(2) = (1/13) x (2 + 1) x (2/3) = 6/39 = 0.1538
  - θ(3) = (1/13) x (3 + 1) x (0/2) = 0


### References
- lecture: https://www.cs.cornell.edu/courses/cs6740/2010sp/guides/lec11.pdf
- nlp.standford: https://nlp.stanford.edu/nlp/javadoc/javanlp/edu/stanford/nlp/stats/SimpleGoodTuring.html
- base code: https://www.grsampson.net/RGoodTur.html
- smoothing: https://web.stanford.edu/class/archive/cs/cs224n/cs224n.1122/handouts/cs224n-section1-smoothing-140111.ppt
