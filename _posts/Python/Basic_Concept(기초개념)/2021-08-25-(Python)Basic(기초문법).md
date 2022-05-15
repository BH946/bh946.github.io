---
title:  "[Python]Basic(기초문법)"
categories : Basic_Concept_PY
tag : [파이썬, 문법]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---


# 파이썬(Python)


## 문법정리

* Tab으로 들여쓰기 하는부분 꼭 잘 확인.(이 부분으로 인해 에러 발생하는 경우도 많음)
* \ 를 입력해서 다음줄에 이어서 코드작성 가능

### 2-1. 숫자 자료형

```python
# print() : 출력 및 '\n'이 들어가 있음. (=자동 줄바꿈)
print(5)
print(5+3) : 연산도 가능
```

### 2-2. 문자 자료형

```python
print('풍선')
print("나비") -> 둘다 가능
print("ㅋ"*9) : ㅋ 9번출력
```

### 2-3. boolean 자료형

```python
print(5>10) : false
print(not True) : false
```

### 2-4. 변수 (+말고 , 도 사용가능하고 ,사용시 str필요x)

```python
animal ="강아지"
name = "연탄이"
age = 4
hobby = "산책"
is_adult = age >= 3 
print("우리집 " + animal + "의 이름은 " + name + "예요")
print(name + "는 " + str(age) + "살이며, " + hobby + "을 아주 좋아해요")
print(name + "는 오른일까요? " + str(is_adult)) 
```

### 2-5. 주석(#),('''),('''),(Ctrl + '/') 

```python
# 주석
''' 주석
'''
# 단축기 : Ctrl+'/'
```



### 3-1. 연산자

```python
2**2 : 2의 2승
5%3 : 나머지 구하기 2
5//3 : 몫 구하기 1
3==3 : true
4==2 : false
1!=3 : true(!는 not생각)
and = & : 둘다 사용 가능
or = | : 둘다 사용 가능 
```

### 3-2. 간단한 수식

```python
변수 선언 num으로 했다가정
num = num + 2
num += 2 : 둘다 같음 
```

### 3-3. 숫자처리 함수

```python
from math import * // 수학math 라이브러리 가져오기
floor, ceil, sqrt : 내림, 올림, 제곱근
abs :절대값
pow(4,2) : 4의 2승
max : 최대값, min : 최소값, round : 반올림 
```

### 3-4. 랜덤함수 : randint(1, 45)

```python
from random import *
random() : 0.0 ~ 1.0 미만의 임의의 값 생성
random() * 10 : 0.0 ~ 10.0 미만의 임의의 값 생성
int(random() *10) : 0 ~ 10 미만의 임의의 값 생성
int(random() *10)+1 : 1 ~ 10 이하의 임의의 값생성
randrange(1, 46) : 1~46미만 임의 값 생성
randint(1, 45) : 1~45이하 임의 값 생성 
```

### 3-5. 퀴즈

```python
조건 1 : 랜덤 날짜 
조건 2 : 월별 28일 이내 
조건 3 : 매월 1~3일 제외
from random import *
date = randint(4,28)
print("오프라인 스터디 모임 날짜는 매월 ",str(date) ," 일로 선정되었습니다.")
```



### 4-1. 문자열 ('),(")

```python
변수 = """
나는 소년이고,
파이썬은 쉬워요
"""
print(변수) : 쌍따 줄바꿈이고 문자 잘나옴 => """하고 엔터치고 문자입력해도 된다는 의미.
```

### 4-2. 슬라이싱 (원하는 부분만 가져오기)

```python
주민="990909-1234567"
print("성별 : " + 주민[7]) : 7번째 값 가져옴(그러나 index는 0부터 세니까 실제론 8번째 값)
print("연 : " + 주민[0:2]) : 0부터 2직전까지(0,1)
print("뒤 7자리 (뒤에부터) : " + 주민[-7:]) 
```

### 4-3. 문자열 처리함수

```python
파이썬 = "Python is Amazing"
print(파이썬.lower()) : 소문자로 나타냄
print(파이썬.upper()) : 대문자로 나타냄
print(파이썬[0].isupper()) : 0번째 대문자냐? true
print(len(파이썬)) : 문자길이
print(파이썬.replace("Python", "Java")) : 대체해줌 

index = 파이썬.index("n") : index값 구해줌
print(index) : 값 5
index = 파이썬.index("n", index +1) : 뒤에꺼 스타트지점
print(index) : 15
-> find함수도 가능. 차이점은 값 없을시
print(파이썬.find("Java")) : 값 -1 나옴
print(파이썬.index("Java")) : 오류뜸
-> 마지막 count는 문자가 몇번나왔는지 개수! 
```

### 4-4. 문자열 포맷

```python
+와 , 말고도 다양한 문자열
print("나는 %d살입니다." % 20) : 정수
print("나는 %s살입니다." % "파이썬") : 스트링. 문자
print("나는 %c살입니다." % "A") : 캐릭터 문자
print("나는 %s색과 %s색을 좋아해요." % ("파란", "빨간")) 

print("나는 {}살입니다.".format(20))
print("나는 {}색과 {}색을 좋아해요.".format("파란","빨간"))
print("나는 {0}색과 {1}색을 좋아해요.".format("파란","빨간")) 
print("나는 {age}살이며, {color}색을 좋아해요.".format(age = 20,color = "빨간")) 

파이썬 버전 3.6이상~~!!! (추천!)
age =20
color ="빨간"
print(f"나는 {age}살이며, {color}색을 좋아해요.") 
```

### 4-5. 탈출문자

