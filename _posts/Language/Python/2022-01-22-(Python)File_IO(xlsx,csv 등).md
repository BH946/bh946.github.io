---
title:  "[Python]File_IO(xlsx,csv 등)"
categories : PY
tag : [파이썬, os, workbook, pandas]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## csv, xlsx등 파일 입출력(관리)


### 1. OS

#### OS 모듈 준비물

**OS 모듈은 아마 기본 내장되어 있을거임.**

**기본 import**

```python
import os
```

<br>

**간단한 경로지정 방법(참고)**

* 상대경로(VScode-자바스크립트의 경우..)

  * . 한개 : 한단계 아래 디렉토리를 의미
  * .. 두개 : 한단계 위의 디렉토리를 의미

  ```html
  <!-- 상대적인 경로(현재 경로에서 찾아가는,,) -->
  <!-- JSCRIPT폴더 -> html폴더 -> index.html파일이 현재 실행파일이며 현재경로이다. -->
  <!-- JSCRIPT폴더 -> hw폴더 -> hw2_1.js파일이 존재하는 상태 -->
  <script type="text/javascript" src="..\hw\hw2_1.js"></script>
  ```

* 절대경로

  ``` python
  # 절대적인 경로
  path_dir = 'C:\\Users\\KoBongHun\\Downloads'
  path_dir = 'C:/Users/KoBongHun/Downloads'
  ```

  ```python
  # 라이브러리 활용
  import os
  
  simp_path = 'demo/which_path.docx'
  abs_path = os.path.abspath(simp_path) # abspath() 이용해서 절대경로 가져오기
  
  print(abs_path)
  # abspath() 함수의 출력은 현재 작업 디렉토리에 상대적인 절대 경로의 문자열 값을 반환합니다.
  ++
  # 아래 코드 실행시 내 위치가 어디든, 해당 파이썬 스크립트 파일의 절대경로를 구할 수 있다.
  path = os.path.dirname(os.path.abspath(__file__)) 
  print(path)
  ```

<br>

#### OS 사용법

**파일 경로의 원하는 파일들 리스트로 반환**

```python
file_list = os.listdir(path_dir) # 해당 경로 파일들 이름 리스트로 반환
```

* endswith는 기본 내장함수(str의)

  ```python
  [file for file in file_list if file.endswith('.csv')] # file_list의 경로의 '.csv'확장자 파일들 리스트로 반환
  ```

<br>

**경로존재 확인 및 폴더 추가**

```python
os.mkdir('경로 이름') # 폴더추가 메소드
os.path.exists('경로 이름') # 존재 확인
if not os.path.exists('경로 이름'): # 경로 없다면 그 경로(폴더)를 만드는 구조이다.
	os.mkdir('경로 이름')
```

<br>

**파일 삭제**

```python
os.remove(r"C:\Users\..경로...\product_jinnyhands.xlsx") # 삭제
```

<br>

**응용**

* 원하는 경로에 '.csv'확장자 파일 발견 할때까지 무한 반복.

  ```python
  file_list_csv = []
  while(len(file_list_csv) == 0): # 이건 반드시 다운로드폴더에 csv파일 없다는 가정.
      path_dir = 'C:\\Users\\KoBongHun\\Downloads'
      file_list = os.listdir(path_dir)
      file_list_csv = [file for file in file_list if file.endswith('.csv')]
  ```

* 보통 파일 삭제 방법.

  ```python
  try:
     os.remove(r"C:\Users\..경로...\product_jinnyhands.xlsx") # 삭제
  except FileNotFoundError:
     print("product_jinnyhands.xlsx 없음.")
  ```

<br>


### 2. Workbook(엑셀.Xlsx을 주로)

#### Workbook 준비물

**기본 import**

```python
from openpyxl import Workbook # 데이터 쓰기에 사용
from openpyxl import load_workbook # 데이터 읽기에 사용
```

<br>

#### Workbook 사용법

**데이터 쓰기(write)**

```python
# 엑셀파일 생성(쓰기)
wb = Workbook()
# 이름이 있는 시트를 생성
ws = wb.create_sheet('생성시트')
# Sheet1에다 입력
ws = wb.active
ws['A1'] = '숫자'
# 행 단위로 추가
ws.append([1,2,3])
# 셀 단위로 추가
ws.cell(5, 5, '5행5열')
# 저장
wb.save("product_jinnyhands.xlsx")
# 종료
wb.close()
```

<br>

**데이터 읽기(read)**

