---
title:  "[Python] Basic(기초문법) 재정리 - 최신ver(2)"
categories : PY
tag : [파이썬, 문법]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



**Python의 클래스와 데이터 분석을 위한 Class, NumPy, Pandas 을 정리한다.**

<br>

<br>

## 클래스(Class) 기본 구조  🔍

**버전 별 나누기 연산 바뀐점 참고**

- 파이썬 2에서는 나누기 연산을 위해 `__div__` 메서드를 사용했지만, 파이썬 3부터는 `__truediv__`를 사용!

  ```python
  class MyNumber:
      def __init__(self, value):
          self.value = value
      
      # 파이썬 3에서의 나누기 연산자 메서드
      def __truediv__(self, other):
          return self.value / other.value
  ```

<br>

**클래스의 기본 구성 요소**

- **생성자** (`__init__`): 객체가 생성될 때 호출되는 메서드로, 초기화를 담당

  ```python
  class Car:
      def __init__(self, color, speed=0):
          self.color = color        # 인스턴스 변수
          self.speed = speed
  ```

- **인스턴스 변수**: 각 객체마다 별도로 유지되는 변수로, `self`를 통해 접근

  ```python
  def upSpeed(self, value):
      self.speed += value
  ```

<br>

**특수 메서드와 속성**

- `__dict__`: 객체의 속성 정보를 딕셔너리 형태로 반환

  ```python
  car = Car('red')
  print(car.__dict__)
  # 출력: {'color': 'red', 'speed': 0}
  ```

- `__str__`: 객체를 문자열로 표현할 때 사용하는 메서드

  ```python
  class Car:
      # ...
      def __str__(self):
          return f"Car(color={self.color}, speed={self.speed})"
  
  print(car)
  # 출력: Car(color=red, speed=0)
  ```

<br>

**접근 제어**

- **프라이빗 변수**: 변수 이름 앞에 `__`를 붙여서 사용하며, 클래스 외부에서 접근할 수 없다.

  ```python
  class BankAccount:
      def __init__(self, balance):
          self.__balance = balance  # 프라이빗 변수
  
      def deposit(self, amount):
          self.__balance += amount
  ```

<br>

**상속과** `super()`

- 자식 클래스에서 부모 클래스의 생성자나 메서드를 호출할 때 `super()`를 사용

- 상속은 괄호안에 넣어서 적용

  ```python
  class Vehicle:
      def __init__(self, make, model):
          self.make = make
          self.model = model
  
  class Truck(Vehicle):
      def __init__(self, make, model, capacity):
          super().__init__(make, model)  # 부모 클래스의 생성자 호출
          self.capacity = capacity
  ```

  - 만약 자식 클래스에서 생성자를 정의하지 않으면 부모 클래스의 생성자가 자동으로 호출됩니다.

<br>

**불변 객체와 문자열**

- 파이썬에서 문자열은 **불변 객체**로써 생성된 후 값을 변경할 수 없다.

- 동일한 문자열 리터럴은 메모리 절약을 위해 같은 객체를 참조

  ```python
  a = "hello"
  b = "hello"
  print(a is b)  # 출력: True
  ```

<br>

<br>

## NumPy

`ndarray`**: NumPy의 핵심 자료구조**

- `ndarray`는 N차원 배열 객체로, 동일한 타입의 요소로 구성

- **리스트와 유사하지만, 더 빠르고** 메모리를 효율적으로 사용하며, 벡터화 연산을 지원

  ```python
  import numpy as np
  
  arr = np.array([1, 2, 3])
  ```

<br>

`ndarray`**의 주요 속성**

- `shape`: 배열의 차원 크기를 튜플로 반환

- `ndim`: 배열의 차원 수를 반환

- `dtype`: 배열 요소의 데이터 타입을 반환

- `itemsize`: 배열 요소 하나의 바이트 크기를 반환

