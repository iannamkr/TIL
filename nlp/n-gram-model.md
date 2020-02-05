# N-gram model

### N-1 Markov Decision Process and N-gram model

<a href="https://www.codecogs.com/eqnedit.php?latex=P(w_i&space;|&space;w_1,&space;w_2,&space;...&space;,&space;w_{i-1})&space;=&space;P(w_i&space;|&space;w_{i-N&plus;1},&space;w_{i-N&plus;1},&space;...&space;,&space;w_{i-1})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?P(w_i&space;|&space;w_1,&space;w_2,&space;...&space;,&space;w_{i-1})&space;=&space;P(w_i&space;|&space;w_{i-N&plus;1},&space;w_{i-N&plus;1},&space;...&space;,&space;w_{i-1})" title="P(w_i | w_1, w_2, ... , w_{i-1}) = P(w_i | w_{i-N+1}, w_{i-N+1}, ... , w_{i-1})" /></a>

- 텍스트의 각 단어 w_i가 이전 N-1개의 단어와 연관 있다고 가정하는 경우, P(w_i)는 이전 N-1개 단어의 확률인 <a href="https://www.codecogs.com/eqnedit.php?latex=P(w_{i-N&plus;1},&space;w_{i-N&plus;1},&space;...&space;,&space;w_{i-1})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?P(w_{i-N&plus;1},&space;w_{i-N&plus;1},&space;...&space;,&space;w_{i-1})" title="P(w_{i-N+1}, w_{i-N+1}, ... , w_{i-1})" /></a> 에 의해 결정된다.

```scala
type NGram = Map[Seq[String], Double]

def seq2NGram(n: Int, tokens: Seq[String]): NGram = {
    val corpus = tokens.sliding(n).toSeq
    val ngram = corpus.groupBy(identity).mapValues(_.size)
    ngram.map { case(k, v) => (k, v.toDouble / ngram.filterKeys(_.take(n - 1) == k.take(n - 1)).values.sum)}
}
```


### References
- N-gram Language Models: https://web.stanford.edu/~jurafsky/slp3/3.pdf
- Google AI Blog: https://ai.googleblog.com/2006/08/all-our-n-gram-are-belong-to-you.html