```python
# data_only=True로 해줘야 수식이 아닌 값으로 받아옴. (엑셀에 함수나오는 그거 얘기임)
wb = load_workbook("product_jinnyhands.xlsx", data_only=True)
# 시트 이름으로 불러오기 
ws = wb['Sheet1']
# 활성화된 sheet로 불러오기
ws = wb.active
# 셀 주소로 값 출력
print(ws['B2'].value)
# 셀 좌표로 값 출력(3행 2열의 값)
print(ws.cell(3, 2).value)

# 지정한 셀의 값 출력
get_cells = ws['B3' : 'B6']
for row in get_cells:
	for cell in row:
		print(cell.value)

# 모든 행 단위로 출력
for row in ws.rows:
	print(row)

# 모든 열 단위로 출력
for column in ws.columns:
print(column)

# 모든 행과 열 출력
all_values = []
for row in ws.rows:
	row_value = []
	for cell in row:
		row_value.append(cell.value)
	all_values.append(row_value)
print(all_values)
# 종료
wb.close()
```

<br>

**부가정보**

* 최대 행 구하기

  ```python
  ws.max_row # 최대행 구함.
  for i in range(1, ws.max_row+1): # 이런식으로 응용. (1행 부터 마지막행 접근 i)
  ```


<br>

### 3. CSV

#### CSV 준비물

**기본 import**

```python
import csv
```

<br>

#### CSV 사용법

**csv Open(오픈) 방법**

* 'r' 형식 (read)

  * reader = csv.reader(d) : 기본적으로 객체 생성방식

  * 아래 예시는 csv파일 읽어서 xlsx로 저장,,!(응용한것)

  ```python
  # Workbook은 저장위해 그냥 예시로 사용하겠다.
  wb = Workbook()
  ws = wb.active
  with open('jinnyhands.csv', 'r') as d: # with로 물론 안해도됨.
      for row in csv.reader(d): # 한 행씩.. 결국 전체다 저장
          ws.append(row)
  # with방법 말고는 아래.
  f = open('data.csv', 'r', encoding='utf-8')
  rdr = csv.reader(f)
  for line in rdr:
      print(line)
  f.close() 
  ```

* 'a' 형식 (append) => 당연히 없으면 파일 'w'처럼 만들어지며, 추가하는것! (String형식 사용)

  * writer = csv.writer(d) : 기본적으로 객체 생성방식

  ```python
  with open('nothing_jinnyhands.csv', 'a', newline='') as d:
      writer = csv.writer(d)
      writer.writerow(nothingList[i]) # 데이터또한 배열로 감싸둔 상태꺼 사용(2중배열)
  ```

* newline, write방식과, 배열로 저장해야,, (물론 다른방식도 있을거지만 내가 한 방식 토대로..)

  * newline=''을 통해서 데이터 저장시 '한줄' 자동으로 넘기는데 그부분을 제거. '\n'제거 느낌이죠.

    ```python
    ########## 출력형식
    # 1. newline='' 안했을시
    데이터1
    
    데이터2
    # 2. newline='' 적용시
    데이터1
    데이터2
    ```

  * write방식 (writerow, writerows)

    ```python
    # writerow (주로 사용) : 한줄(한행)에 데이터 출력.
    writer.writerow(dataList)
    # writerows : 한행출력 후 다음행에 출력,, 반복해서 데이터 출력.
    writer.writerows(dataList)
    ```

  * 데이터 저장 방식(배열 추천) => 2번 4번 형식 주로 사용중!

    ```python
    dataList = ['1234', 'aaadsf']
    datasList = [['1234'], ['aaadsf']]
    string = 'asdf'
    ##### 데이터 선언후
    writer.writerow(string) # 1. str데이터 기본 사용하며 a,s,d,f 이런식으로 저장.
    writer.writerow(dataList) # 2. []로 넣어서 1234,aaadsf 이런식으로 저장.(정상)
    writer.writerow(dataList[0] # 3. []의 요소 접근함으로 1번처럼 str 형식으로 저장.
    writer.writerow(datasList[0]) # 4. 2중[]임으로 요소접근해도 []접근!! 2번과 동일한 출력.(정상)
    ```

<br>

**응용(네이버 시가총액 기록,,)**

* **새로보는 함수** : strip() : 불필요한 문자 삭제