```python
\n : 줄바꿈
"백문이 불여일견\n백견이 불여일타"
"저는 \"나도코딩\"입니다." : 쌍따를 문자로 넣음 
\\ : 문장 내에서 \

\r : 커서를 맨앞으로 이동
"red apple\rpine" : pineapple

\b : 백스페에스 (한 글자 삭제)
"redd\bapple" : redapple

\t : 탭
"red\tapple" : red   apple 
```

### 4-6. 퀴즈() 

```python
사이트별로 비밀번호 만들기
http://naver.com
조건 1 : http:// 제외
조건 2 : 처음 만나는 점(.) 이후 부분 제외
조건 3 : 남은 글자 중 처음 세자리 + 글자개수 + 글자 내
'e' 갯수 + "!" 로 구성

site = "http://naver.com"
# site.replace("http://", "") # 이걸로 해도 된다는 의미.
site_1 = site[7:]
site_2 = site_1[0:site_1.index(".")]
password = site_2[0:3] + str(len(site_2)) + str(site_2.count("e")) + "!"
# 문자로 합치기 위해서는 str로 감싸줘야함(각자 데이터형식 다를 수 있으니까)
print(password)
```



### 5-1. 리스트(LIST) : []

```python
# 선언
subway = list()
subway = []

# 자료넣기
subway = [10,20,30]

# append() => 뒤에 삽입되는 특징
subway.append("조세호")

# insert() => 10과 20사이에 정형돈씨 넣기
subway.insert(1,"정형돈") # index 1에 "정형돈" 삽입

# pop() => 뒤에서 부터 빠지는 특징
subway.pop()

# 정렬
numlist = [5,2,4,3,1]
numlist.sort()

# 순서 뒤집기
numlist.reverse() # numlist가 뒤집혀 진거임.

# 모두 지우기
numlist.clear()

# 다양한 자료형 함께사용
mixlist = ["조세호",20,True]

#리스트 확장
numlist.extend(mixlist) # 두 리스트를 합친거임(numlist가 합쳐짐)
```

### 5-2. 사전(DICT) : {}

```python
cab = {3:"유재석"} ; 앞은 키값이고 3키 = 유재석
print(cab[3]) ; 유재석이 나옴
print(cab.get(3)) ; 똑같음
print(cab[5]) ; 오류 발생 및 종료
print(cab.get(5)) ; none이 나옴(위와 차이는 오류X)
print(cab.get(5, "사용 가능")) ; 사용가능이 나옴
print(3 in cab) ; true값 반환 

#새손님
print(cab)
cab[3] = "김종국" # 유재석에서 김종국으로
cab["A-20"] = "조세호" # A-20:조세호 로 추가(cab에)

#간 손님
del cab[3] # 김종국이 사라지겠죠

# key들만 출력
print(cab.keys())

#value들만 출력
print(cab.values())

#key,value 쌍으로 출력
print(cab.items())

#목욕탕 폐점
cab.clear() 
```

### 5-3. 튜플(TUPLE) : ()

```python
### 리스트와 다르게 내용변경 추가 못함 그러나 속도가 빠름 리스트보다

menu = ("돈까스","치즈까스")
print(menu) # ('돈까스', '치즈까스')
print(menu[0]) # 돈까스 

(name,age,hobby) = ("김종국",20,"코딩")
print(name) # 김종국
print(name,age,hobby) # 김종국 20 코딩
```

### 5-4 세트(set 집합) : {}

```python
### 중복 불가, 순서없음

my= {1,2,3,3,3} # set 형식으로 저장
print(my) # {1,2,3,3,3}

ja= {"유재석","김태호","양세형"}
py=set(["유재석","박명수"]) # 리스트의 중복제거에 사용할 수 있겠죠?

# 교집합
print(ja & py)
print(ja.intersection(py))

#합집합
print(ja | py)
print(ja.union(py))

#차집합
print(ja - py)
print(ja.difference(py))

#파이썬 할줄아는사람 늘어남
py.add("김태호") # 뒤에 추가
print(py)

#java 까먹음
ja.remove("김태호")
print(ja) 
```

### 5-5. 자료구조의 변경

```python
#커피숍
menu = {"커피","우유","주스"}
print(menu,type(menu)) ; type이 set으로나옴 
menu = list(menu)
print(menu,type(menu)) ; type이 list으로나옴 
menu = tuple(menu)
print(menu,type(menu)) ; type이 tuple으로나옴 
menu = set(menu)
print(menu,type(menu)) ; type이 set으로나옴 
```

### 5-6.퀴즈 () => random 모듈 활용

````python
추첨 프로그램 ( 1명 치킨, 3명 커피)
조건 1 : 20명이고 아이디 1~20 이라고 가정
조건 2 : 무작위 추첨하되 중복 불가
조건 3 : random 모듈의 shuffle 과 sample 을 활용
# 처음엔 randint 활용해보려고 했음.

from random import *
lst = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
shuffle(lst) # 랜덤으로 섞어줌

lst_no=sample(lst,4) # 중복 X => 4개를 sample로 뽑아오는 함수임!!
print(lst_no[0]) # 치킨 당첨자
print(lst_no[1:]) # 커피 당첨자
````



### 6-1.if문

```python
weather = input("오늘 날씨는 어때요? ") => C언어의 scanf같은 것임.
if weather == "비" or weather == "눈": # 자바와 다르게 바로 "=="으로 비교 가능~!
  print("우산을 챙기세요")
elif weather == "미세먼지": # elif 인점 체크.
  print("마스크를 챙기세요")
else:
  print("준비물 필요 없어요.")

temp = int(input("기온은 어때요? "))
if 30 <= temp:
  print("너무 더워요, 나가지 마세요")
elif 10 <= temp and temp < 30:
  print("괜찮은 날씨에요")
elif 0 <= temp and temp < 10:
  print("외투를 챙기세요")
else:
  print("너무 추워요. 나가지 마세요")
```

### 6-2.for문

