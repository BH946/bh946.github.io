---
title:  "[Python]부스 알고리즘_Booth(+GUI)"
categories : Algorithm
tag : [tkiinter, Booth 알고리즘, 2진수 곱셈]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## Booth알고리즘

* 주관적인 생각으로 만들었기 때문에 예외가 많을 수 있다
* 의미 : 부호가 있는 이진수의 곱셈을 수행할 수 있도록 해주는 알고리즘
* 참고 : 입력은 2진수로 받으며, 음수는 2의 보수로 입력
* GUI로 만듬(tkiinter 라이브러리 활용) -> import 생략하겠음

<br>

**2진수 <-> 10진수 변환 작업이 핵심인 알고리즘이다.**

* 곱셈을 10진수로 변환해서 연산 해버리게 만들었기 때문! (1, 2번 확인)

**00, 01, 10, 11 에 따른 다른 연산방법이 핵심인 알고리즘이다.**

* 3번에 계산부분 확인

<br>

### 1. 2진수 -> 10진수

* 입력받은 2진수를 변수n이라 가정한다면
  * 첫째로, 양수인지 음수인지 구분(n의 최상위Bit 활용) => 양수라면 바로 두번째 단계로 넘어가기
    * 음수라면?? 최상위Bit는 버리고, 2의보수를 원래형태로 바꾸는 작업이 필요!!(for문 확인)
    * 마지막엔 2의보수를 바꿨기 때문에 +1을 꼭 해주고, 음수로 바꾸기위해 -까지 필요
  * 두번째로, 변수n을 int형으로 변환해서 num변수에 담아줌(나머지, 나눗셈 구하기 위해)
  * 마지막으로, 아래 코드의 while문을 이용시 변환끝.(while문이 여기서의 핵심 알고리즘)

```python
# 2진수 -> 10진수 (최상위비트 부호)
def convertDecimal(n): # n은 2진수(string)
	convertN,rem,i,num = 0,0,0,0
	if n[0] == "0": # 양수
		num = int(n)
		while (num != 0):
			rem = int(num % 10)
			num = int(num / 10)
			convertN = int(convertN + rem * pow(2,i))
			i+= 1
	else: # 음수
		tempStr = n[1:]
		str_1 = ""
		for j in range(len(tempStr)): # 2의보수를 원래형태로 바꾸는 작업
			if tempStr[j] == "0":
				str_1 += "1"
			else:
				str_1 += "0"
		num =int(str_1)
		while (num != 0): # 101은 rem:1 num:10 num:1 num:0 ... 이런식의 흐름
			rem = int(num % 10)
			num = int(num / 10)
			convertN = convertN + int(rem * pow(2,i))
			i+=1
		convertN += 1 # 2의보수에서 원래형태로 바꾸기 위해 +1까지 꼭 해주기.
		convertN = -convertN # 양수로 구했기 때문에 마지막 음수로 바꾸는 작업까지.
	return convertN
```

<br>

### 2. 10진수 -> 2진수

* 인수로 받은 n은 10진수, whatBit는 몇 비트인지 의미하며, 음수는 2의 보수로 나타낼 것이다.
  * 10진수를 2진수로 바꾸는데 bit를 2배로 만들 필요 없다면 while문에 whatBit*2가 아니라 whatBit로.
    * 음수라면?? 양수로 바꿔서 똑같이 while문을 이용한다.
    * 2의보수로 나타내야 하기 때문에 1의보수로 먼저 바꾼후, 2의보수로 바꿔준다.