```python
import csv
import requests
from bs4 import BeautifulSoup

url = "https://finance.naver.com/sise/sise_market_sum.nhn?sosok=0&page="

filename = "시가총액1-200.csv"
f = open(filename, "w", encoding="utf-8-sig", newline="") # utf-8-sig는 엑셀 한글깨질때 적용해주면 됌
writer = csv.writer(f)

title = "N	종목명	현재가	전일비	등락률	액면가	시가총액	상장주식수	외국인비율	거래량	PER	ROE".split("\t")
# 출력 : ["N", "종목명", "현재가", ...]

writer.writerow(title) # 데이터 쓰기

for page in range(1,5):
    res = requests.get(url + str(page))
    res.raise_for_status()
    soup = BeautifulSoup(res.text, "lxml")

    data_rows = soup.find("table", attrs={"class":"type_2"}).find("tbody").find_all("tr")
    for row in data_rows:
        columns = row.find_all("td")
        if len(columns) <= 1: # 의미없는 데이터 skip
            continue
        data = [column.get_text().strip() for column in columns] # 한줄for문 and strip으로 불필요한 문자열 없앰
        # print(data)
        writer.writerow(data) # 데이터 쓰기
```

<br>

### 4. Pandas(판다스) - xlsx(엑셀), csv 둘다 활용

* 다양한 데이터 형식(주로 데이터프레임)

#### pandas 준비물

```python
pip install pandas
```

**기본 import**

```python
import pandas as pd # pandas의 경우 보통 pd로 이름변경해서 사용하는것이 관례임.
```

<br>

#### pandas 사용법

**함수들**

* 상위, 하위 호출(**head, tail**)

  ```python
  print(df.head(10)) # 명령어를 호출하여 상위 10개의 데이터를 불러옵니다.
  print(df.tail(10)) # 하위 출력
  ```

* 차원(486 rows × 29 column), 통계(**shape, describe**)

  ```python
  # 2차원 행렬로 구성된 데이터 프레임의 크기 출력 ( number_of_rows, number_of_columns )
  print(f"차원 수 : {df.shape}") # 행 x 열
  print(f"행의 수 : {df.shape[0]}") # 행
  print(f"열의 수 : {df.shape[1]}") # 열
  print(df.describe()) # 데이터프레임의 표현적인 통계(descriptive statistics) 보기
  ```

* df에 원하는 필드와 원하는 레코드만 df_sub로 저장

  ```python
  df_sub = df[['역번호','역명','구분','08시-09시']]
  df_sub = df_sub[30000:40000]
  ```

* '구분'에 따른 평균,최대,최소 구하기(= '구분'에 그룹화 후 평균,최대,최소)

  ```python
  print(df_sub.groupby(['구분'],as_index=True).mean()) # mean() 평균
  # print(df_sub.groupby(['구분'],as_index=True).min()) # min() 최소
  # print(df_sub.groupby(['구분'],as_index=True).max()) # max() 최대
  ```

* 특정 컬럼값을 만족하는 데이터프레임을 출력하기(**loc, iloc**)

  ```python
  df.loc[df['column_name'] == some_value]
  # ex) 하단역(102)에 대한 승하차정보만 갖게 필터링
  df_hadan = df.loc[df['역번호'] == 102]
  # ex) 두 가지 이상의 필터를 혼합
  df_hadan_sub = df_hadan.loc[(df_hadan['08시-09시'] >= 1000) & (df_hadan['18시-19시'] >= 2000)]
  
  # df.iloc[start_row:end_row, start_col:end_col]
  mr.iloc[:,1:2] # mr은 df형식
  mr.iloc[:,0:2] # 이 열만 보여줌
  mr[1:2] # 이 행만 보여줌
  ```

* 행, 열 교환(**Transpose** 메소드)

  ```python
   df.T
  ```


* 열 이름 바꾸기(**rename**)

  ```python
  usa_airports = ~~.loc[:,[1,4,6,7]] # df로 구성된 usa_airports
  usa_airports.rename(columns={1: 'name', 4: 'id', 6: 'latitude', 7: 'longitude'}, inplace=True)
  ```
  
* **리스트로 변환**

  ```python
  listA = dfA.values.tolist() # 데이터프레임의 값들을 tolist()한다.
  ```

* **해당 데이터에 원하는 값이 있는지 찾기**

  ```python
  src = usa_airports[usa_airports.name.str.contains('San Francisco International Airport')]
  # 더 나아가서 공항 이름 찾은 행 데이터 src를 공항코드로 접근하기
  src = src.iloc[:,1].values[0] # 공항코드 값으로 변경
  ```