```python
print("대기번호 : 1")
print("대기번호 : 2")
print("대기번호 : 3")
print("대기번호 : 4")

# 두가지 유형확인
for watino in range(1,6): # 1~5
  print("대기번호 : {0}".format(watino))

star = ["아이언맨", "토르", "아이엠 그루트"]
for cutomer in star:
  print("{0}, 커피가 준비되었습니다.".format(cutomer))
```

### 6-3.while문

```python
customer = "토르"
index = 5

while index >= 1:
  print("{0}, 커피가 준비 되었습니다. {1} 번 남았어요.".format(customer,index))
  index -= 1
  if index == 0:
    print("커피는 폐기처분되었습니다.")

customer = "아이언맨"
index = 1

while True:
  print("{0}, 커피가 준비 되었습니다. 호출 {1} 회".format(customer,index))
  index += 1

**ctrl + C 누르면 강제종료**

customer = "토르"
person = "Unknown"

while person != customer :
  print("{0}, 커피가 준비 되었습니다.".format(customer))
  person = input("이름이 어떻게 되세요? ")
```

### 6-4 continue(다음반복으로 감) 와 break(바로나감)

```python
absent = [2,5] # 결석
nobook = [7] # 책을 깜빡했음

for student in range(1, 11):
  if student in absent:
    continue
  elif student in nobook:
    print("오늘 수업 여기까지. {0}는 교무실로 따라와".format(student))
    break
  print("{0}, 책을 읽어봐".format(student))
```

### 6-5 한줄 for

```python
# 출석번호가 1 2 3 4, 앞에 100을 붙이기로 함 -> 101, 102, 103, 104.
 students = [1,2,3,4,5]
 print(students)
 students = [i+100 for i in students]
 print(students)

# 학생 이름을 길이로 변환
students = ["Iron man", "Thor", "I am groot"]
students = [len(i) for i in students]
print(students)

# 학생 이름을 대문자로 변환
students = ["Iron man", "Thor", "I am groot"]
students = [i.upper() for i in students]
print(students)
```

### 6-6.퀴즈 ()

```python
50명의 승객과 매칭 기회, 총 탑승 승객 수?
조건 1 : 승객별 운행 시간 5분 ~ 50분 사이의 난수
조건 2 : 당신의 소요 시간 5분 ~ 15분 사이의 승객만 매칭

(출력문 예제)
[O] 1번째 손님 (소요시간 : 15분)
[ ] 2번째 손님 (소요시간 : 50분)
...
[ ] 50번째 손님 (소요시간 : 16분)
총 탑승 승객 : 2 분

# 답안
from random import *
count = 0

for i in range(1,51):
  time = randint(5,50)
  if time >= 5 and time <= 15:
    count += 1
    print("[O] {0}번째 손님 (소요시간 : {1}분)".format(i,time))
  else:
    print("[ ] {0}번째 손님 (소요시간 : {1}분)".format(i,time))
print("총 탑승 승객 : {0} 분".format(count))
```



### 7-1.함수

```python
def open_account():
  print("새로운 계좌가 생성되었습니다.")
```

### 7-2.전달값과 반환값 : 매개변수, 인수, return

* 매개변수 : 함수 호출할 때 전달받은 인수를 매개변수(파라미터)로 함수내에서 사용
* 인수 : 함수 호출할 때 전달하는 인수(argument)
* return : 함수의 반환을 의미

```python
# 함수 선언
def deposit(balance, money): # 입금 : balance, money는 매개변수
  print("입금이 완료되었습니다. 잔액은 {0} 원입니다.".format(balance + money))
  return balance + money # 반환값

def withdraw(balance, money): # 출금
  if balance >= money:
    print("출금이 완료되었습니다. 잔액은 {0} 원입니다.".format(balance - money))
    return balance - money
  else:
    print("출금이 완료되지 않았습니다. 잔액은 {0} 원입니다.".format(balance))
    return balance
    
##### return 2개이상 가능
def withdraw_night(balance, money): # 저녁에 출금
  commission = 100 # 수수료 100원
  return commission, balance - money - commission

# 함수 호출
balance = 0 # 잔액
balance = deposit(balance, 1000) # balance, 1000은 인수
print(balance)
balance = withdraw(balance, 500)
commission, balance = withdraw_night(balance, 500)
print("수수료 {0} 원이며, 잔액은 {1} 원입니다.".format(commission, balance))
```

### 7-3.기본값 : 매개변수를 기본값 설정

```python
def profile(name, age, main_lang):
   print("이름 : {0}\t나이 : {1}\t주 사용 언어 : {2}" \
     .format(name,age,main_lang))

profile("유재석", 20, "파이썬")
profile("김태호", 25, "자바")

# 같은 학교 같은 학년 같은 반 같은 수업.
# 기본값!!
def profile(name, age=17, main_lang="파이썬"):
  print("이름 : {0}\t나이 : {1}\t주 사용 언어 : {2}" \
    .format(name,age,main_lang))

profile("유재석")
profile("김태호")
```

### 7-4.키워드값

* 키워드=값 형식으로 사용.
  * 장점 : 가독성 상승, 함수의 매개변수에 넣어줄 인자를 순서대로 안해도 됨.
  * 즉, profile()함수의 main_lang매개변수를 main_lang="파이썬" 먼저 넣어도 된다는 의미.

```python
def profile(name, age, main_lang):
  print(name, age, main_lang)
  
profile(name="유재석", main_lang="파이썬", age=20) # 키워드값 사용은 이렇게 순서 상관X
```

### 7-5.가변인자 -> *language로 lang갯수 상관 X!!

