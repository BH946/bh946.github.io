---
title:  "[C++]Replacement_Selection(대체선택=정렬)"
categories : Algorithm
tag : [Replacement Selection, 대체선택, 정렬]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## Replacement Selection

* **입력 조건** : Buffer Size m = 5, 첫 줄은 정렬할 값의 개수 n(1<=n<=100), 둘째줄은 공백으로 구분된 길이 n인 정수의 순열을 나타냄.(정수 범위 : 0~100)
* **출력 조건** : 첫 줄은 생성된 run의 개수 r(Integer), 이어지는 r개의 행에 각 run의 내용들 출력
* **출력화면, 개념을** 참고해서 알고리즘 이해.



#### 출력화면

```c++
// 입력
4
10
47 2 88 88 90 29 42 14 19 16
15
39 90 21 52 93 32 99 79 13 55 30 25 45 4 68
20
10 13 35 84 3 8 86 15 91 64 68 7 88 60 13 73 42 57 37 17
20
66 99 2 76 43 46 21 57 42 76 35 13 72 41 96 63 81 64 17 8

// 출력
2
2 29 42 47 88 88 90 
14 16 19 
3
21 32 39 52 79 90 93 99 
13 25 30 45 55 68 
4 
3
3 8 10 13 15 35 64 68 84 86 88 91 
7 13 37 42 57 60 73 
17 
3
2 43 46 57 66 76 76 99 
13 21 35 41 42 63 64 72 81 96 
8 17 
```



### 1. 개념

꼭 필요한 알고리즘은 아니고 어마어마한 파일의 정보들을 한정된 메모리로서 자료를 보다 효율적으로 처리하기 위한 방식 중 하나이다.(파일처리론)

**예를 들어 100개의 자료가 있는데 메모리는 5개 밖에 저장하지 못할때 100개의 자료를 모두 정렬하는 방법은**

1. 5개씩 읽어와 따로 정렬을 수행한다. 그러면 20개의 각각 5개씩 정렬된 파일이 만들어진다.

2. 5개 파일씩 각 파일의 원소들을 하나씩 가져와 정렬하여 4개의 25개씩 정렬된 파일을 만든다.

3. 2번과 같은 방식으로 4개의 파일을 합병 정렬하여 하나의 정렬이 완료된 파일을 만든다.

**예로 5 8 4 6 1 9 2 7 3 이렇게 9개의 자료가 있을때** 

1. 메모리 한계가 5개라면 5 8 4 6 1 을 우선 가져와서 분할 파일을 생성하여(run1) 가장 작은 수를 써준다. 
   run1 : 1 그러면 비게되는 1 의 자리에 다음 수 9 를 가져와서 5 8 4 6 9 에서 가장 작은 수인 4 를 써주고

2. run1 : 1 4.. 다시 비게되는 4 의 자리에 다음 수 2 를 가져오는데 5 8 6 9 2 이때 run1 에 이미 1 4 가 쓰여졌으므로 가장 작은 수인 2 를 쓰게되면 1 4 2 가 되어서 정렬이 무너지게 된다.

이때 2 에 대한 처리 방법에 따라 대체 선택과 자연 선택의 두가지로 나누어지게 된다.



#### 대체 선택을 이용한 정렬의 수행 방법

1. 원본 파일로부터 비어있는 메모리에 자료를 채운다.

2. run 파일 생성(자료가 많기 때문에 분할되는 파일이다. 보통 run1, run2, run3.. 으로 정한다.)

3. 모든 메모리를 활성화
4. 활성화 되어 있는 메모리의 값 중 가장 작은 수를 run 파일에 쓴다.
5. 하나가 run 파일에 쓰여짐으로서 비게 되는 메모리에 원본 파일의 자료를 가져온다.
6. 가져온 자료가 run 파일 마지막에 쓰여진 수보다 크거나 같다면 4 ~ 5 를 반복한다.
7. 가져온 자료가 run 파일 마지막에 쓰여진 수보다 작다면 해당 메모리를 동결한다.
8. 활성화된 메모리가 남아있다면 4 ~ 5 를 반복한다.
9. 메모리가 모두 동결되면 메모리에 자료가 남아있거나 파일에 자료가 남아있다면 다시 2번부터 반복한다.
10. 분할된 x 개의 run 파일을 병합 정렬을 통하여 하나의 완성된 정렬 파일을 만든다.



### 2. replacement.cpp

#### main함수

