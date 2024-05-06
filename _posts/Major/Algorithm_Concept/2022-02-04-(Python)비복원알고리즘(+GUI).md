---
title:  "[Python]비복원 알고리즘(+GUI)"
categories : Algorithm
tag : [tkiinter, 비복원 알고리즘, 2진수 나눗셈]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## 비복원 알고리즘

**개념 설명 생략**

* 복원알고리즘과 다르게 복원을 하지않는다는 큰 특징을 가진다.(이 또한 나눗셈의 알고리즘이다)
* 10진수, 2진수 둘다 입력받을수 있도록 구현(2진수 입력시 음수는 맨앞에 -를 붙혀라)
* 출력화면을 보고 알고리즘을 이해
* GUI는 tkiinter라이브러리 활용

<br>

#### 출력화면

* Bit를 음수 나타낼 때 각 Bit앞에 -를 붙힌다는 점.
* 피제수 : 2, 제수 : 1 입력 화면이며, 피제수는 Bit를 2배로 해서 나타낸다는점.
* 몫은 Bit가 -가 있는 즉, 부분합이 음수라면 0이고, 양수라면 1이 들어가게 된다.
  * 아래 예시에서 보면 001 까지 몫이 나올텐데, 이는 이미 부분합이 0으로 일찍 끝나버려서 bit(예로 4)만큼 반복하기 전에 끝나버린것 뿐이다. 즉, 001이면 뒤에 0을 추가해 0010으로 몫을 알 수 있다.

```python
0001|00000010   2/1
-    00001000   8
----------------
     00000-1-10   -6
     00000100   4
----------------
     000000-10   -2
     00000010   2
----------------
     00000000   0
몫 : 0010 십진수 : 2
나머지 : 0000 십진수 : 0
```

<br>

### 1. 2진수 -> 10진수

* Booth알고리즘과 방식이 좀 다른데 비교해볼것.

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

* Bit 확장 때문에 함수 2개 만듬.(그냥 빨리 만드려고 함수 2개로 나누었다,,)

```python
# 10진수 -> 2진수 => 피벳 bit와 같은 bit로 나타내줌.
def convertBinary(n, whatBit): # 처음 convert함수(맨앞에 0붙히고 나머지 뒤에 0붙혀서 비트확장)
    if(n >= 0): # 양수
        quo = n
        rem = n
        str_1 = ""
        while(quo != 0):
            rem = int(quo %2) # 2%2 = 0
            quo = int(quo /2) # 2/2 = 1
            str_1 = str(rem)+str_1 # str_1앞에 rem이 붙어야함.
        while(len(str_1) != int(whatBit/2)):
            str_1 = "0" + str_1 # 맨앞에 0을 추가
        while(len(str_1) != whatBit):
            str_1 = str_1 + "0" # 맨뒤에 0을 추가.
        return str_1
    else: # 음수
        nM = -n # 양수로 전환
        quo = n
        rem = n
        str_1 = ""
        while(quo != 0):
            rem = int(quo %2) # 2%2 = 0
            quo = int(quo /2) # 2/2 = 1
            str_1 = str(rem)+str_1 # str_1앞에 rem이 붙어야함.
        while(len(str_1) != int(whatBit/2)):
            str_1 = "0" + str_1 # 맨앞에 0을 추가
        while(len(str_1) != whatBit):
            str_1 = str_1 + "0" # 맨뒤에 0을 추가.
        str_2 = ""
        for i in range(len(str_1)):
            if(str_1[i] == "1"):
                str_2 = str_2+"-1"
            else:
                str_2 = str_2+"0"
        return str_2
```

```python
def convertBinaryOri(n, whatBit): # 후반 convert함수(그냥 앞에만 0)
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

```python
def btnExit():
	root.destroy()