```python
def profile(name, age, lang1, lang2, lang3, lang4, lang5):
  print("이름 : {0}\t나이 : {1}\t".format(name,age), end=" ")
  print(lang1, lang2, lang3, lang4, lang5)

def profile(name, age, *language): # 가변인자!!!
  print("이름 : {0}\t나이 : {1}\t".format(name,age), end=" ")
  for lang in language:
    print(lang, end=" ") # 줄바꿈 안함
  print() # 줄바꿈

profile("유재석", 20, "Python", "Java", "C", "C++", "C#", "JavaScript")
profile("김태호", 20, "Python", "Java")
```

### 7-6.지역변수와 전역변수

```python
gun = 10 # 이녀석을 전역으로 쓴거임.

def checkpoint(soldiers): # 경계근무
  # gun = 20 # 지역변수!!!
  global gun # 전역 공간에 있는 gun 사용!!!
  gun = gun - soldiers
  print("[함수 내] 남은 총 : {0}".format(gun))

def checkpoint_ret(gun, soldiers):
  gun = gun - soldiers
  print("[함수 내] 남은 총 : {0}".format(gun))
  return gun # 실질적으론 이런식으로 사용하자! gun을 외부로 보내준것임

print("전체 총 : {0}".format(gun))
checkpoint(2) # 2명이 경계근무 나감
gun = checkpoint_ret(gun, 2) # 실제로 쳌(10,2)라는 것.
print("남은 총 : {0}".format(gun))
```

### 7-7.퀴즈()

```python
표준 체중 프로그램
(성별 따른 공식)
남자 : 키(m) x 키(m) x 22
여자 : 키(m) x 키(m) x 21
조건 1 : 표준 체중은 별도 함수 내에서 계산
      \* 함수명 : std_weight
      \* 전달값 : 키(height), 성별(gender)
조건 2 : 표준 체중은 소수점 둘째자리까지 표시

(출력 예제)
키 175cm 남자의 표준 체중은 67.38kg 입니다.

# 답안
def std_weight(height, gender): # 키 m 단위(실수), 성별
  if gender == "남자":
    return height * height * 22
  else:
    return height * height * 21 

height = 175 # cm 단위
gender = "남자"
weight = round(std_weight(height / 100, gender), 2) # 소수점
print("키 {0}cm {1}의 표준 체중은 {2}kg 입니다.".format(height,gender,weight))
```



### 8-1.표준입출력

* print내부에 콤마(,)로 구분시 한칸 자동으로 띄어서 구분함.
  * sep를 사용해 콤마부분 수정가능.
* print만 입력시 자동으로 한줄 넘어감(끝에 자동으로 '\n' 들어가는 특징때문임)
  * end를 사용해 문장끝부분 수정가능.(한줄 넘어가는걸 막을 수 있는것.)
* print내부에 +로 문자열 합칠시 말 그대로 둘이 합침. (보충)
  * ljust, rjust 메소드(보충) : 공간 확보 후 왼쪽정렬, 오른쪽정렬
  * zfill 메소드(보충) : 공간 확보 후 값 없는곳은 0표시

```python
import sys
print("Python" ,"java", file=sys.stdout)
print("Python" ,"java", file=sys.stderr) # 에러처리 가능
print("Python" ,"java", sep=",", end="?")
# sep를 사용해 콤마부분 입력, end를 사용해 문장끝부분 입력

scores = {"수학":0, "영어":50, "코딩":100} # 앞: key 뒤: value
for subject, score in scores.items(): # items는 key, value 받아줌
  print(subject.ljust(8),str(score).rjust(4), sep=":") # ljust(8)은 왼쪽8칸 할당 후 정렬

# 은행 대기순번표
for num in range(1, 21):
  print("대기번호 : " + str(num).zfill(3)) # 3공간확보 값없는곳 0표시

answer = input("아무 값이나 입력하세요 : ")
answer = 10
print(type(answer))
print("입력하신 값은 " + answer + "입니다.")
사용자 입력을 받은후 출력하면 무조건 문자열(str)형태!!
```

### 8-2.다양한 출력포맷

```python
# 빈 자리는 빈공간으로 두고, 오른쪽 정렬 하되, 총 10자리 공간 확보
print("{0: >10}".format(500)) #               500

# 양수일 땐 +로 표시, 음수일 땐 -로 표시
print("{0: >+10}".format(500))
print("{0: >+10}".format(-500))

# 왼쪽 정렬하고, 빈칸으로 _로 채움
print("{0:_<10}".format(500))

# 3자리 마다 콤마를 찍어주기
print("{0:,}".format(1000000000))

# 3자리 마다 콤마를 찍어주기, +- 부호도 붙이기
print("{0:+,}".format(1000000000))
print("{0:+,}".format(-1000000000))

# 3자리 마다 콤마를 찍어주기, 부호도 붙이고, 자릿수 확보하기
# 돈이 많으면 행복하니까 빈 자리는 ^로 채워주기
print("{0:^<+30,}".format(100000000000))

# 소수점 출력
print("{0:f}".format(5/3))
# 소수점 특정 자리수 까지만 표시
print("{0:.2f}".format(5/3))
```

### 8-3.파일입출력

```python
#파일 쓰기
score_file = open("score.txt", "w", encoding="utf8") # 쓰기목적으로 open
print("수학 : 0", file=score_file)
print("영어 : 50", file=score_file)
score_file.close() # 열었으면 닫아줘야함

#파일 내용 추가
score_file = open("score.txt", "a", encoding="utf8") # append의미
score_file.write("과학 : 80")
score_file.write("\n코딩 : 100")
score_file.close()

#파일 읽기
score_file = open("score.txt", "r", encoding="utf8") # reading 뜻
print(score_file.read())
score_file.close()

#파일 한줄 읽기
score_file = open("score.txt", "r", encoding="utf8") # reading 뜻
print(score_file.readline()) # 한줄만 읽어와서 커서를 다음줄로 이동
print(score_file.readline())
print(score_file.readline())
print(score_file.readline())
score_file.close()

#파일 전체 읽기
score_file = open("score.txt", "r", encoding="utf8") 
while True:
  line = score_file.readline()
  if not line:
    break
  print(line, end="")
score_file.close()

#파일 전체 읽기 - 리스트에 값 넣어서 처리 
score_file = open("score.txt", "r", encoding="utf8")
lines = score_file.readlines() # list 형태로 저장
for line in lines:
  print(line, end="")
score_file.close()
```

