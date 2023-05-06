---
title:  "[Python]Sieve Of Eratosthenes(에라토스테네스의 체)"
categories : Algorithm
tag : [SieveOfEratosthenes, 에라토스테네스의 체, 모든 소수 찾는 방법, Prime Number]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## 에라토스테네스의 체

* 모든 소수(Prime number)를 찾는 방법
* 자기와 자기 자신만이 나눠질 수 있는 수

```python
def SieveOfEratosthenes(n):
    # Create a boolean array => prime배열(True로 초기화)
    # prime배열이 True면 소수 출력, False면 출력X
    
    prime = [True for i in range(n+1)]
    p=2 # 1은 소수가 아니라서 2부터 시작
    while(p*p <= n): 
        if(prime[p] == True): # 소수아닌것들 False로 만드는 작업시작
            for i in range(p*p, n+1, p):
                prime[i] = False
        p += 1
    
    for p in range(2, n+1):
        if prime[p]: # True라면
            print(p) # 출력

if __name__ == '__main__': # main함수인것이다.
    n=100
    SieveOfEratosthenes(n)
```

```python
# 핵심코드는 이부분이다. 이부분이 소수를 구하는 과정이다.
while(p*p <= n): 
    if(prime[p] == True): # 소수아닌것들 False로 만드는 작업시작
        for i in range(p*p, n+1, p):
            prime[i] = False
    p += 1
```