```python
# 10진수 -> 2진수 (최상위비트 부호) (음수는 2의보수로 나타내야함.)
def convertBinary(n, whatBit): # n은 10진수, whatBit는 몇비트로 바꿔야 하는지
	if(n >= 0): # 양수
		quo = n
		rem = n
		str_1 = ""
		while(quo != 0):
			rem = int(quo % 2)
			quo = int(quo / 2)
			str_1 = str(rem) + str_1 # str_1앞에 rem이 붙어야함.
		while(len(str_1) != whatBit*2): # bit를 2배로 만들어서 연산할 것이다.
			str_1 = "0" + str_1 # 맨앞에 0을 추가.
		return str_1
	else: # 음수(2의 보수로)
		nM = -n # 양수로 전환
		quo = nM
		rem = nM
		str_1 = ""
		while(quo!=0):
			rem = int(quo % 2)
			quo = int(quo / 2)
			str_1 = str(rem) + str_1
		while(len(str_1)!=whatBit*2):
			str_1="0" + str_1
		tempStr = str_1
		str_1 = ""
		for i in range(len(tempStr)): # 1의보수 변환
			if(tempStr[i] == "0"): str_1 += "1"
			else: str_1 += "0"
		arrtwo = []
		for i in range(len(str_1)):
			arrtwo.append(int(str_1[i]))
		# 2의보수 변환
		arrtwo[len(arrtwo)-1] += 1
		if(arrtwo[len(arrtwo)-1] == 2): # 올림수 있을시
			arrtwo[len(arrtwo)-1] = 0 # 그자리는 0
			for i in range(1, len(arrtwo)):
				arrtwo[len(arrtwo)-1-i] += 1
				if(arrtwo[len(arrtwo)-1-i] == 2):
					arrtwo[len(arrtwo)-1-i] = 0
					continue
				else:
					break
		else: # 올림수 없음
			pass
		strLeng = len(str_1)
		str_1 = ""
		for i in range(strLeng):
			str_1 += str(arrtwo[i])
		return str_1
```

<br>

### 3. 종료, 시작 버튼

* 계산하는 부분이 중요. 나머지는 중요X
  * 승수Bit에 0하나를 더 붙혀서 00, 01, 10, 11 인지 확인하며 각각 다르게 연산하는게 큰 특징이다.