### 8-4.pickle

```python
import pickle => pickle이라는 데이터파일을 따로 만든다고 생각 .pickle
profile_file = open("profile.pickle", "wb") # 따로 인코딩 안해도됨
profile = {"이름":"박명수", "나이":30, "취미":["축구", "골프", "코딩"]}
print(profile)

pickle.dump(profile, profile_file) # profile 에 있는 정보를 file 에 저장
profile_file.close()

profile_file = open("profile.pickle", "rb")
profile = pickle.load(profile_file) # file에 있는 정보를 profile 에 불러오기

print(profile)

profile_file.close()
```

### 8-5.with : 자동 close해주는 장점.

```python
# with문을 탈출하면서 자동으로 close도 해줌
import pickle

with open("profile.pickle", "rb") as profile_file:
  print(pickle.load(profile_file))

with open("study.txt", "w", encoding="utf8") as study_file:
  study_file.write("파이썬을 열심히 공부하고 있어요")

with open("study.txt", "r", encoding="utf8") as study_file:
  print(study_file.read())
```

### 8-6.퀴즈()

```python
매주 1회 작성해야 하는 보고서가 있다.
양식)
\- X 주차 주간보고 -
부서 :
이름 :
업무 요약 :

1주차부터 50주차까지의 보고서 파일을 만드는 프로그램을 작성하시오.
조건 : 파일명은 '1주차.txt', ... 와 같이 만듭니다.

for a in range(1,51):
  with open("{0}주차.txt".format(a), "w", encoding="utf8") as study_:
    study_.write("- {0} 주차 주간보고 -".format(a))
    study_.write("\n부서 : ")
    study_.write("\n이름 : ")
    study_.write("\n업무 요약 : ")
```



### 9-1.클래스 (붕어빵 틀) : 객체지향

```python
class Unit: # 클래스 내부에선 항상 self 적어준다 생각
  def __init__(self, name, hp, damage):
    self.name = name
    self.hp = hp
    self.damage = damage
    print("{0} 유닛이 생성 되었습니다.".format(self.name))
    print("체력 {0}, 공격력 {1}".format(self.hp, self.damage))

marine1 = Unit("마린", 40, 5)
marine2 = Unit("마린", 40, 5)
tank = Unit("탱크", 150, 35)

# 레이스 : 공중 유닛, 비행기. 클로킹 (상대방에게 보이지 않음)
wraith1 = Unit("레이스", 80, 5)
print("유닛 이름 : {0}, 공격력 : {1}".format(wraith1.name, wraith1.damage))

# 마인드 컨트롤 : 상대방 유닛 내 것으로 만듬
wraith2 = Unit("빼앗은 레이스", 80, 5)
wraith2.clocking = True # 클래스 외부에서 원하는 변수를 확장 할수도 있다.

if wraith2.clocking == True:
  print("{0} 는 현재 클로킹 상태입니다.".format(wraith2.name))
```

### 9-2.__init__ : 생성자, del : 소멸자...등

**마린이라던지 탱크같은 객체만들어질때 자동으로 호출되는 부분(init)**

**마린이나 탱크와 같이 어떤 클래스로부터 만들어지는 녀석들을 객체라 하고**

**이때, 마린과 탱크는 유닛클래스의 인스턴스라고 표현을 합니다**.

* **init**

  ```python
  간단해서 생략.
  ```

* **del**

  ```python
  이 또한 생략.
  ```

* **repr**

  ```python
  # 출력.
  def __repr__(self):
  	return self.title
  ```

* **add**

  ```python
  # 덧셈.
  def __add__(self, other):
  	return self.area + other.area
  ```

* **cmp**

  ```python
  # 비교.
  def __cmp__(self, other):
  	if self.area < other.area :
  		return -1
  	elif self.area == other.area :
  		return 0
  	else :
  		return 1
  ```

### 9-3.멤버변수

**어떤 클래스 내에서 정의된 변수**

**변수를 내부 외부(.변수)에서도 사용 가능** 

**클래스 외부에서 원하는 변수를 확장 할수도 있다.**

* wraith2.clocking = True # class에서 예시부분

### 9-4.메소드 : 함수

```python
# 일반 유닛
class Unit:
  def __init__(self, name, hp, damage):
    self.name = name
    self.hp = hp
    self.damage = damage
    print("{0} 유닛이 생성 되었습니다.".format(self.name))
    print("체력 {0}, 공격력 {1}".format(self.hp, self.damage))

# 공격 유닛  
class AttackUnit:
  def __init__(self, name, hp, damage):
    self.name = name
    self.hp = hp
    self.damage = damage

  def attack(self, location):
    print("{0} : {1} 방향으로 적군을 공격 합니다. [공격력 {2}]" \
      .format(self.name, location, self.damage))

  def damaged(self, damage):
    print("{0} : {1} 데미지를 입었습니다.".format(self.name, damage))
    self.hp -= damage
    print("{0} : 현재 체력은 {1} 입니다.".format(self.name, self.hp))
    if self.hp <= 0:
      print("{0} : 파괴되었습니다.".format(self.name))

# 파이어뱃 : 공격 유닛, 화염방사기.
firebat1 = AttackUnit("파이어뱃", 50, 16)
firebat1.attack("5시")

# 공격 2번 받는다고 가정
firebat1.damaged(25)
firebat1.damaged(25)
```