<br>

**csv->xlsx (read_csv(), to_excel()) : csv읽기, 엑셀로 변환** 

* **read_csv()**

  ```python
  md = pd.read_csv('부산교통공사_시간대별 승하차인원_20210801.csv', encoding='cp949')
  ```

  * 속성들 : header, encoding, low_memory... 등

    ```python
    header=None으로 속성을 줄 수 있다.
    encoding='cp949'
    low_memory=False
    ```

* 우선 ExcelWriter메소드 사용을 위해 xlsx파트에서 import 따라하기.
* Workbook + Pandas인 응용이라 생각하면 됨.

```python
# encoding은 항상 고려해줘야함. (한글이 깨질 수 있기 때문)
md = pd.read_csv('부산교통공사_시간대별 승하차인원_20210801.csv', encoding='cp949')
writer = pd.ExcelWriter('product_jinnyhands.xlsx') # 이때 엑셀 만들어짐.

md.to_excel(writer, index = False) # 엑셀에 데이터 삽입
writer.save() # 엑셀 데이터 저장
```

<br>

#### 집합 연산

**UNION(합집합)**

* 두가지 방법 이상이 기대될수 있다. 1) pd.concat을 사용하여 데이터프레임을 유지하거나, 2) 각 데이터에 대해 list 형태로 변환한 뒤에 계산하는 방법

```python
# pd.concat 사용

union_commute = pd.concat([P, S], ignore_index = True) # pd.concat을 이용해 P, S를 합할수 있다. ignore_index를 통해 행 인덱스 번호도 재배열!
union_commute = union_commute.drop_duplicates() # drop_duplicates메소드를 이용해 중복값 제거를 할 수 있다.
```

**INTERSECTION(교집합)**

* pd.concat은 outer로 join이 기본값으로 되어있으므로 합집합이 나온다.(outer:합집합, inner:교집합) 따라서 join을 inner로 바꿔주거나, merge함수를 사용해주면 된다.(merge의 기본값은 inner)

```python
# pd.merge 사용

intersection_commute = pd.merge(P, S)
```

**DIFFERENCE(차집합)**

* isin메소드 이용

```python
P_only = P[P.index.isin(S.index) == False]
S_only = S[S.index.isin(P.index) == False]
```

<br><br>

## Workbook() 이용한 엑셀파일 자세히 설명,,

### 1. create_file

#### 준비물

```python
from openpyxl import Workbook
```

<br>

#### 사용법

```python
wb = Workbook() # 새 워크북 생성
ws = wb.active # 현재 활성화된 sheet 가져옴
ws.title = "NadoSheet" # sheet 의 이름을 변경
wb.save("sample.xlsx") # 저장
wb.close() # 닫기

# 기본 open()메소드 이용한 방법
file = open("./hello.xlsx", "w+") # 엑셀로 만들어도 됨
file.write("hello" + "\n") # 이런식으로도 엑셀,csv 저장가능! ! !
```

<br>

### 2. sheet(시트)

**sheet 생성방식**

* create_sheet() : 생성

  ```python
  ws = wb.create_sheet() # 새로운 sheet 기본 이름으로 생성
  ws1 = wb.create_sheet("YourSheet") # 주어진 이름으로 sheet 생성
  ws2 = wb.create_sheet("NewSheet", 2) # 2번째 index에 sheet 생성
  ```

* copy_worksheet() : 복사

  ```python
  # sheet 복사
  new_ws["A1"] = "Test" # 임의로 [A1]셀에 Test기입
  target = wb.copy_worksheet(new_ws)
  target.title = "Copied Sheet"
  ```

<br>

**sheet 이름변경, 이름확인, 색상적용**

* .title : 제목변경

  ```python
  ws.title = "MySheet" # sheet 이름 변경
  ```

* sheet_properties.tabColor : 색상변경

  ```python
  ws.sheet_properties.tabColor = "ff66ff" # RGB 형태로 값을 넣어주면 탭 색상 변경
  ```

* .sheetnames : 이름 확인

  ```python
  print(wb.sheetnames) # 모든 sheet 이름 확인
  ```

**sheet 접근(Dict형태)**

```python
new_ws = wb["NewSheet"] # Dict 형태로 sheet 에 접근
```

<br>

### 3. cell(셀)

**셀에 값 입력**

```python
# A1 셀에 1 이라는 값을 입력(전부동일)
ws["A1"] = 1
ws["A1"].value = 1
ws.cell(1,1).value = 1 # ws.cell(row=1, column=1).value
ws.cell(1,1,1) # ws.cell(column=1, row=1, value=1)
```

