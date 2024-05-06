---
title:  "[Python]복원 알고리즘(+GUI)"
categories : Algorithm
tag : [tkiinter, 복원 알고리즘, 2진수 나눗셈]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## 복원 알고리즘

**개념 설명 생략**

* 나눗셈 알고리즘이며, 2진수 양수(4bit)로 입력받는것만 구현.
* 출력형태를 보고 알고리즘 이해(복원 한다는 점이 핵심)
* GUI로 만듬(tkiinter 라이브러리 활용) -> import 생략하겠음

<br>

#### 출력형태

* 참고로 몫은 맨 오른쪽bit에 몫 값을 넣고 왼쪽으로 쉬프트를 반복하는 중. 따라서 마지막에 0001이 된것

```python
  00000010(피제수)   xxxx(몫)   0010(제수)

  00000100(최초의 부분나머지)
-+0010(-+라는것은 복원했다는 의미, 제수)
--------
  00000100   xxx0(복원을 한다면 몫을 0)
  00001000
-+0010
--------
  00001000   xx00
  00010000
-+0010
--------
  00010000   x000
  00100000
- 0010
--------
  00000000   0001
나머지 : 0000 => 0
몫 : 0001 => 1
```

<br>

### 1. 2진수 -> 10진수

* Booth알고리즘에서랑은 조금 다른 방식임.(비교해볼것)

```python
# 2진수 -> 10진수 변환
def convertDecimal(n):
    n = n[::-1] # 역순으로 봄.
    convertN = 0
    for i in range(len(n)):
        if i == 0:
            if n[int(i)] == '1':
                x = 1 
            else: 
                x = 0
            convertN += x
        else:
            if n[i] == '1':
                convertN = convertN + (2 ** i)
    return convertN
```

<br>

### 2. 10진수 -> 2진수

```python
# 10진수 -> 2진수 변환
def convertBinaryOri(n, whatBit): 
    if(n >= 0): # 양수
        quo = n
        rem = n
        str_1 = ""
        while(quo != 0):
            rem = int(quo %2) # 2%2 = 0
            quo = int(quo /2) # 2/2 = 1
            str_1 = str(rem)+str_1 # str_1앞에 rem이 붙어야함.
        while(len(str_1) != whatBit):
            str_1 = "0" + str_1 # 맨앞에 0을 추가
        return str_1
    else: # 음수 => bit -1로 표시하기.
        nM = -n # 양수로 전환
        quo = n
        rem = n
        str_1 = ""
        while(quo != 0):
            rem = int(quo %2) # 2%2 = 0
            quo = int(quo /2) # 2/2 = 1
            str_1 = str(rem)+str_1 # str_1앞에 rem이 붙어야함.
        while(len(str_1) != whatBit):
            str_1 = "0" + str_1 # 맨앞에 0을 추가
        str_2 = ""
        for i in range(len(str_1)):
            if(str_1[i] == "1"):
                str_2 = str_2+"-1"
            else:
                str_2 = str_2+"0"
        return str_2
```

<br>

### 3. 종료, 시작 버튼

* 쉬프트를 2진수가 아닌 십진수에 하는 방식이다.