```c++
#pragma warning(disable:4996)
#include <iostream>
#include <fstream>
#include <sstream>
#include <algorithm>
using namespace std;

#define buffer_size 5

bool compare(int a, int b);
int minIndexCal(int arr[], int frozen[]); // 버퍼속 동결 피해야함.
int sortFrozenCal(int arr[], int frozen[], int tempArr[]); // 2번을 위한 동결속 정렬
int sortCal(int arr[], int frozen[], int tempArr[]); // 3번을 위한 동결 없이 정렬 함수
void fileRead(string fileArr[]); // 파일 읽어서 배열에 담아두는 함수

int main() {
	// input_txt 읽기
	string* fileArr;
	fileArr = new string[102];
	fileRead(fileArr); // 파일 읽기

	int frozen[102]; // 레코드가 동결되었는지 확인 배열
	for (int i = 0; i < 102; i++) { // 초기화
		frozen[i] = { -1 }; // -1로 초기화
	}
	int inputArr[102][102]; // input에 잠시 담아둘 배열
	int runArr[102][102]; // run 개수 잠시 담아둘 배열
	for (int i = 0; i < 102; i++) {
		for (int j = 0; j < 102; j++) {
			runArr[i][j] = { -1 }; // -1로 초기화
		}
	}
	int runCountArr[102] = { 0 }; // run 카운트 배열 0으로 초기화

	int frozenIndex = 0; // 레코드 동결 배열의 인덱스 따로 저장
	int runIndex = 0; // 런 배열 인덱스 따로 저장
	int runCountIndex = 0; // 런카운트 배열 인덱스 따로 저장

	int caseNum = stoi(fileArr[0]); // Test Case의 개수
	int	testNum; // test할 Replacement Selection 개수

	int fileArrIndex_test = 1; // fileArr에서 testNum구하기 위한 index
	int fileArrIndex_input = 2; // fileArr에서 inputArr에 담아둘 값 구하기 위한 index

	for (int i = 0; i < caseNum; i++) { 
		if (i == 0) { // testNum에 알맞은 값 넣는 조건문
			testNum = stoi(fileArr[fileArrIndex_test]);
			fileArrIndex_test = fileArrIndex_test + 2;
		}
		else {
			testNum = stoi(fileArr[fileArrIndex_test]);
			fileArrIndex_test = fileArrIndex_test + 2;
		}
		////////////////////////////////
		for (int l = 0; l < 102; l++) { // 초기화
			frozen[l] = { -1 };
		}
		frozenIndex = 0; // 레코드 동결 배열의 인덱스 저장
		int check = 0;
		////////////////////////////////
		if (testNum < 1 || testNum > 100) return 0; // 예외처리 종료 (1~100 사이 숫자만 허용하기 때문)

		// caseNum 초기화, testNum 초기화 끝나고, 마지막 inputArr배열 초기화 부분시작
		string str = fileArr[fileArrIndex_input];
		fileArrIndex_input = fileArrIndex_input + 2;
		int start = 0; // start
		int end = str.find(' '); // end
		int num = 0; // 추출 숫자 초기화
		for (int j = 0; j < testNum; j++) {
			num = stoi(str.substr(start, end)); // start~end 추출(숫자)
			start = end+1;
			end = str.find(' ', start); // start인덱스 부터 다시 ' ' 찾아서 반환
			inputArr[i][j] = num;
			if (inputArr[i][j] < 0 || inputArr[i][j] > 100) return 0; // 예외처리 종료 (1~100 사이 숫자만 허용하기 때문)
		} // inputArr배열까지 초기화 완료!
		
		// 만약 testNum이 5도 안된다? 그럼 동결이 필요없음. 이부분 작성먼저.
		if (testNum < 5) {
			for (int j = 0; j < testNum; j++) {
				runArr[runIndex][j] = inputArr[i][j];
			}
			sort(runArr[runIndex], runArr[runIndex] + testNum, compare); 
			runCountArr[runCountIndex] = 1;
			runIndex++;
			runCountIndex++;
			continue;
		} // 예외처리 끝

		int bufferArr[buffer_size]; // m=5인 버퍼이다.
		for (int j = 0; j < buffer_size; j++) {
			if (j < buffer_size) { // j=4까지 버퍼에 채워줌
				bufferArr[j] = inputArr[i][j];
			}
		} // 버퍼 다채움.


		// 현재 여기까지 오는경우는 버퍼 5개 다채워진 경우이다. (애초에 버퍼가 다 안채워지면 RUN이 만들어질 필요도 없음.)
		int count = 1; // Run 1~?번까지 있는지 셈.
		int col = 0; // runArr에 쓰기위한 열 index
		for (int j = 0; j < testNum - 5; j++) { // 버퍼 5개 뺀 뒤의 입력값 읽기
			// 동결 5개인지 확인하기
			if (frozenIndex == 5) { 
				// 1번 Run2로 넘어가서 버퍼에 남은 동결5개부터 시작하면됨.
				count++;
				runIndex++; // Run2로 넘어가기.

				for (int i = 0; i < 102; i++) { // 초기화
					frozen[i] = { -1 };
				}
				frozenIndex = 0; // 레코드 동결 배열의 인덱스 저장
				col = 0;
			}

			// 알고리즘
			if (j + 1 == testNum - 5) { // 마지막일때 = testNum 데이터 다 버퍼로 감!
				// 2번 버퍼에 동결도 0이아니고, 남은게 몇개있다 ? ?
				// 남은거 현재 Run에 넣어야함.(동결은 빼고 비교!!)
				// 일단 먼저, 원래처럼 비교하는건 해야함.
				int minIndex = minIndexCal(bufferArr, frozen); // 버퍼속 최소값 구하기
				runArr[runIndex][col] = bufferArr[minIndex]; // 런에 넣기.

				bufferArr[minIndex] = inputArr[i][j + 5]; // 버퍼 빈자리에 새로 숫자넣음
				if (bufferArr[minIndex] < runArr[runIndex][col]) {
					// 동결O 
					frozen[frozenIndex] = minIndex;
					frozenIndex++;
				}
				else {
					// 동결X
				}
				int tempArr[buffer_size];
				for (int k = 0; k < buffer_size - frozenIndex; k++) { // 동결 아닌만큼 반복
					sortCal(bufferArr, frozen, tempArr); // tempArr로 sort해서 가져오는 함수
					runArr[runIndex][k + col + 1] = tempArr[k];
				}
				col++;
				continue;
			}

			int minIndex = minIndexCal(bufferArr, frozen); // 버퍼속 최소값 구하기
			runArr[runIndex][col] = bufferArr[minIndex]; // 런에 넣기.
			bufferArr[minIndex] = inputArr[i][j + 5]; // 버퍼 빈자리에 새로 숫자넣음
			if (bufferArr[minIndex] < runArr[runIndex][col]) {
				// 동결O 
				frozen[frozenIndex] = minIndex;
				frozenIndex++;
				col++;
				continue;
			}
			else {
				// 동결X
				col++;
				continue;
			}
			col++;
		}
		// 3번 testNum끝났는데 동결이 0개 아니다? 남은 동결끼리 정렬해서 Run(다음)에 넣어주면 됨.
		// => 한마디로 남은 버퍼에 있는 숫자들 정렬.
		// 그전에 Run2로 넘기기
		count++;
		runIndex++; // Run2로 넘어가기끝.

		if (frozenIndex != 0) { // 동결이 아예 없어야 끝난건데, 동결이 한개라도 있으면 정렬 해야함.
			int tempArr[buffer_size];
			for (int j = 0; j < frozenIndex; j++) {
				sortFrozenCal(bufferArr, frozen, tempArr); // 동결끼리 비교 정렬 함수
				runArr[runIndex][j] = tempArr[j];
			}
		}
		runCountArr[runCountIndex] = count; // Run이 몇개씩 만들어졌는지 저장하는 배열
		runIndex++;
		runCountIndex++;
	}
    
	// 출력하기 => txt로
	ofstream os;
	os.open("replacement_output.txt");
	int startIndex = 0;
	int runCountArrLastIndex = 0; // 배열길이 구함.
	while (runCountArr[runCountArrLastIndex] != 0) {
		runCountArrLastIndex++;
	}
	for (int i = 0; i < runCountArrLastIndex; i++) {
		os << runCountArr[i];
		os << endl;
		for (int j = 0; j < runCountArr[i]; j++) { // Run 갯수만큼 반복
			int lastIndex = 0;
			while (runArr[startIndex][lastIndex] != -1) {
				os << runArr[startIndex][lastIndex];
				os << " ";
				lastIndex++;
			}
			startIndex++;
			os << endl;
		}
	}

	os.close();

	return 0;
}
```