### 9-5.상속 : 부모 자식 관계

```python
# 공격 유닛  
class AttackUnit(Unit): # 상속!
  def __init__(self, name, hp, damage):
    Unit.__init__(self, name, hp)
    self.damage = damage
```

### 9-6.다중상속

```python
# 드랍쉽 : 공중 유닛, 수송기. 마린 / 파이어뱃 / 탱크 등을 수송. 공격 X
# 공중 공격 유닛 클래스
class FlyableAttackUnit(AttackUnit, Flyable): # 다중상속
  def __init__(self, name, hp, damage, flying_speed):
    AttackUnit.__init__(self, name, hp, damage)
    Flyable.__init__(self, flying_speed)

# 정리:
Unit <- AttackUnit <- FlyableAttackUnit -> Flyable
```

### 9-7.메소드 오버라이딩(연산자 오버라이딩)

* 상속받으면 부모의 함수를 사용할 수 있지만, 그 함수를 가공해서 조금 다르게 사용하고 싶을때.

```python
# 일반 유닛
class Unit:
  def __init__(self, name, hp, speed):
    self.name = name
    self.hp = hp
    self.speed = speed

  def move(self, location):
    print("[지상 유닛 이동]")
    print("{0} : {1} 방향으로 이동합니다. [속도 {2}]".\
      format(self.name, location, self.speed))

# 공격 유닛  
class AttackUnit(Unit): # 상속!
  def __init__(self, name, hp, speed, damage):
    Unit.__init__(self, name, hp, speed)
    self.damage = damage

  def attack(self, location):
    print("{0} : {1} 방향으로 적군을 공격 합니다. [공격력 {2}]" \
      .format(self.name, location, self.damage))

  def damaged(self, damage):
    print("{0} : {1} 데미지를 입었습니다.".format(self.name, damage))
    self.hp -= damage
    print("{0} : 현재 체력은 {1} 입니다.".format(self.name, self.hp))
    if self.hp <= 0:
      print("{0} : 파괴되었습니다.".format(self.name))

# 공중 기능 클래스
class Flyable:
  def __init__(self, flying_speed):
    self.flying_speed = flying_speed

  def fly(self, name, location):
    print("{0} : {1} 방향으로 날아갑니다. [속도 {2}]"\
      .format(name, location, self.flying_speed))

# 공중 공격 유닛 클래스
class FlyableAttackUnit(AttackUnit, Flyable): # 다중상속
  def __init__(self, name, hp, damage, flying_speed):
    AttackUnit.__init__(self, name, hp, 0, damage) # 지상 speed 0
    Flyable.__init__(self, flying_speed)

  def move(self, location): # 메소드 오버라이딩!!!!!
    print("[공중 유닛 이동]")
    self.fly(self.name, location)

# 벌쳐 : 지상 유닛, 기동성이 좋음
vulture = AttackUnit("벌쳐", 80, 10, 20)

# 배틀크루저 : 공중 유닛, 체력도 굉장히 좋음, 공격력도 좋음.
battlecruiser = FlyableAttackUnit("배틀크루저", 500, 25, 3)

vulture.move("11시")
battlecruiser.fly("배틀크루저", "9시")

# 따라서 매번 공중(fly), 지상유닛일시(move) 다른 함수를 써야함..
battlecruiser.move("9시") # move로 통일!

# 정리:
Unit(move()) <- AttackUnit <- FlyableAttackUnit(move()) -> Flyable
```

### 9-8.pass : 바로 넘어감.

```python
class BuildingUnit(Unit):
  def __init__(self, name, hp, location):
    pass
```

### 9-9.super

* 부모를 의미. super().함수() 이런식으로 사용하겠죠.

```python
# 단일 상속일때
class BuildingUnit(Unit):
  def __init__(self, name, hp, location):
    # Unit.__init__(self, name, hp, 0) # speed는 0으루 가정
    super().__init__(name, hp, 0) 
    self.location = location

# 다중 상속일때
class Unit:
  def __init__(self):
    print("Unit 생성자")

class Flyable:
  def __init__(self):
    print("Flyable 생성자")

class FlyableUnit(Unit, Flyable): # 다중 상속.
  def __init__(self):
    # super().__init__() # 다중 상속일때는 앞에만 받아와짐
    Unit.__init__(self)
    Flyable.__init__(self)

# 드랍쉽
dropship = FlyableUnit()
```

### 9-10.퀴즈()

```python
부동산 프로그램 작성하시오
(예제)
총 3대의 매물이 있습니다.
강남 아파트 매매 10억 2010년
마포 오피스텔 전세 5억 2007년
송파 빌라 월세 500/50 2000년

[코드]
class House:
  # 매물 초기화
  def __init__(self, location, house_type, deal_type, price, comletion_year):
    self.location = location
    self.house_type = house_type
    self.deal_type = deal_type
    self.price = price
    self.comletion_year = comletion_year

  # 매물 정보 표시
  def show_detail(self):
    print(self.location, self.house_type, self.deal_type\
      , self.price, self.comletion_year)

houses = []
house1 = House("강남", "아파트", "매매", "10억", "2010년")
house2 = House("마포", "오피스텔", "전세", "5억", "2007년")
house3 = House("송파", "빌라", "월세", "500/50", "2000년")
houses.append(house1) # 각각 show_detail하긴 힘드니까 for문사용위해
houses.append(house2)
houses.append(house3)

print("총 {0}대의 매물이 있습니다.".format(len(houses))) # 객체가 몇개인지 알려줌
for house in houses:
  house.show_detail()
```



### 10-1.예외처리

* 에러이름 알아내는법, except:만 사용시 에러 전부
* raise ValueError # 에러를 정의해줌!