- `size`: 배열 전체 요소의 개수를 반환

  ```python
  arr = np.array([[1, 2], [3, 4]])
  print(arr.shape)     # 출력: (2, 2)
  print(arr.ndim)      # 출력: 2
  print(arr.dtype)     # 출력: int64 (시스템에 따라 다를 수 있음)
  print(arr.itemsize)  # 출력: 8 (시스템에 따라 다를 수 있음)
  print(arr.size)      # 출력: 4
  ```

<br>

**배열 생성**

- `np.array()`: 리스트나 튜플을 배열로 변환

- `np.arange()`: 지정된 범위의 수열을 생성

- `np.linspace()`: 시작과 끝을 지정하여 일정한 간격으로 수열을 생성

- `np.logspace()`: 로그 스케일로 수열을 생성

  ```python
  np.array([1, 2, 3])            # array([1, 2, 3])
  np.arange(0, 3)                # array([0, 1, 2])
  np.linspace(0, 10, 5)          # array([ 0. ,  2.5,  5. ,  7.5, 10. ])
  np.logspace(0, 2, 5)           # array([  1. ,   3.16227766,  10. ,  31.6227766, 100. ])
  ```

<br>

**배열 형태 변경**

- `reshape()`: 배열의 형태를 변경

- `flatten()`: 다차원 배열을 1차원으로 평탄화

  ```python
  arr = np.arange(10).reshape(2, 5)
  print(arr)
  # 출력:
  # [[0 1 2 3 4]
  #  [5 6 7 8 9]]
  
  flat_arr = arr.flatten()
  print(flat_arr)
  # 출력: [0 1 2 3 4 5 6 7 8 9]
  ```

<br>

**인덱싱과 슬라이싱**

- **기본 인덱싱**: 리스트와 유사하게 인덱스를 사용

- **슬라이싱**: `[start:stop:step]` 형식으로 부분 배열을 가져옴

- **다차원 인덱싱**: 쉼표를 사용하여 각 차원에 대한 인덱스를 지정

  ```python
  arr = np.arange(10).reshape(2, 5)
  
  # 요소 접근
  print(arr[0, 2])  # 출력: 2
  
  # 슬라이싱
  print(arr[:, 1:3])
  # 출력:
  # [[1 2]
  #  [6 7]]
  ```

<br>

**논리적 인덱싱**

- 조건을 만족하는 요소를 선택할 수 있다.

  ```python
  ages = np.array([18, 25, 30, 21])
  adults = ages[ages >= 20]
  print(adults)  # 출력: [25 30 21]
  ```

<br>

<br>

## Pandas

**Pandas의 핵심 자료구조**

- `Series`: 1차원 배열로, 인덱스를 가짐

  ```python
  import pandas as pd
  
  s = pd.Series([1, 2, 3], index=['a', 'b', 'c'])
  print(s)
  # 출력:
  # a    1
  # b    2
  # c    3
  # dtype: int64
  ```

- `DataFrame`: 2차원 데이터 구조로, 행과 열에 인덱스를 가짐

  ```python
  data = {
      'name': ['Alice', 'Bob', 'Charlie'],
      'age': [25, 30, 35]
  }
  df = pd.DataFrame(data)
  print(df)
  # 출력:
  #       name  age
  # 0    Alice   25
  # 1      Bob   30
  # 2  Charlie   35
  ```

<br>

**데이터 불러오기**

- **CSV 파일 읽기**

  ```python
  df = pd.read_csv('data.csv')
  ```

- **인덱스 컬럼 지정하기**

  ```python
  df = pd.read_csv('data.csv', index_col=0)
  ```

<br>

**데이터 선택**

- **열 선택**

  ```python
  df['age']          # Series 반환
  df[['name', 'age']]  # DataFrame 반환
  ```

- **행 선택**

  ```python
  df[0:2]            # 슬라이싱으로 행 선택
  df.head(2)         # 위에서부터 2개 행 선택
  ```