#### 나머지 함수들

```c++
bool compare(int a, int b) {
	return a < b;
}

int minIndexCal(int arr[], int frozen[]) { // 버퍼속 동결 피해야함.
	int min = 101;
	int index = 0;
	int frozenIndexArr[102];
	for (int i = 0; i < 102; i++) { // 초기화
		frozenIndexArr[i] = { -1 };
	}
	// 동결 체크 먼저하겠음.
	for (int i = 0; i < buffer_size; i++) {
		if (frozen[i] != -1) { // -1이 아니라면 동결있다는 의미.
			frozenIndexArr[index] = frozen[i];
			index++;
		}
	} // frozenIndexArr배열에 0 index부터 차례로 동결된 index자리 저장

	for (int k = 0; k < buffer_size; k++) {
		int num = 0;
		for (int j = 0; j < buffer_size; j++) {
			if (frozenIndexArr[j] == k) num = 1; // 동결인건 넘어가기.(index비교임)
		}
		if (num == 1) continue;
		if (min > arr[k]) { // 최소값 찾기
			min = arr[k];
			index = k;
		}

	}
	return index;
}

int sortFrozenCal(int arr[], int frozen[], int tempArr[]) { // 2번을 위한 동결속 정렬
	int min = 101;
	int index = 0;
	int frozenIndexArr[102];
	for (int i = 0; i < 102; i++) { // 초기화
		frozenIndexArr[i] = { -1 };
	}
	// 동결 체크 먼저하겠음.
	for (int i = 0; i < buffer_size; i++) {
		if (frozen[i] != -1) { // -1이 아니라면 동결있다는 의미.
			frozenIndexArr[index] = frozen[i];
			index++;
		}
		tempArr[i] = 101;
	} // frozenIndexArr배열에 0 index부터 차례로 동결된 index자리 저장
	index = 0;
	for (int k = 0; k < buffer_size; k++) {
		for (int j = 0; j < buffer_size; j++) {
			if (frozenIndexArr[j] == k) { // 동결인걸 tempArr에 넣기.
				tempArr[index] = arr[k];
				index++;
			}
		}
	}
	sort(tempArr, tempArr + buffer_size, compare);
	return 0;
}

int sortCal(int arr[], int frozen[], int tempArr[]) { // 3번을 위한 동결 없이 정렬 함수
	int min = 101;
	int index = 0;
	int frozenIndexArr[102];
	for (int i = 0; i < 102; i++) { // 초기화
		frozenIndexArr[i] = { -1 };
	}
	// 동결 체크 먼저하겠음.
	for (int i = 0; i < buffer_size; i++) {
		if (frozen[i] != -1) { // -1이 아니라면 동결있다는 의미.
			frozenIndexArr[index] = frozen[i];
			index++;
		}
		tempArr[i] = arr[i];
	} // frozenIndexArr배열에 0 index부터 차례로 동결된 index자리 저장
	for (int k = 0; k < buffer_size; k++) {
		for (int j = 0; j < buffer_size; j++) {
			if (frozenIndexArr[j] == k) { // 동결인건 101로 만들기.
				tempArr[k] = 101;
			}
		}
	}
	sort(tempArr, tempArr + buffer_size, compare);
	return 0;
}

void fileRead(string fileArr[]) { // file읽고, 배열에 담는 함수
	ifstream is; // txt 읽기 변수 선언
	is.open("replacement_input.txt"); // file 오픈	
	int index = 0;
	while (getline(is, fileArr[index])) {
		index++;
	}
	is.close();
}
```



