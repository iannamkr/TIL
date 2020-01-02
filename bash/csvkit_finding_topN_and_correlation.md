## Prerequisite
```bash
sudo pip install csvkit
sudo pip install scatter
```

1. `Artist.Name`, `Length`, `Popularity` 컬럼을 인기도 순으로 정렬 후 상위 10 추출
```bash
cat top50.csv | cut -f3,11,14 -d, | sort -k 3 -t"," -g -r | csvlook -H --max-rows 10
```

2. `Length`, `Popularity` 간의 correlation 파악
```bash
cat top50.csv | cut -f11,14 -d, > sample.csv
cat sample.csv | tail -n +2 | scatter -c blue
```