```python
try:
  print("나누기 전용 계산기입니다.")
  nums = []
  nums.append(int(input("첫 번째 숫자를 입력하세요 : ")))
  nums.append(int(input("두 번째 숫자를 입력하세요 : ")))
  #nums.append(int(nums[0]/nums[1]))
  print("{0} / {1} = {2}".format(nums[0],nums[1],nums[2]))
except ValueError:
  print("에러! 잘못된 값을 입력하였습니다.")
except ZeroDivisionError as err:
  print(err)
except Exception as err: # 에러이름 알아내는법, except:만 사용시 에러 전부
  print("알 수 없는 에러가 발생하였습니다.")
  print(err)
```

### 10-2.에러 발생시키기

```python
try:
  print("한 자리 숫자 나누기 전용 계산기입니다.")
  num1 = int(input("첫 번째 숫자를 입력하세요 : "))
  num2 = int(input("두 번째 숫자를 입력하세요 : "))
  if num1 >= 10 or num2 >=10:
    raise ValueError # 에러를 정의해줌!
  print("{0} / {1} = {2}".format(num1, num2, int(num1 / num2)))
except ValueError: # 그 에러를 여기서 받을수 있음!
  print("잘못된 값을 입력하였습니다. 한 자리 숫자만 입력하세요.")
```

### 10-3.사용자 정의 예외처리 (에러 직접정의!)

```python
class BigNumberError(Exception): # 에러 정의해줬음(Exception)상속 해줘야함
  #pass
  def __init__(self, msg):
    self.msg = msg
    
  def __str__(self):
    return self.msg
try:
  print("한 자리 숫자 나누기 전용 계산기입니다.")
  num1 = int(input("첫 번째 숫자를 입력하세요 : "))
  num2 = int(input("두 번째 숫자를 입력하세요 : "))
  if num1 >= 10 or num2 >=10:
    raise BigNumberError("입력값 : {0}, {1}".format(num1, num2)) # 에러를 정의해줌!
  print("{0} / {1} = {2}".format(num1, num2, int(num1 / num2)))
except ValueError: # 그 에러를 여기서 받을수 있음!
  print("잘못된 값을 입력하였습니다. 한 자리 숫자만 입력하세요.")
except BigNumberError as err:
  print("에러가 발생하였습니다. 한 자리 숫자만 입력하세요.")
  print(err)
```

### 10-4.finally

```python
class BigNumberError(Exception):
  #pass
  def __init__(self, msg):
    self.msg = msg
    
  def __str__(self):
    return self.msg

try:
  print("한 자리 숫자 나누기 전용 계산기입니다.")
  num1 = int(input("첫 번째 숫자를 입력하세요 : "))
  num2 = int(input("두 번째 숫자를 입력하세요 : "))
  if num1 >= 10 or num2 >=10:
    raise BigNumberError("입력값 : {0}, {1}".format(num1, num2)) 
  print("{0} / {1} = {2}".format(num1, num2, int(num1 / num2)))
except ValueError:
  print("잘못된 값을 입력하였습니다. 한 자리 숫자만 입력하세요.")
except BigNumberError as err:
  print("에러가 발생하였습니다. 한 자리 숫자만 입력하세요.")
  print(err)
finally: # 반드시 실행 함!
  print("계산기를 이용해 주셔서 감사합니다.")
```

### 10-5.퀴즈()

```python
치킨집있당. 자동 주문 시스템 제작했당.
코드 확인후 적절한 예외처리 구문넣기
조건 1 : 1보다 작거나 숫자가 아닌 입력값올때 ValueError로 처리
    출력 메시지 : "잘못된 값을 입력하였습니다."
조건 2 : 대기 손님이 주문할 수 있는 총 치킨량은 10마리로 한정
    치킨 소진 시 사용자 정의 에러[SoldOutError]발생후 프로그램 종료
    출력 메시지 : "재고가 소진되어 더 이상 주문을 받지 않습니다."

[코드]
class SoldOutError(Exception):
  pass

chicken = 10
waiting = 1 # 홀 안에는 현재 만석. 대기번호 1부터 시작

while(True):
  try:
    print("[남은 치킨 : {0}]".format(chicken))
    order = int(input("치킨 몇 마리 주문하시겠습니까?"))
    if order > chicken: # 남은 치킨보다 주문량이 많을때
      print("재료가 부족합니다.")
    elif order < 1:
      raise ValueError
    else:
      print("[대기번호 {0}] {1} 마리 주문이 완료되었습니다."\
        .format(waiting, order))
      waiting += 1
      chicken -= order
    if chicken == 0:
      raise SoldOutError
  except ValueError:
    print("잘못된 값을 입력하였습니다.")
  except SoldOutError:
    print("재고가 소진되어 더 이상 주문을 받지 않습니다.")
    break
```



### 11-1.모듈 # theater_modoul.py

#### theater_modoul.py

```python
# 일반 가격
def price(people):
    print("{0}명 가격은 {1}원 입니다.".format(people, people * 10000))

# 조조할인 가격
def price_morning(people):
    print("{0}명 조조 할인 가격은 {1}원 입니다.".format(people,people * 6000))

# 군인 할인 가격
def price_soldier(people):
    print("{0}명 군인 할인 가격은 {1}원 입니다.".format(people,people * 4000))
```

#### 다른 파일.py

```python
import theater_module
theater_module.price(3) # 3명이서 영화 보러갔을 때 가격
theater_module.price_morning(4)
theater_module.price_soldier(5)

import theater_module as mv
mv.price(3)
mv.price_morning(4)
mv.price_soldier(5)

from theater_module import * # 대박 줄임!!
price(3)
price_morning(4)
price_soldier(5)

from theater_module import price, price_morning # 원하는것만!!
price(3)
price_morning(4)

from theater_module import price_soldier as price # 원하는걸 원하는 형태까지!!
price(5)
```