- `loc`**와** `iloc`

  - `loc`: 라벨 기반 인덱싱

    ```python
    df.loc[0, 'name']       # 첫 번째 행의 'name' 열 값
    df.loc[:, 'age']        # 모든 행의 'age' 열
    ```

  - `iloc`: 정수 기반 인덱싱

    ```python
    df.iloc[0, 1]           # 첫 번째 행의 두 번째 열 값
    df.iloc[:, 0]           # 모든 행의 첫 번째 열
    ```

<br>

**데이터 추가**

- **열 추가**

  ```python
  df['salary'] = [50000, 60000, 70000]
  ```

<br>

**데이터 분석**

- **기본 통계 정보**

  ```python
  df.describe()
  # count, mean, std, min, max 등의 정보를 제공
  ```

- **표준편차 계산 시 유의사항**

  - Pandas의 `std()`는 **베셀 보정**을 적용하여 **자유도(n-1)**를 사용합니다.

  - NumPy의 `np.std()`는 기본적으로 **n**을 사용합니다.

    ```python
    df['age'].std()                 # Pandas 표준편차
    np.std(df['age'])               # NumPy 표준편차
    ```

<br>

**날짜 데이터 다루기**

- **날짜 형식 변환**

  ```python
  df['date'] = pd.to_datetime(df['date'])
  ```

- **날짜 관련 정보 추출**

  ```python
  df['year'] = df['date'].dt.year
  df['month'] = df['date'].dt.month
  df['day'] = df['date'].dt.day
  ```

<br>

**데이터 그룹핑**

- `groupby()`

  ```python
  grouped = df.groupby('month').mean()
  ```

- **최대 또는 최소 값 찾기**

  ```python
  max_value = grouped['sales'].max()
  max_month = grouped[grouped['sales'] == max_value]
  ```

<br>

**데이터 필터링**

- **조건을 사용한 필터링**

  ```python
  high_salary = df[df['salary'] > 60000]
  ```

<br>

**결손값 처리**

- **결손값 확인**

  ```python
  df.isna()                  # 결손값 여부를 True/False로 반환
  df.isna().sum()            # 결손값 개수 확인
  ```

- **결손값 제거**

  ```python
  df.dropna(axis=0, how='any', inplace=True)  # 결손값 포함된 행 제거
  ```

- **결손값 대체**

  ```python
  df['salary'].fillna(df['salary'].mean(), inplace=True)  # 평균값으로 대체
  ```

  - 파라미터 설명
    - `axis`: 축 지정 (`0`은 행, `1`은 열)
    - `how`: 제거 조건 (`'any'`는 하나라도 결손값이면 제거, `'all'`은 모두 결손값일 때 제거)
    - `inplace`: 원본 데이터프레임을 수정할지 여부

<br>

**데이터 구조 변경**

- **피벗 테이블**

  ```python
  pivot_df = df.pivot(index='item', columns='type', values='price')
  ```

  - `index`: 행 인덱스로 사용할 열
  - `columns`: 열 이름으로 사용할 열
  - `values`: 값으로 사용할 열

<br>

**데이터 합치기**

- `concat()`

  ```python
  combined_df = pd.concat([df1, df2], axis=0, join='outer')
  ```

  - `axis`: 합칠 축 지정 (`0`은 행 방향, `1`은 열 방향)
  - `join`: 조인 방식 (`'outer'`, `'inner'`)

- `merge()`

  ```python
  merged_df = df1.merge(df2, how='inner', on='key')
  ```

  - `how`: 조인 방식 (`'inner'`, `'outer'`, `'left'`, `'right'`)
  - `on`: 공통 열 이름

<br>

**데이터 정렬**

- `sort_values()`

  ```python
  df_sorted = df.sort_values(by='age', ascending=True)  # 오름차순 정렬
  df_sorted = df.sort_values(by='salary', ascending=False)  # 내림차순 정렬
  ```