<br>

**셀 정보, 값 출력**

```python
ws["A1"] # A1 셀의 정보를 출력
ws["A1"].value # A1 셀의 '값'을 출력, 값이 없을 땐 'None' 을 출력
ws.cell(1,1) # 정보출력(동일)
ws.cell(1,1).value # 값 출력(동일)
```

<br>

### 4. open_file(파일 load)

#### 준비물

```python
from openpyxl import load_workbook
```

<br>

#### 사용법

```python
from openpyxl import load_workbook
wb = load_workbook("sample.xlsx")
ws = wb.active

# cell 데이터 불러오기
for x in range(1, 11):
    for y in range(1, 11):
        print(ws.cell(row=x, column=y).value, end=" ") # 1 2 3
    print() # 줄바꿈

# cell 개수 모를때 (max_row or max_column을 이용!)
for x in range(1, ws.max_row + 1):
    for y in range(1, ws.max_column + 1):
        print(ws.cell(row=x, column=y).value, end=" ") # 1 2 3
    print()
```

<br>

### 5. cell_range(셀 주소)

**1줄씩 데이터 넣기**

```python
ws.append(["번호", "영어", "수학"]) # A, B, C열 순서로 적용
for i in range(1, 11):
    ws.append([i, randint(0, 100), randint(0, 100)])
1	92	59
2	67	12
... 이런식으로 적용.
```

<br>

**다양한 데이터 가져오는 방식**

```python
ws["B"] # B열 가져오기.
ws["B:C"] # B,C열 가져오기.
ws[1] # 1번째 행 가져오기.
ws[2:6] # 2행부터 6행까지 가져오기.

# 전체 rows
print(tuple(ws.rows)) # 한줄씩 가져와서 튜플로 ((<~>,<~>,,),(<~>,<~>,,)..)
for row in tuple(ws.rows): # row : (<~>,<~>,,)
    print(row[2].value) # 따라서 [2]는 row의 2번째 index 요소꺼 가져옴.

# 전체 columns
print(tuple(ws.columns)) # 한열씩 가져와서 튜플로
for column in tuple(ws.columns):
    print(column[0].value)

# 위와 동일 iter_rows(), iter_cols()
for row in ws.iter_rows(): # 전체 row
    print(row[2].value)

for column in ws.iter_cols(): # 전체 column
    print(column[0].value)

# 2번째 줄부터 11번째 줄까지, 2번째 열부터 3번째 열까지
for row in ws.iter_rows(min_row=2, max_row=11, min_col=2, max_col=3):
	print(row[index].value)
```

<br>

### 6. search(검색)

```python
for row in ws.iter_rows(min_row=2):
    # 번호, 영어, 수학
    if int(row[1].value) > 80:
        print(row[0].value, "번 학생은 영어 천재")

for row in ws.iter_rows(max_row=1):
    for cell in row:
        if cell.value == "영어":
            cell.value = "컴퓨터"
            
# 물론 예시일뿐 이방법으로만 접근할 수 있다는 의미는 아니다. 다양한 방식으로 접근 가능하다.
```

<br>

### 7. insert(추가)

**행삽입, 열삽입**

```python
ws.insert_rows(8) # 8번째 행추가.
ws.insert_rows(8, 5) # 8번째 행기준 5행 추가

ws.insert_cols(2) # 2번째 열
ws.insert_cols(2, 3) # 2번째 열기준 3열 추가
```

<br>

### 8. delete(삭제)

**행삭제, 열삭제**

```python
ws.delete_rows(8) # 8번째 행 데이터 삭제
ws.delete_rows(8, 3) # 8번째 행부터 총 3행 삭제

ws.delete_cols(2) # 2번째 열 데이터 삭제
ws.delete_cols(2, 2) # 2번째 열부터 총 2열 삭제
```

<br>

### 9. move(이동)

**데이터 다른셀로 이동**

```python
ws.move_range("B1:C11", rows=0, cols=1) # B1:C11이 0행 1열 간 것. (1열 옆으로 한칸 옮)
```

<br>

### 10. chart(차트)

#### 준비물

```python
from openpyxl.chart import BarChart, Reference, LineChart
```

<br>

#### 사용법