### 3. 프로그램 구현의 문제점과 해결법



**1) input_txt파일의 숫자들을 읽어와서 가공을 해야하는데 어떻게 읽어올지 난감했습니다. (int형 배열로 읽어올지, string형 배열로 읽어올지 등)**

=> string형 배열로 해당 데이터를 전체 읽어와서 stoi메소드를 사용했습니다. 그래서 정수로 형 변환을 통해 데이터를 사용하였습니다.



**2) 숫자가 5개 이하인 것은 동결이 될 리가 없기 때문에, 이부분을 따로 예외처리로 코드를 작성했습니다.**

해결방법은 if문을 통해 5개 이하이면, 동결처리 하는 부분을 빼버리고 코드 작성 후 바로 continue;를 통해서 다음 case로 넘어갔습니다.



**3) Run1과 Run2를 적절한 타이밍에 만들고, 넘어가는 것이 어려웠습니다.**

해결방법으로는 3가지 정도로 나눴습니다.

1. 계속 버퍼의 숫자를 바꿔가며 진행중이라면, 진행중인 for문 안에서 if문을 이용해 동결 5개가 되었는지 확인하고, Run2로 넘어갔습니다.

2. 숫자 진행이 마지막이고 버퍼에 동결이 0개가 아니고 남은게 몇 개있다면, 동결은 빼고 비교해서 남은거 현재 Run에 넣도록 코드를 작성했습니다.

3. 2번과 동일하게 숫자 진행 마지막이고 버퍼에 동결이 0개가 아닌데 동결은 어떻게 되냐면, 동결은 동결끼리 정렬해서 Run(다음)에 넣으면 됩니다.



**4) 마지막으로 동결을 하는방법을 생각하는데 어려움이 있었습니다.**

해결방법으로는 동결시키는 코드가 자주 등장을 하게 될 것 같아서, 따로 함수로 작성하였습니다. 일부러 처음에 동결배열을 –1로 초기화 시킴으로써 체크표시를 –1이 아니면 동결이 있다는 의미로 해석을 하게끔 이용해서 코드를 작성하였습니다.