```python
def btnExit():
	root.destroy()

def btnStart():
    # 초기화 시작
    whatBit = bit_var.get() # Bit 가져옴. (제수)
    dividend = entry_dividend.get() # 피제수 가져옴.
    divisor = entry_divisor.get() # 제수 가져옴.
    # 지워줘야 다시 적기 편해서 지움.
    entry_dividend.delete(0, END) 
    entry_divisor.delete(0,END)
    txtResult.delete("1.0",END)
    # 뒤에 데이터 범위 오류처리 위한 작업 실시.
    lenDividend = pow(2,whatBit)-1 # 피제수 데이터 크기 초기화.
    lenDivisor = pow(2,whatBit)-1 # 제수 데이터 크기 초기화.
    # 10진수가 해당 비트 범위를 넘었는지 체크.
    dividendDeci = convertDecimal(dividend) # 10진수 피제수
    divisorDeci = convertDecimal(divisor) # 10진수 제수
    if(dividendDeci > lenDividend or dividendDeci < (-lenDividend) or divisorDeci > lenDivisor or divisorDeci < (-lenDivisor)):
        txtResult.insert(END,"범위를 넘었습니다. 다시 입력하세요.")
        return
    quotient = "x"*whatBit # 몫 x로 초기화
    quotientSub = ""
    # 피제수 bit 확장 등 알고리즘 시작.
    division = convertBinaryOri(dividendDeci, whatBit*2) # 최초의 부분나머지로 사용
    initPrint = "  " + division + "   " + quotient + "   " + divisor + '\n' # 출력 초기화 시작
    # 0단계 초기화 끝

    # 1단계 부터 시작..
    for i in range(whatBit):
        divisionDeci = convertDecimal(division) # 10진수 부분 나머지
        divisionDeci = divisionDeci << 1 # shift 1번 사용

        a = convertBinaryOri(divisionDeci, whatBit*2)
        initPrint += "  " + a + '\n' + "-"

        divisionBan = a[0:whatBit] # 왼쪽 절반 bit만 추출
        divisionBan2 = a[whatBit:] # 오른쪽 절반 bit만 추출
        divisionBanDeci = convertDecimal(divisionBan) # 연산 위해 십진수로 변경
        divisionDeci = divisionBanDeci-divisorDeci # 연산

        if(divisionDeci < 0): # 결과가 음수라면 복원..
            initPrint += "+"
            bokwon = divisionDeci+divisorDeci # 복원..
            divisionBan = convertBinaryOri(bokwon, whatBit)
            division=divisionBan+divisionBan2 # 다시 부분 나머지 8bit으로 확장
            # 몫 추가.
            quotientSub += "0"
            quotient = quotient[:-(i+1)] + quotientSub
        else: # 양수라면..
            initPrint += " "
            divisionBan = convertBinaryOri(divisionDeci,whatBit)
            division = divisionBan + divisionBan2
            # 몫 추가
            quotientSub += "1"
            quotient = quotient[:-(i+1)] + quotientSub
        initPrint += divisor + '\n' + "-"*whatBit*2 + '\n' + "  " + division + f"   {quotient}" + '\n'

    initPrint += f"나머지 : {division[:whatBit]} => {convertDecimal(division[:whatBit])}" + '\n'
    initPrint += f"몫 : {quotient} => {convertDecimal(quotient)}" + '\n'    
    txtResult.insert(END,initPrint)
```

<br>

### 4. GUI

```python

# GUI 설정
root = Tk()
root.title("BoothAlg")
root.geometry("400x360")

label1 = Label(root, text="피제수 : ")
label1.place(x=10, y=10)
entry_dividend = Entry(root, width=20)
entry_dividend.place(x=80, y=10)

label2 = Label(root, text="제수 : ")
label2.place(x=10, y=50)
entry_divisor = Entry(root, width=20)
entry_divisor.place(x=80, y=50)

bit_var = IntVar() # 여기에 int 형으로 값을 저장한다.
label3 = Label(root, text="비트 4Bit로 고정하겠습니다.")
label3.place(x=230, y=10)
radio_4bit = Radiobutton(root, text="4Bit",width=3, value=4, variable=bit_var)
radio_4bit.select() # 기본값 선택
radio_4bit.place(x=230, y=40)

btn1 = Button(root, text="실행",width=20, height=1, command=btnStart)
btn2 = Button(root, text="종료",width=20, height=1, command=btnExit)
btn1.place(x=10, y=80)
btn2.place(x=200, y=80)

label4 = Label(root, text="결과 (참고 : 2진수 양수(4bit)로 입력하셔야 합니다)")
label4.place(x=10, y=120)
txtResult = Text(root, width=50, height=15)
txtResult.place(x=10, y=150)

root.mainloop() # 마지막에 붙혀주기.
```