def btnStart():
    # 초기화 시작
    gubunMinus = 4 # 처음엔 양수로 초기화.
    whatBit = bit_var.get() # Bit 가져옴. (제수)
    whatBinary = bit_var2.get() # 10진수라면 10, 2진수라면 2반환
    if(whatBinary == 10): # 십진수 입력시
        dividend = entry_dividend.get() # 피제수 가져옴.
        divisor = entry_divisor.get() # 제수 가져옴.
        try:
            a = int(dividend)
            b = int(divisor)
        except:
            txtResult.insert(END,"숫자를 다시 입력하세요.")
            return
        if(a<0 and b >= 0):
            a = -a
            gubunMinus = 1
        elif(a>=0 and b<0):
            b = -b
            gubunMinus = 2
        elif(a<0 and b<0):
            a= -a
            b= -b
            gubunMinus = 3
        else: # 양수
            gubunMinus = 4
        dividend=convertBinaryOri(a,whatBit*2)
        divisor=convertBinaryOri(b,int(whatBit))
    else: # 2진수 입력시
        dividend = entry_dividend.get() # 피제수 가져옴.
        divisor = entry_divisor.get() # 제수 가져옴.
        # 앞서 얘기했듯이, 몫과 나머지의 부호를 추후에 바꾸기 위한 작업 실시.
        if(dividend[0] == "-" and divisor[0] != "-"): # 1 피젯 -, 제수 +
            dividend = dividend[1:]
            gubunMinus = 1
        elif(dividend[0] != "-" and divisor[0] == "-"): # 2 피젯 +, 제수 -
            divisor = divisor[1:]
            gubunMinus = 2
        elif(dividend[0] == "-" and divisor[0] == "-"): # 3 피젯 -, 제수 -
            dividend = dividend[1:]
            divisor = divisor[1:]
            gubunMinus = 3
        else: # 4 피젯 +, 제수 +
            gubunMinus = 4

    # 지워줘야 다시 적기 편해서 지움.
    entry_dividend.delete(0, END) 
    entry_divisor.delete(0,END)
    txtResult.delete("1.0",END)
    # 뒤에 데이터 범위 오류처리 위한 작업 실시.
    lenDividend = pow(2,whatBit*2)-1 # 피제수 데이터 크기 초기화.
    lenDivisor = pow(2,int(whatBit))-1 # 제수 데이터 크기 초기화.
    # 10진수가 해당 비트 범위를 넘었는지 체크.
    dividendDeci = convertDecimal(dividend) # 10진수 피제수
    divisorDeci = convertDecimal(divisor) # 10진수 제수
    if(dividendDeci > lenDividend or dividendDeci < (-lenDividend) or divisorDeci > lenDivisor or divisorDeci < (-lenDivisor)):
        txtResult.insert(END,"범위를 넘었습니다. 다시 입력하세요.")
        return
    # 10진수 제수를 2진수 제수로 다시 바꾸는데 whatBit크기로 바꾸기
    # 그리고 바꾼 2진수를 다시 10진수로 바꿔서 >> 쉬프트 1번 한후 부분 나머지로 초기화.
    a = convertBinary(divisorDeci, whatBit*2) # 2진수 부분 나머지
    divisionDeci = convertDecimal(a) # 10진수 부분 나머지
    divisionDeci = divisionDeci >> 1 # shift 1번 사용(초기값 출력위함)
    
    quotient = "" # 몫

    # 초기 출력 시작
    initPrint = ""
    initPrint += divisor + "|" + dividend + "   " # 2진수 bit 출력(처음 나눗셈)
    initPrint += str(dividendDeci) + "/" + str(divisorDeci)  + '\n' # 이부분은 십진수로 옆에 표시 출력. (처음 나눗셈)
    initPrint += "-" + " "*(int(whatBit)) + convertBinaryOri(divisionDeci, whatBit*2) + "   " # 2진수 bit 출력 (부분 나머지 초기화 출력)
    initPrint += str(divisionDeci) + '\n' + "-"*whatBit*4 + '\n' # 이부분은 십진수로 옆에 표시 출력. (부분 나머지 초기화 출력)
    # 부분나머지 첫 연산
    divisionDeci = dividendDeci-divisionDeci # 부분나머지 연산
    if(divisionDeci < 0): # 몫에 bit 0 넣음
        quotient += "0"
    else: # 몫에 bit 1 넣음
        quotient += "1"
    # 2진수 bit 출력 (부분 나머지 처음 연산후 출력)
    initPrint += " "*(int(whatBit)+1) + convertBinaryOri(divisionDeci, whatBit*2) + "   "
    # 이부분은 십진수로 옆에 표시 출력.(부분 나머지 처음 연산후 출력)
    initPrint += str(divisionDeci) + '\n'
    # 10진수 제수를 >>해서 제수 재정의
    a = convertBinary(divisorDeci, whatBit*2)
    divisorDeci = convertDecimal(a) # 10진수 제수 재정의
    divisorDeci = divisorDeci >> 1 # shift 1번 사용

    # 후반 반복 출력
    for i in range(int(whatBit)-1): # 앞에서 쉬프트 1번 사용했기 때문에 -1
        prevDivisionDeci = divisionDeci # prev부분나머지.
        divisorDeci = divisorDeci >> 1 # 제수 쉬프트
        if(divisionDeci < 0): # shift된 제수 더함.
            divisionDeci = divisionDeci + divisorDeci # 부분나머지 연산
        else: # shift된 제수 뺌.
            divisionDeci = divisionDeci - divisorDeci # 부분나머지 연산
        if(divisionDeci < 0): # 몫에 bit 0 넣음
            quotient += "0"
        else: # 몫에 bit 1 넣음
            quotient += "1"
        initPrint += " "*(int(whatBit)+1) + convertBinaryOri(divisorDeci,whatBit*2) + "   " + str(divisorDeci) + '\n'
        initPrint += "-"*whatBit*4 + '\n' + " "*(int(whatBit)+1) + convertBinaryOri(divisionDeci, whatBit*2) + "   " + str(divisionDeci) + '\n'
        # 예외처리로 이미 나머지가 0이 나올경우 처리
        if(divisionDeci == 0):
            while(len(quotient) != int(whatBit)):
                quotient += "0"
            break

    # 나머지가 음수가 나왔다면 그 전단계 나머지가 답이된다.
    if(divisionDeci < 0):
        divisionDeci = prevDivisionDeci
        initPrint += "나머지가 음수이기 때문에 복원 실시 : "+ convertBinaryOri(divisionDeci, whatBit*2) + '\n'
    # 입력 음수의 경우 추가하기.(음수나누기)
    quotientDeci = 0
    division = ""
    if(gubunMinus == 1):
        # 몫 -, 나머지 -로 변환
        initPrint += "음수 입력했기 때문에 변환 실시" + '\n'
        quotientDeci = convertDecimal(quotient)
        quotientDeci = -quotientDeci # 몫 십진수 음수 변환
        quotient = convertBinaryOri(quotientDeci, whatBit*2) # 몫 2진수 음수 변환
        divisionDeci = -divisionDeci # 나머지 십진수 음수 변환
        division = convertBinaryOri(divisionDeci, whatBit*2) # 나머지 2진수 음수 변환
    elif(gubunMinus == 2):
        # 몫 -
        initPrint += "음수 입력했기 때문에 변환 실시" + '\n'
        quotientDeci = convertDecimal(quotient)
        quotientDeci = -quotientDeci # 몫 십진수 음수 변환
        quotient = convertBinaryOri(quotientDeci, whatBit*2) # 몫 2진수 음수 변환
        division = convertBinaryOri(divisionDeci, whatBit*2) # 초기화
    elif(gubunMinus == 3):
        # 나머지 -
        initPrint += "음수 입력했기 때문에 변환 실시" + '\n'
        divisionDeci = -divisionDeci # 나머지 십진수 음수 변환
        division = convertBinaryOri(divisionDeci, whatBit*2) # 나머지 2진수 음수 변환
        quotientDeci = convertDecimal(quotient) # 초기화
    elif(gubunMinus == 4):
        # 양수
        quotientDeci = convertDecimal(quotient) # 초기화
        division = convertBinaryOri(divisionDeci, int(whatBit)) # 초기화

    initPrint += "몫 : " + quotient + " 십진수 : " + str(quotientDeci) + '\n'
    initPrint += "나머지 : " + division + " 십진수 : " + str(divisionDeci) + '\n'
    txtResult.insert(END,initPrint)
