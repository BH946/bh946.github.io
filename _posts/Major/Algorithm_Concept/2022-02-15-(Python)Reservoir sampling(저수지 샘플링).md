---
title:  "[Python]Reservoir Sampling(저수지 샘플링)"
categories : Algorithm
tag : [Reservoir Sampling(저수지 샘플링)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## Reservoir Sampling(저수지 샘플링)

저수지 샘플링(Reservoir Sampling)은 매우 큰 데이터 집합에서 크기 `k`의 랜덤한 샘플을 뽑는 알고리즘입니다. 특히 데이터의 크기 `n`이 미리 알려지지 않았거나 매우 클 때 사용됩니다. 이 알고리즘은 모든 원소가 샘플링될 확률을 균등하게 만들기 위해 설계되었습니다.

* zoom in, out 등등에서 활용 많이하는 알고리즘

```python
# 저수지 샘플링(reservoir sampling)
# 함수 안에서 yield를 사용하면 함수는 제너레이터가 되며 yield에는 값(변수)을 지정합니다.
import random
import numpy as np # 수치계산 라이브러리
def reservoir_sampling(size):
    i, sample = 0, []

    while True:
        item = yield i, sample # yiled 선언안하면 에러

        i += 1
        k = np.random.randint(0, i) 

        if len(sample) < size:
            sample.append(item)
        elif k < size:
            sample[k] = item
        
reservoir = reservoir_sampling(5) # size를 5로
next(reservoir)

for i in range(1000):
    k, sample = reservoir.send(i)
    if k % 100 == 0:
        print(k,sample)

'''
100 [32, 41, 86, 61, 22]
200 [199, 186, 86, 61, 22]
300 [199, 204, 291, 61, 238]
400 [199, 365, 341, 61, 238]
500 [199, 365, 451, 61, 238]
600 [199, 365, 451, 599, 238]
700 [199, 365, 451, 599, 627]
800 [199, 365, 451, 599, 627]
900 [199, 365, 451, 599, 627]
1000 [961, 365, 451, 599, 627]
'''
```