### 11-2.패키지(pycache)

* **travel이란 폴더에, thailand.py 만듬**

  ```python
  class ThailandPackage:
      def detail(self):
          print("[태국 패키지 3박 5일] 방콕, 파타야 여행 (야시장 투어) 50만원")
  
  if __name__ == "__main__":
      print("Thailand 모듈을 직접 실행")
      print("이 문장은 모듈을 직접 실행할 때만 실행돼요")
      trip_to = ThailandPackage()
      trip_to.detail()
  else:
      print("Thailand 외부에서 모듈 호출")
  ```

```python
import travel.thailand # 이 파일보다 현재 코드작성하는 파일이 위에있어야함.
trip_to = travel.thailand.ThailandPackage()
trip_to.detail()

# 주의할점
# import뒤에는 모듈이나 패키지만가능, 즉 클래스나 함수는 직접 import바로 못함
import travel.thailand.ThailandPackage # 오류

다만,
from travel.thailand import ThailandPackage # 이건가능!!
trip_to = ThailandPackage()
trip_to.detail()
```

### 11-3._all_

#### travel/vietnam.py

```python
class VietnamPackage:
    def detail(self):
        print("[베트남 패키지 3박 5일] 다낭 효도 여행 60만원")
```

#### 외부 파일.py

```python
# from random import *
from travel import * 
trip_to = vietnam.VietnamPackage()
trip_to.detail()
# 오류 뜨는이유는 패키지안에서 공유하는 범위지정해줄수 있는데
# 그것때문
# __init__.py에서 범위 수정!! 수정하는거 확인!
```

#### __ init __.py

```python
__all__ = ["vietnam", "thailand"]
```

### 11-4.모듈 직접 실행

#### thailand.py

```python
class ThailandPackage:
    def detail(self):
        print("[태국 패키지 3박 5일] 방콕, 파타야 여행 (야시장 투어) 50만원")

if __name__ == "__main__":
    print("Thailand 모듈을 직접 실행")
    print("이 문장은 모듈을 직접 실행할 때만 실행돼요")
    trip_to = ThailandPackage()
    trip_to.detail()
else:
    print("Thailand 외부에서 모듈 호출")
```

### 11-5.패키지, 모듈 위치

```python
from travel import * # travel패키지 안에 py다 받아옴.
import inspect
import random

print(inspect.getfile(random)) # 경로 구해줌
print(inspect.getfile(thailand))
만약 thailand가 폴더를 random있는 경로에 넣어줘도 됨.
```

### 11-6.pip install

```python
pypi구글에 검색해서 들어가서 여러가지 볼수있음.
pip list - 치면은 뭐깔렸는지 볼수있음.
pip show 패키지명 - 치면은 자세한 정보 볼수있음.
pip install --upgrade 패키지명 - 업그레이드 할수있음.
pip uninstall 패키지명 - 삭제 할수있음.
```

### 11-7.내장함수 # 구글에 list of python builtins 검색하면 많이 나옴!

* dir() : 객체가 어떤 변수와 함수를 가지는지 표시.

```python
# input() : 사용자 입력을 받는 함수
language = input("무슨 언어를 좋아하세요?")
print("{0}은 아주 좋은 언어입니다!".format(language))

# dir : 어떤 객체를 넘겨줬을 때 그 객체가 어떤 변수와 함수를 가지고 있는지 표시
print(dir())

import random # 외장 함수
print(dir()) # random이 추가됨

import pickle
print(dir()) # pickle이 추가됨

print(dir(random)) # random. 찍었을때 나오는함수들 유사하게 나온다 생각
lst = [1,2,3]
print(dir(lst))

name = "Jim"
print(dir(name))
```

### 11-8.외장함수 # 구글에 list of python modules 검색하면 많이 나옴!

* glob, os 모듈
* time, datetime 모듈

```python
# glob : 경로 내의 폴더 / 파일 목록 조회
import glob
print(glob.glob("*.py")) # 확장자가 py인 모든 파일

# os : 운영체제에서 제공하는 기본 기능
import os
print(os.getcwd()) # 현재 디렉토리

folder = "sample_dir"
if os.path.exists(folder):
  print("이미 존재하는 폴더입니다.")
  os.rmdir(folder)
  print(folder, "폴더를 삭제하였습니다.")
else:
  os.makedirs(folder) # 폴더 생성
  print(folder, "폴더를 생성하였습니다.")

print(os.listdir()) # glob가 비슷하게

import time
print(time.localtime()) # 현재시간
print(time.strftime("%Y-%m-%d %H:%M:%S"))

import datetime
print("오늘 날짜는 ", datetime.date.today()) # 현재날짜
timedelta : 두 날짜 사이의 간격
today = datetime.date.today()
td = datetime.timedelta(days=100) # 100일 저장
print("우리가 만난지 100일은", today + td) # 오늘 날짜에서 100일 더해줌
```

### 11-9.퀴즈() 

```python
# 프로젝트 내에 나만의 시그니처를 남기는 모듈을 만드시오
# 조건 : 모듈 파일명은 byme.py로 작성
# (모듈 사용 예제)

# (출력 예제)
# 이 프로그램은 ~~~~~~에 의해 만들어졌습니다.
# 유튜브 : http://youtube.com
# 이메일 : ~~~~~~~~@gmail.com

# 답안(외부 파일.py)
import byme
byme.sign()
# 답안(byme.py)
def sign():
    print("이 프로그램은 ~~~~~~에 의해 만들어졌습니다.")
    print("유튜브 : http://youtube.com")
    print("이메일 : ~~~~~~~~@gmail.com")
```

