---
title:  "[Python]Recursion(재귀)"
categories : Algorithm
tag : [Factorials(팩토리얼), Recursion, 재귀, 스택구조, Fibonacci(피보나치), Towers of Hanoi(하노이 탑), Exponential Operation(지수 함수), Knapsack Problem, N Queen Problem]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## Recursion(재귀)

* Recursion은 말 그대로 재귀하는 형태로 생각하면 된다. 스택구조를 가진다.
* 활용 예시 : 팩토리얼 등

<br>

### 1. Factorials(팩토리얼)

* 다만 재귀를하면 스택이 쌓이므로, 메모리에 효율적이지 못하다.

```python
def factorial(n):
    if n <= 1:
        return 1
    else:
        return n*factorial(n-1) # 핵심은 이부분이다.
    
print(factorial(4))
```

<br>

#### 1-1. Factorials(For문으로 구현법)

* 재귀를 하는게 아니라서, 메모리에 효율적이다.

```python
factorial = 1
for i in range(1, 5): # factorial(4)를 의미
    factorial *=1

print(factorial)
```

<br>

### 2. Fibonacci(피보나치)

* 피보나치 수열 : 1,1,2,3,4,5,13,21,,,
* 조건
  * fib(n-1) + fib(n-2), **if n > 1**
  * n, **if n = 1 or n = 0**

```python
def fib(n):
    # assert n >= 1, "1미만은 피보나치 정의가 되지 않음."
    if n == 1:
        return 1
    elif n == 0:
        return 0
    else:
        return fib(n-1) + fib(n-2)
    
print(fib(6)) # 6번째 피보나치 수열값은 : 8출력
```

<br>

### 3. Towers of Hanoi(하노이 탑)

* 유명한 문제. 해결법 또한 다양하기 때문에 인터넷 검색해서 알아 볼것

```python
# 하노이 탑
def move(n, src, dest, temp):
    if n >= 1:
        move(n-1, src, temp, dest)
        print("Move %d -> %d" % (src, dest))
        move(n-1, temp, dest, src)

move(3,1,3,2)
'''Move 1 -> 3
Move 1 -> 2
Move 3 -> 2
Move 1 -> 3
Move 2 -> 1
Move 2 -> 3
Move 1 -> 3'''
```

<br>

### 4. Exponential Operation(지수 함수) - O(N)

```python
def exp(x, n):
    y=1
    for i in range(n):
        y *= x
    return y

print(exp(2, 5))
```

<br>

#### 4-4. Exponential Operation(For문으로 구현법) - O(logN)

```python
# 지수 함수 - Fast way(더욱 성능이 좋다)
def exp(x, n):
    if n == 0 :
        return 1
    result = exp(x*x, n//2)
    if n%2 == 0 :
        return result # 짝수
    else :
        return x*result # 홀수

print(exp(2, 5)) # 32
```

<br>

### 5. Knapsack Problem - O(2<sup>2</sup>)

* 도둑이 보석가게에 배낭을 메고 침입했다.  
  배낭의 최대 용량은 W이며, 이를 초과해서 보석을 담으면 배낭이 찢어질 것이다.  
  각 보석들의 무게와 가격은 알고 있다.  
  배낭이 찢어지지 않는 선에서 가격 합이 최대가 되도록 보석을 담는 방법은?  
  => 아래 코드에서는 답이 19 값
* **모든 경우의 수를 넣어본다 (Brute-Force)**

```python
def knapsack(W, wt, val, n):
    # Base Case
    if n == 0 or W == 0:
        return 0

    if(wt[n-1] > W):
        return knapsack(W, wt, val, n-1)

    else:
        return max(
            val[n-1] + knapsack(W-wt[n-1], wt, val, n-1), knapsack(W, wt, val, n-1)
            )

values = [3, 7, 2, 9]
weights = [2, 2, 4, 5]
W = 10
n = len(values)
print(knapsack(W, weights, values, n)) # 19 출력
```

<br>

#### 5-1. Knapsack Problem(For문으로 구현법) - O(nw)

* 이녀석이 더 성능이 좋다. 코드는 인터넷 검색바람.

<br>

### 6. N Queen Problem

N-Queen 문제는 `N x N` 체스판 위에 `N`개의 퀸을 서로 공격하지 않도록 배치하는 문제. 즉, 어떤 두 퀸도 같은 행, 열, 또는 대각선에 위치할 수 없음.

* 안전한 위치가 없다면 **백 트래킹(되돌아 간다)**를 활용, 이 또한 recursion형식이며, 코드는 인터넷 검색바람.

<br>

#### 6-1. N Queen Problem(For문인지? 동적으로 최적화 하는 방법)

* 이녀석이 더 성능이 좋으며, 코드는 인터넷 검색바람.