```python
def btnExit():
	root.destroy()

def btnStart():
	# 먼저, 초기화 시작
	inputMultiplicand = entry_Multiplicand.get() # 피승수 가져옴
	inputMultiplier = entry_Multiplier.get() # 승수 가져옴
	entry_Multiplicand.delete(0, END)
	entry_Multiplier.delete(0, END) # 지워줘야 다시 적기 편해서 지움.
	txtResult.delete("1.0", END)

	whatBit = bit_var.get() # int형임. whatBit
	initNum = "0" # 초기화
	calTotalNum = "" # 부분합
	printLine = "------" # 프린트 선
	printAcc = "" # 프린트 부호 공백
	printBin = " " # 프린트 공백
	initPrintBin = " " 
	initPrintAcc = ""
	lastPrintBin = "            "
	for i in range(whatBit*2-1):
		initNum = initNum + "0"
		printLine = printLine + "-"
		lastPrintBin = lastPrintBin + " "
	for i in range(4): # 4칸정도 띄어서 표시하자.
		printAcc = printAcc + " "
		printBin = printBin + " "
		initPrintBin = initPrintBin + " "
		initPrintAcc = initPrintAcc + " "
	for i in range(whatBit):
		initPrintBin = initPrintBin + " "
		initPrintAcc = initPrintAcc + " "

	totalNum = 0
	multiplicand = convertDecimal(inputMultiplicand)
	multiplier = convertDecimal(inputMultiplier)

	# 초기 출력 시작
	initPrint = ""
	initPrint += initPrintBin + inputMultiplicand+ printBin + " "+ f"{multiplicand}" + '\n'
	initPrint += "x" + initPrintAcc + inputMultiplier + "0" + printBin + f"{multiplier}" + '\n'
	initPrint += printLine + '\n' + printBin + initNum + '\n'
	inputMultiplier = inputMultiplier + "0" # 승수엔 0 하나 추가해야함.
	calTotalNum = initNum
	# 초기 출력 끝

	# 계산시작
	multiplierIndex = len(inputMultiplier)-1 # 마지막 index
	initIndex = multiplierIndex

	while(multiplierIndex != 0):
		calData = inputMultiplier[multiplierIndex-1:multiplierIndex-1+2] # 끝자리 2개 자른 문자열
		if(calData == "00"): # 피승수를 왼쪽 Shift
			multiplicand = multiplicand << 1
			inputMultiplicand = convertBinary(multiplicand, whatBit)
			# 0 더하기 출력, 부분합 출력
			initPrint += "+" + printAcc + initNum + '\n' + printLine + '\n' + printBin+calTotalNum + '\n'
		elif(calData == "01"): # 피승수를 누적 부분곱에 더한 후 피승수를 왼쪽 Shift
			totalNum = convertDecimal(calTotalNum)
			totalNum = totalNum + multiplicand
			calTotalNum = convertBinary(totalNum, whatBit)
			if(initIndex == multiplierIndex): initPrint += "+" + printAcc+printBin+inputMultiplicand + '\n' # 피승수 더하기 출력
			else: initPrint += "+" +printAcc + inputMultiplicand + '\n' # 피승수 더하기 출력
			# 부분합 출력
			initPrint += printLine + '\n' + printBin+calTotalNum + '\n' 
			multiplicand = multiplicand << 1
			inputMultiplicand = convertBinary(multiplicand, whatBit)
		elif(calData == "10"): # 피승수를 누적 부분곱에 뺀 후 피승수를 왼쪽 Shift
			totalNum = convertDecimal(calTotalNum)
			totalNum = totalNum - multiplicand
			calTotalNum = convertBinary(totalNum, whatBit) # 문제 발견
			if(initIndex == multiplierIndex): initPrint += "-" + printAcc+printBin+inputMultiplicand +'\n' # 피승수 더하기 출력
			else: initPrint += "-" +printAcc + inputMultiplicand +'\n' # 피승수 더하기 출력
			# 부분합 출력
			initPrint += printLine + '\n' + printBin+calTotalNum + '\n' 
			multiplicand = multiplicand << 1
			inputMultiplicand = convertBinary(multiplicand, whatBit)
		elif(calData == "11"): # 피승수를 왼쪽 Shift
			multiplicand = multiplicand << 1
			inputMultiplicand = convertBinary(multiplicand, whatBit)
			# 0 더하기 출력, # 부분합 출력
			initPrint += "+" + printAcc + initNum + '\n' + printLine + '\n' + printBin+calTotalNum + '\n'
		multiplierIndex -= 1
	initPrint += lastPrintBin+ f"{totalNum}" + '\n'
	# 결과 표시해주는 곳.
	txtResult.insert(END,initPrint)
```

<br>

### 4. GUI

```python
# GUI 설정
root = Tk()
root.title("BoothAlg")
root.geometry("1450x500")

label0 = Label(root, text="입력은 2진수로 입력하세요.(음수는 2의보수로 입력)")
label0.grid(row=0, column=4, padx=3, pady=5)

label1 = Label(root, text="피승수 : ")
label1.grid(row=0, column=0)
entry_Multiplicand = Entry(root, width=30)
entry_Multiplicand.grid(row=0, column=1)

label2 = Label(root, text="승수 : ")
label2.grid(row=1, column=0)
entry_Multiplier = Entry(root, width=30)
entry_Multiplier.grid(row=1, column=1)

# 16bit를 기본값으로 radioButton 설정
label3 = Label(root, text="Bit를 선택하세요")
label3.grid(row=0, column=2, padx=5)
bit_var = IntVar() # 여기에 int 형으로 값을 저장한다.
radio_4bit = Radiobutton(root, text="4Bit",width=3, value=4, variable=bit_var)
radio_8bit = Radiobutton(root, text="8Bit",width=3, value=8, variable=bit_var)
radio_16bit = Radiobutton(root, text="16Bit",width=3, value=16, variable=bit_var)
radio_32bit = Radiobutton(root, text="32Bit",width=3, value=32, variable=bit_var)
radio_64bit = Radiobutton(root, text="64Bit",width=3, value=64, variable=bit_var)
radio_16bit.select() # 기본값 선택
radio_4bit.grid(row=1, column=2)
radio_8bit.grid(row=2, column=2)
radio_16bit.grid(row=1, column=3)
radio_32bit.grid(row=2, column=3)
radio_64bit.grid(row=3, column=3)

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