```

<br>

### 4. GUI

```python

# GUI 설정
root = Tk()
root.title("BoothAlg")
root.geometry("1450x550")

label0 = Label(root, text="입력시 10진수 또는 2진수 선택\n2진수 입력시, 음수는 맨앞에 -를 붙힙니다.")
label0.grid(row=0, column=4, padx=3, pady=5)

label1 = Label(root, text="피제수 : ")
label1.place(x=200, y=10)
entry_dividend = Entry(root, width=50)
entry_dividend.grid(row=0, column=1)

label2 = Label(root, text="제수 : ")
label2.place(x=200, y=50)
entry_divisor = Entry(root, width=50)
entry_divisor.grid(row=1, column=1)

# 십진수냐 이진수냐 설정
bit_var2 = IntVar() # 여기에 int 형으로 값을 저장한다.
radio_Decimal = Radiobutton(root, text="10진수",width=4, value=10, variable=bit_var2)
radio_Binary = Radiobutton(root, text="2진수",width=3, value=2, variable=bit_var2)
radio_Binary.select() # 기본값 선택
radio_Decimal.place(x=10, y=10)
radio_Binary.place(x=10, y=30)

# 16bit를 기본값으로 radioButton 설정
bit_var = IntVar() # 여기에 int 형으로 값을 저장한다.
label3 = Label(root, text="몫 Bit를 선택하세요")
label3.grid(row=0, column=2, padx=5)
radio_4bit = Radiobutton(root, text="4Bit",width=3, value=4, variable=bit_var)
radio_8bit = Radiobutton(root, text="8Bit",width=3, value=8, variable=bit_var)
radio_16bit = Radiobutton(root, text="16Bit",width=3, value=16, variable=bit_var)
radio_32bit = Radiobutton(root, text="32Bit",width=3, value=32, variable=bit_var)
radio_8bit.select() # 기본값 선택
radio_4bit.grid(row=1, column=2)
radio_8bit.grid(row=2, column=2)
radio_16bit.grid(row=1, column=3)
radio_32bit.grid(row=2, column=3)

btn1 = Button(root, text="실행",width=30, height=1, command=btnStart)
btn2 = Button(root, text="종료",width=30, height=1, command=btnExit)
btn1.grid(row=2, column=0, sticky=N+W+S, columnspan=1 ,padx=3, pady=3)
btn2.grid(row=2, column=1, sticky=N+W+S, columnspan=2 ,padx=3, pady=3)

label4 = Label(root, text="결과")
label4.grid(row=3, column=0,padx=5)
txtResult = Text(root, width=205, height=30)
txtResult.grid(row=4, column=0, padx=5, columnspan=10)


root.mainloop() # 마지막에 붙혀주기.
```