```python
# B2:C11 까지의 데이터를 차트로 생성
bar_value = Reference(ws, min_row=2, max_row=11, min_col=2, max_col=3)
bar_chart = BarChart() # 차트 종류 설정 (Bar, Line, Pie, ..)
bar_chart.add_data(bar_value) # 차트 데이터 추가
ws.add_chart(bar_chart, "E1") # 차트 넣을 위치 정의

# B1:C11 까지의 데이터
line_value = Reference(ws, min_row=1, max_row=11, min_col=2, max_col=3)
line_chart = LineChart()
line_chart.add_data(line_value, titles_from_data=True) # 계열 > 영어, 수학 (범례부분)
line_chart.title = "성적표" # 제목
line_chart.style = 20 # 미리 정의된 스타일을 적용, 사용자가 개별 지정
line_chart.y_axis.title = "점수" # Y축의 제목
line_chart.x_axis.title = "번호" # X축의 제목
ws.add_chart(line_chart, "E1")
```

<br>

### 11. cell_style(셀 스타일)

#### 준비물

```python
from openpyxl.styles import Font, Border, Side, PatternFill, Alignment
```

<br>

#### 사용법

**데이터 셋,,,**

```python
a1 = ws["A1"] # 번호
b1 = ws["B1"] # 영어
c1 = ws["C1"] # 수학
```

<br>

**속성**

* column_dimensions[] : 행 or 열의 노비나 넢이 설정

  ```python
  # A 열의 너비를 5로 설정
  ws.column_dimensions["A"].width = 5
  # 1 행의 높이를 50으로 설정
  ws.row_dimensions[1].height = 50
  ```

* font : 스타일 적용

  ```python
  a1.font = Font(color="FF0000", italic=True, bold=True) # 글자 색 빨강, 기울임, 진하게
  b1.font = Font(color="CC33FF", name="Arial", strike=True) # strike는 취소선
  c1.font = Font(color="0000FF", size=20, underline="single") # underline은 밑줄
  ```

* border : 테두리 적용

  ```python
  thin_border = Border(left=Side(style="thin"), right=Side(style="thin"), top=Side(style="thin"), bottom=Side(style="thin"))
  a1.border = thin_border
  b1.border = thin_border
  c1.border = thin_border
  ```

* alignment : 정렬

  ```python
  # center, left, right, top, bottom
  a1.alignment = Alignment(horizontal="center", vertical="center") # 상하, 좌우
  ```

* fill : 배경 설정

  ```python
  a1.fill = PatternFill(fgColor="00FF00", fill_type="solid") # 배경 설정
  ```

* freeze_panes : 틀 고정

  ```python
  ws.freeze_panes = "B2" # B2 기준으로 틀 고정
  ```

* merge : 병합하기

  ```python
  ws.merge_cells("B2:D2") # B2:D2 셀을 합침
  ws["B2"].value = "Merged Cell"
  ```

* unmerge : 병합 해제하기

  ```python
  ws.unmerge_cells("B2:D2") # B2:D2 셀 병합을 해제함. => B2,C2,D2로 다시 나뉨.
  ```

<br>

**예시**

```python
for row in ws.rows:
    for cell in row:
        # 각 cell에 대해서 정렬
        cell.alignment = Alignment(horizontal="center", vertical="center")
        # center, left, right, top, bottom

        if cell.column == 1: # A 번호열은 제외
            continue

        # cell 이 정수형 데이터이고 90점보다 높으면
        if isinstance(cell.value, int) and cell.value > 90:
            cell.fill = PatternFill(fgColor="00FF00", fill_type="solid") # 배경설정
            cell.font = Font(color="FF0000") # 폰트 색상 변경
```

<br>

### 12. formula(함수사용)

* data_only=True 속성 : True로 지정해야 수식(함수형태)이 아닌 데이터를 가져옴.

  ```python
  wb = load_workbook("sam_formula.xlsx", data_only=True)
  ```

**엑셀함수 사용하듯이 그대로 사용!!**

```python
ws["A2"] = "=SUM(1, 2, 3)"
ws["A3"] = "=AVERAGE(1, 2, 3)"

ws["A4"] = 10
ws["A5"] = 20
ws["A6"] = "=SUM(A4:A5)"
```

<br>

### 13. image(이미지 삽입)

* 만약 ImportError : You must install Pillow to ...이면 pip install Pillow 설치 할 것.

#### 준비물

```python
from openpyxl.drawing.image import Image
```

<br>

#### 사용법

```python
img = Image("img.png")

# C3 위치에 이미지 삽입
ws.add_image(img, "C3")
```











