---
layout: single
title:  "[Python] Practice_pandas_and_matplotlib(활용편)"
categories: PY
tag: [python, 예제, pandas, matplotlib]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

<head>
  <style>
    table.dataframe {
      white-space: normal;
      width: 100%;
      height: 240px;
      display: block;
      overflow: auto;
      font-family: Arial, sans-serif;
      font-size: 0.9rem;
      line-height: 20px;
      text-align: center;
      border: 0px !important;
    }

    table.dataframe th {
      text-align: center;
      font-weight: bold;
      padding: 8px;
    }
    
    table.dataframe td {
      text-align: center;
      padding: 8px;
    }
    
    table.dataframe tr:hover {
      background: #b8d1f3; 
    }
    
    .output_prompt {
      overflow: auto;
      font-size: 0.9rem;
      line-height: 1.45;
      border-radius: 0.3rem;
      -webkit-overflow-scrolling: touch;
      padding: 0.8rem;
      margin-top: 0;
      margin-bottom: 15px;
      font: 1rem Consolas, "Liberation Mono", Menlo, Courier, monospace;
      color: $code-text-color;
      border: solid 1px $border-color;
      border-radius: 0.3rem;
      word-break: normal;
      white-space: pre;
    }

  .dataframe tbody tr th:only-of-type {
      vertical-align: middle;
  }

  .dataframe tbody tr th {
      vertical-align: top;
  }

  .dataframe thead th {
      text-align: center !important;
      padding: 8px;
  }

  .page__content p {
      margin: 0 0 0px !important;
  }

  .page__content p > strong {
    font-size: 0.8rem !important;
  }

  </style>
</head>


# 1. 인구 피라미드

* 연령별 인구현황은 구글링해서 데이터 사용하였다.(여기선 2021년도 인구)

* 예전 년도의 인구도 이런식으로 그래프 만들면 서로 비교하기 용이할것이다.(이런식으로 활용)


## 남자 데이터 정의



```python
import pandas as pd
file_name = '202108_202108_연령별인구현황_월간.xlsx'
# 위의 불필요한 3행 무시(skiprows)
# 필요한 컬럼만 가져오기 위해(usecols) => 큰 데이터 활용시 유용할테니 잘 참고
df_m = pd.read_excel(file_name, skiprows=3, index_col='행정기관', usecols='B,E:Y') 
df_m.head(3)
```

<pre>
C:\Users\Nadocoding\anaconda3\lib\site-packages\openpyxl\styles\stylesheet.py:221: UserWarning: Workbook contains no default style, apply openpyxl's default
  warn("Workbook contains no default style, apply openpyxl's default")
</pre>
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0~4세</th>
      <th>5~9세</th>
      <th>10~14세</th>
      <th>15~19세</th>
      <th>20~24세</th>
      <th>25~29세</th>
      <th>30~34세</th>
      <th>35~39세</th>
      <th>40~44세</th>
      <th>45~49세</th>
      <th>...</th>
      <th>55~59세</th>
      <th>60~64세</th>
      <th>65~69세</th>
      <th>70~74세</th>
      <th>75~79세</th>
      <th>80~84세</th>
      <th>85~89세</th>
      <th>90~94세</th>
      <th>95~99세</th>
      <th>100세 이상</th>
    </tr>
    <tr>
      <th>행정기관</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>807,150</td>
      <td>1,154,806</td>
      <td>1,215,483</td>
      <td>1,229,848</td>
      <td>1,652,371</td>
      <td>1,875,014</td>
      <td>1,655,155</td>
      <td>1,829,373</td>
      <td>2,024,616</td>
      <td>2,139,842</td>
      <td>...</td>
      <td>2,075,439</td>
      <td>2,039,854</td>
      <td>1,423,351</td>
      <td>982,527</td>
      <td>691,904</td>
      <td>448,750</td>
      <td>189,426</td>
      <td>47,982</td>
      <td>9,047</td>
      <td>2,230</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>129,766</td>
      <td>176,433</td>
      <td>189,993</td>
      <td>201,088</td>
      <td>292,214</td>
      <td>402,129</td>
      <td>364,322</td>
      <td>358,270</td>
      <td>362,416</td>
      <td>377,526</td>
      <td>...</td>
      <td>348,510</td>
      <td>346,019</td>
      <td>257,783</td>
      <td>183,490</td>
      <td>133,498</td>
      <td>81,360</td>
      <td>31,954</td>
      <td>8,564</td>
      <td>1,819</td>
      <td>623</td>
    </tr>
    <tr>
      <th>부산광역시</th>
      <td>46,491</td>
      <td>68,329</td>
      <td>68,191</td>
      <td>69,493</td>
      <td>102,324</td>
      <td>116,387</td>
      <td>99,345</td>
      <td>110,248</td>
      <td>125,965</td>
      <td>128,387</td>
      <td>...</td>
      <td>132,304</td>
      <td>145,246</td>
      <td>112,633</td>
      <td>79,465</td>
      <td>53,942</td>
      <td>32,142</td>
      <td>11,705</td>
      <td>2,744</td>
      <td>442</td>
      <td>147</td>
    </tr>
  </tbody>
</table>
<p>3 rows × 21 columns</p>
</div>



```python
# 데이터에 , 가있으니까 문자로 인식이 될 수 있기 때문에 int형으로 데이터 변환하겠다.
df_m.iloc[0] = df_m.iloc[0].str.replace(',', '').astype(int) # 1,195,951 -> 1195951 (정수형)
```


```python
df_m.iloc[0] # 첫번째 행(데이터)는 정수형으로 바뀐걸 볼 수 있다. (0번째가 전국이라서 어차피 이 데이터만 사용)
```

<pre>
0~4세        807150
5~9세       1154806
10~14세     1215483
15~19세     1229848
20~24세     1652371
25~29세     1875014
30~34세     1655155
35~39세     1829373
40~44세     2024616
45~49세     2139842
50~54세     2271863
55~59세     2075439
60~64세     2039854
65~69세     1423351
70~74세      982527
75~79세      691904
80~84세      448750
85~89세      189426
90~94세       47982
95~99세        9047
100세 이상       2230
Name: 전국  , dtype: object
</pre>
## 여자 데이터 정의



```python
df_w = pd.read_excel(file_name, skiprows=3, index_col='행정기관', usecols='B,AB:AV')
df_w.head(3)
# 아래 출력을 보면 컬럼명이 .1 이 나오는 이유는 동일한 컬럼이 2개이상 존재하기 때문(남, 여)
```

<pre>
C:\Users\Nadocoding\anaconda3\lib\site-packages\openpyxl\styles\stylesheet.py:221: UserWarning: Workbook contains no default style, apply openpyxl's default
  warn("Workbook contains no default style, apply openpyxl's default")
</pre>
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0~4세.1</th>
      <th>5~9세.1</th>
      <th>10~14세.1</th>
      <th>15~19세.1</th>
      <th>20~24세.1</th>
      <th>25~29세.1</th>
      <th>30~34세.1</th>
      <th>35~39세.1</th>
      <th>40~44세.1</th>
      <th>45~49세.1</th>
      <th>...</th>
      <th>55~59세.1</th>
      <th>60~64세.1</th>
      <th>65~69세.1</th>
      <th>70~74세.1</th>
      <th>75~79세.1</th>
      <th>80~84세.1</th>
      <th>85~89세.1</th>
      <th>90~94세.1</th>
      <th>95~99세.1</th>
      <th>100세 이상.1</th>
    </tr>
    <tr>
      <th>행정기관</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>765,718</td>
      <td>1,099,511</td>
      <td>1,146,252</td>
      <td>1,142,146</td>
      <td>1,521,873</td>
      <td>1,672,118</td>
      <td>1,523,976</td>
      <td>1,744,685</td>
      <td>1,954,674</td>
      <td>2,073,661</td>
      <td>...</td>
      <td>2,020,720</td>
      <td>2,084,814</td>
      <td>1,523,981</td>
      <td>1,119,798</td>
      <td>908,817</td>
      <td>731,836</td>
      <td>429,062</td>
      <td>163,137</td>
      <td>37,655</td>
      <td>8,705</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>122,200</td>
      <td>168,049</td>
      <td>179,786</td>
      <td>192,489</td>
      <td>322,918</td>
      <td>422,032</td>
      <td>365,483</td>
      <td>358,167</td>
      <td>368,511</td>
      <td>384,837</td>
      <td>...</td>
      <td>359,036</td>
      <td>381,456</td>
      <td>292,733</td>
      <td>215,754</td>
      <td>163,571</td>
      <td>113,723</td>
      <td>61,339</td>
      <td>24,590</td>
      <td>6,093</td>
      <td>1,893</td>
    </tr>
    <tr>
      <th>부산광역시</th>
      <td>44,214</td>
      <td>64,756</td>
      <td>64,561</td>
      <td>64,967</td>
      <td>96,558</td>
      <td>105,853</td>
      <td>93,317</td>
      <td>106,076</td>
      <td>122,036</td>
      <td>127,847</td>
      <td>...</td>
      <td>140,679</td>
      <td>161,288</td>
      <td>126,921</td>
      <td>93,874</td>
      <td>68,059</td>
      <td>50,639</td>
      <td>26,473</td>
      <td>9,739</td>
      <td>2,095</td>
      <td>605</td>
    </tr>
  </tbody>
</table>
<p>3 rows × 21 columns</p>
</div>



```python
df_m.columns
```

<pre>
Index(['0~4세', '5~9세', '10~14세', '15~19세', '20~24세', '25~29세', '30~34세',
       '35~39세', '40~44세', '45~49세', '50~54세', '55~59세', '60~64세', '65~69세',
       '70~74세', '75~79세', '80~84세', '85~89세', '90~94세', '95~99세', '100세 이상'],
      dtype='object')
</pre>

```python
df_w.columns # 컬럼이 동일해서 구분을 위해 .1 이 붙었다는것을 알 수 있다.
```

<pre>
Index(['0~4세.1', '5~9세.1', '10~14세.1', '15~19세.1', '20~24세.1', '25~29세.1',
       '30~34세.1', '35~39세.1', '40~44세.1', '45~49세.1', '50~54세.1', '55~59세.1',
       '60~64세.1', '65~69세.1', '70~74세.1', '75~79세.1', '80~84세.1', '85~89세.1',
       '90~94세.1', '95~99세.1', '100세 이상.1'],
      dtype='object')
</pre>

```python
df_w.columns = df_m.columns # 컬럼명 통일(.1 이 사라진다)
df_w.columns
```

<pre>
Index(['0~4세', '5~9세', '10~14세', '15~19세', '20~24세', '25~29세', '30~34세',
       '35~39세', '40~44세', '45~49세', '50~54세', '55~59세', '60~64세', '65~69세',
       '70~74세', '75~79세', '80~84세', '85~89세', '90~94세', '95~99세', '100세 이상'],
      dtype='object')
</pre>

```python
df_w.iloc[0] = df_w.iloc[0].str.replace(',', '').astype(int) # 1,195,951 -> 1195951 (정수형)
df_w
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0~4세</th>
      <th>5~9세</th>
      <th>10~14세</th>
      <th>15~19세</th>
      <th>20~24세</th>
      <th>25~29세</th>
      <th>30~34세</th>
      <th>35~39세</th>
      <th>40~44세</th>
      <th>45~49세</th>
      <th>...</th>
      <th>55~59세</th>
      <th>60~64세</th>
      <th>65~69세</th>
      <th>70~74세</th>
      <th>75~79세</th>
      <th>80~84세</th>
      <th>85~89세</th>
      <th>90~94세</th>
      <th>95~99세</th>
      <th>100세 이상</th>
    </tr>
    <tr>
      <th>행정기관</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>전국</th>
      <td>765718</td>
      <td>1099511</td>
      <td>1146252</td>
      <td>1142146</td>
      <td>1521873</td>
      <td>1672118</td>
      <td>1523976</td>
      <td>1744685</td>
      <td>1954674</td>
      <td>2073661</td>
      <td>...</td>
      <td>2020720</td>
      <td>2084814</td>
      <td>1523981</td>
      <td>1119798</td>
      <td>908817</td>
      <td>731836</td>
      <td>429062</td>
      <td>163137</td>
      <td>37655</td>
      <td>8705</td>
    </tr>
    <tr>
      <th>서울특별시</th>
      <td>122,200</td>
      <td>168,049</td>
      <td>179,786</td>
      <td>192,489</td>
      <td>322,918</td>
      <td>422,032</td>
      <td>365,483</td>
      <td>358,167</td>
      <td>368,511</td>
      <td>384,837</td>
      <td>...</td>
      <td>359,036</td>
      <td>381,456</td>
      <td>292,733</td>
      <td>215,754</td>
      <td>163,571</td>
      <td>113,723</td>
      <td>61,339</td>
      <td>24,590</td>
      <td>6,093</td>
      <td>1,893</td>
    </tr>
    <tr>
      <th>부산광역시</th>
      <td>44,214</td>
      <td>64,756</td>
      <td>64,561</td>
      <td>64,967</td>
      <td>96,558</td>
      <td>105,853</td>
      <td>93,317</td>
      <td>106,076</td>
      <td>122,036</td>
      <td>127,847</td>
      <td>...</td>
      <td>140,679</td>
      <td>161,288</td>
      <td>126,921</td>
      <td>93,874</td>
      <td>68,059</td>
      <td>50,639</td>
      <td>26,473</td>
      <td>9,739</td>
      <td>2,095</td>
      <td>605</td>
    </tr>
    <tr>
      <th>대구광역시</th>
      <td>33,301</td>
      <td>50,145</td>
      <td>51,747</td>
      <td>53,488</td>
      <td>72,843</td>
      <td>73,837</td>
      <td>62,613</td>
      <td>74,250</td>
      <td>89,310</td>
      <td>100,716</td>
      <td>...</td>
      <td>102,229</td>
      <td>101,311</td>
      <td>76,491</td>
      <td>56,373</td>
      <td>42,677</td>
      <td>33,782</td>
      <td>18,595</td>
      <td>6,432</td>
      <td>1,278</td>
      <td>304</td>
    </tr>
    <tr>
      <th>인천광역시</th>
      <td>43,971</td>
      <td>63,283</td>
      <td>66,430</td>
      <td>65,516</td>
      <td>86,994</td>
      <td>101,318</td>
      <td>90,591</td>
      <td>102,585</td>
      <td>114,678</td>
      <td>119,736</td>
      <td>...</td>
      <td>123,367</td>
      <td>118,457</td>
      <td>78,978</td>
      <td>54,143</td>
      <td>42,634</td>
      <td>33,055</td>
      <td>19,798</td>
      <td>7,995</td>
      <td>1,975</td>
      <td>422</td>
    </tr>
    <tr>
      <th>광주광역시</th>
      <td>22,602</td>
      <td>33,687</td>
      <td>36,409</td>
      <td>37,671</td>
      <td>50,079</td>
      <td>49,107</td>
      <td>40,131</td>
      <td>48,846</td>
      <td>57,440</td>
      <td>61,851</td>
      <td>...</td>
      <td>54,453</td>
      <td>52,763</td>
      <td>36,125</td>
      <td>29,771</td>
      <td>22,568</td>
      <td>17,166</td>
      <td>9,583</td>
      <td>3,821</td>
      <td>880</td>
      <td>210</td>
    </tr>
    <tr>
      <th>대전광역시</th>
      <td>21,700</td>
      <td>31,506</td>
      <td>33,996</td>
      <td>35,270</td>
      <td>49,378</td>
      <td>50,621</td>
      <td>42,816</td>
      <td>47,974</td>
      <td>55,890</td>
      <td>60,817</td>
      <td>...</td>
      <td>55,179</td>
      <td>56,850</td>
      <td>40,131</td>
      <td>28,008</td>
      <td>22,029</td>
      <td>16,946</td>
      <td>10,066</td>
      <td>3,790</td>
      <td>832</td>
      <td>174</td>
    </tr>
    <tr>
      <th>울산광역시</th>
      <td>18,529</td>
      <td>27,124</td>
      <td>27,162</td>
      <td>25,248</td>
      <td>29,711</td>
      <td>31,220</td>
      <td>30,500</td>
      <td>38,235</td>
      <td>43,776</td>
      <td>46,490</td>
      <td>...</td>
      <td>47,961</td>
      <td>45,784</td>
      <td>30,400</td>
      <td>19,164</td>
      <td>13,243</td>
      <td>9,925</td>
      <td>5,723</td>
      <td>2,106</td>
      <td>523</td>
      <td>74</td>
    </tr>
    <tr>
      <th>세종특별자치시</th>
      <td>9,941</td>
      <td>13,034</td>
      <td>12,604</td>
      <td>9,535</td>
      <td>8,242</td>
      <td>10,397</td>
      <td>13,568</td>
      <td>17,752</td>
      <td>19,304</td>
      <td>15,760</td>
      <td>...</td>
      <td>9,793</td>
      <td>9,738</td>
      <td>6,743</td>
      <td>4,430</td>
      <td>3,394</td>
      <td>2,871</td>
      <td>2,014</td>
      <td>839</td>
      <td>163</td>
      <td>29</td>
    </tr>
    <tr>
      <th>경기도</th>
      <td>225,076</td>
      <td>319,632</td>
      <td>327,337</td>
      <td>317,509</td>
      <td>407,961</td>
      <td>448,234</td>
      <td>423,626</td>
      <td>494,008</td>
      <td>553,942</td>
      <td>573,577</td>
      <td>...</td>
      <td>514,555</td>
      <td>489,425</td>
      <td>333,357</td>
      <td>231,807</td>
      <td>188,167</td>
      <td>146,519</td>
      <td>85,361</td>
      <td>33,103</td>
      <td>7,982</td>
      <td>1,782</td>
    </tr>
    <tr>
      <th>강원도</th>
      <td>21,125</td>
      <td>28,786</td>
      <td>31,597</td>
      <td>33,185</td>
      <td>40,061</td>
      <td>37,118</td>
      <td>34,911</td>
      <td>42,294</td>
      <td>50,461</td>
      <td>56,727</td>
      <td>...</td>
      <td>63,705</td>
      <td>73,853</td>
      <td>54,100</td>
      <td>36,741</td>
      <td>36,685</td>
      <td>31,075</td>
      <td>17,257</td>
      <td>7,172</td>
      <td>1,748</td>
      <td>357</td>
    </tr>
    <tr>
      <th>충청북도</th>
      <td>24,103</td>
      <td>34,144</td>
      <td>35,652</td>
      <td>35,008</td>
      <td>43,448</td>
      <td>43,605</td>
      <td>40,542</td>
      <td>49,091</td>
      <td>54,562</td>
      <td>59,938</td>
      <td>...</td>
      <td>62,954</td>
      <td>68,454</td>
      <td>49,989</td>
      <td>33,766</td>
      <td>31,763</td>
      <td>27,412</td>
      <td>17,103</td>
      <td>6,020</td>
      <td>1,326</td>
      <td>251</td>
    </tr>
    <tr>
      <th>충청남도</th>
      <td>33,360</td>
      <td>47,677</td>
      <td>49,460</td>
      <td>47,370</td>
      <td>54,019</td>
      <td>53,061</td>
      <td>53,970</td>
      <td>66,688</td>
      <td>74,394</td>
      <td>77,698</td>
      <td>...</td>
      <td>76,346</td>
      <td>83,852</td>
      <td>63,464</td>
      <td>49,776</td>
      <td>43,375</td>
      <td>39,844</td>
      <td>26,778</td>
      <td>9,333</td>
      <td>2,132</td>
      <td>425</td>
    </tr>
    <tr>
      <th>전라북도</th>
      <td>23,371</td>
      <td>36,218</td>
      <td>40,646</td>
      <td>41,997</td>
      <td>50,520</td>
      <td>45,466</td>
      <td>39,634</td>
      <td>49,429</td>
      <td>59,077</td>
      <td>68,134</td>
      <td>...</td>
      <td>69,420</td>
      <td>76,246</td>
      <td>58,091</td>
      <td>50,816</td>
      <td>44,006</td>
      <td>37,765</td>
      <td>24,453</td>
      <td>9,313</td>
      <td>1,906</td>
      <td>445</td>
    </tr>
    <tr>
      <th>전라남도</th>
      <td>25,516</td>
      <td>36,569</td>
      <td>39,337</td>
      <td>39,771</td>
      <td>45,358</td>
      <td>41,231</td>
      <td>37,563</td>
      <td>47,990</td>
      <td>56,070</td>
      <td>62,324</td>
      <td>...</td>
      <td>72,199</td>
      <td>79,487</td>
      <td>60,005</td>
      <td>54,791</td>
      <td>51,607</td>
      <td>48,998</td>
      <td>28,744</td>
      <td>11,057</td>
      <td>2,513</td>
      <td>545</td>
    </tr>
    <tr>
      <th>경상북도</th>
      <td>36,267</td>
      <td>52,093</td>
      <td>53,066</td>
      <td>52,840</td>
      <td>62,146</td>
      <td>59,051</td>
      <td>57,674</td>
      <td>74,002</td>
      <td>85,110</td>
      <td>95,918</td>
      <td>...</td>
      <td>109,065</td>
      <td>119,672</td>
      <td>95,407</td>
      <td>71,283</td>
      <td>62,194</td>
      <td>57,612</td>
      <td>35,736</td>
      <td>12,904</td>
      <td>2,848</td>
      <td>564</td>
    </tr>
    <tr>
      <th>경상남도</th>
      <td>48,955</td>
      <td>75,654</td>
      <td>78,936</td>
      <td>74,285</td>
      <td>82,718</td>
      <td>80,470</td>
      <td>79,474</td>
      <td>104,526</td>
      <td>123,674</td>
      <td>133,392</td>
      <td>...</td>
      <td>134,046</td>
      <td>141,827</td>
      <td>103,474</td>
      <td>75,815</td>
      <td>61,496</td>
      <td>54,734</td>
      <td>33,409</td>
      <td>12,262</td>
      <td>2,516</td>
      <td>427</td>
    </tr>
    <tr>
      <th>제주특별자치도</th>
      <td>11,487</td>
      <td>17,154</td>
      <td>17,526</td>
      <td>15,997</td>
      <td>18,919</td>
      <td>19,497</td>
      <td>17,563</td>
      <td>22,772</td>
      <td>26,439</td>
      <td>27,899</td>
      <td>...</td>
      <td>25,733</td>
      <td>24,351</td>
      <td>17,572</td>
      <td>13,486</td>
      <td>11,349</td>
      <td>9,770</td>
      <td>6,630</td>
      <td>2,661</td>
      <td>845</td>
      <td>198</td>
    </tr>
  </tbody>
</table>
<p>18 rows × 21 columns</p>
</div>


## 데이터 시각화



```python
import matplotlib.pyplot as plt
import matplotlib
matplotlib.rcParams['font.family'] = 'Malgun Gothic' # Windows
# matplotlib.rcParams['font.family'] = 'AppleGothic' # Mac
matplotlib.rcParams['font.size'] = 15 # 글자 크기
matplotlib.rcParams['axes.unicode_minus'] = False # 한글 폰트 사용 시, 마이너스 글자가 깨지는 현상을 해결
```


```python
plt.figure(figsize=(10, 7))
plt.barh(df_m.columns, -df_m.iloc[0] // 1000) # 단위 : 천 명 (-는 음수로해서 왼쪽으로 그리기 위해)
plt.barh(df_w.columns, df_w.iloc[0] // 1000)
plt.title('2021 대한민국 인구 피라미드')
plt.savefig('2021_인구피라미드.png', dpi=100)
plt.show()
```

<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAo4AAAG1CAYAAAB3W3UTAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAABV8UlEQVR4nO3de5zdVX3v/9c7QEhJoBPIqLQQgliKB6hSxoJUaIBgUajFCzRJFSKc4mn1WFuRplgkiFjwlGJFLkY4RLGBthSQEo0KFQ9Q5GfkgcChp4gwFKTEiZIL4RKM798f6zuw2cxlz8yevWfPvJ+Px/cxM2ut73et72xCPllX2SYiIiIiYjjT2t2AiIiIiOgMCRwjIiIioiEJHCMiIiKiIQkcIyIiIqIhCRwjYsqR9FZJpw+St52kFZL2b0I9O0taImnWWJ813iS9Q9Ib292OiJjYEjhGREerArN1NT/3SvrIMLcdApw8SN42wEnArw5S336SPMR1bU3xucCVwJxBnvXeYZ5Vex00zDsNSNKbJK2StEHSs5L+P0l/MEDRTwLHDfKMWSNo53WjaecI3md+Vc+og/Hqv5n1TWrPirrPPGJS27bdDYiI8SdpGvA/gf8O/BqwCfgX4GO2f1pX9rXAhcB84BfAKuAjtmuDsy7gLOBdwK7Aj4ErgL+2vXWQNvwBcJ7tPUfY9jXAgQNk/bPt9zT4jOOA2kDjN4CdJL23rujXgGcabNoCynvX+kyD9/a7EXj9MGVmA/8GPDvCZyPpd6s6rgbeDWwGjgK+JGlv2+c0+KjNDbQT4K+BEe3xJmkZ5b+l4XzH9vwhnjMPeGSI+99ve0UD7ekCnhqqjG0N95ya530W+NMGiv6H7X0afW5EuyRwjJgaPgp8DPgscA8lCPg48AZJv9Uf7El6NXAH8ENKr9ts4Fzga5LeXBMU/j2we5X3BHAksAzopu4vyWr481PAMcDLgtQRuBj4fF3axhHcfyrwK3VpPwFOq0u7G3i4wWf+yHZvbYKkjcCMRhtleyPDvIekV1XfjihwlLQN8EVgue3/WZN1p6T/AP5e0jW2f9hAOw38vwbq3MQI3r/yOeArDZQb7v1/zODB7f83ohYV7wXuqktbAFw6imd9DzhxmDLPj+K5ES2XwDFiangceIPt/6p+/pqk/wt8HXg7pfcR4K+A54Dftf0sgKQHgdspw5j/XJX7OiUg2VL9fJOk54GPSlpm+6nq3j8D/hb4EfBV4C2jbP8628MGLoOx/fb+76shztcBM4FHbD9RW1bSSAOf8bZd9fW5Ed73m5Tg/nP1Gbb/oeoJO4byj4lmmQa8MJIbbP8M+Jmk3wN2tL2yP0/SwcCBti9u4DkvMEhwK+kXI2lT5ce2H6p7zn6jeA7AM2P57zdiIskcx4gpwPbVNUFjv28BW4B9ASQJWAhc0R80VvfeATxAzfw325+vCRr73UT5x+iv16Q9CfxJVcc9zXiX0ZK0vaS/AfqAb1J6uR6VdIukRoZh26U/kB3pUHV39bV+OL3f4zVlmmU7Rt9z9jZe2St3GPCBMbUoIpoqPY4RU9c0ykKQ/qHSvSmLOG4boOydwHCLM/p7xl4cerV9df/3JS5tq7+lBL+H2/4ugKQ5lCHwW6o5f0/XlN+pyv+57fXNbIikmUCjv5Bdq6/bVL2lW2sD+yH096TuTV3QXg1j71VTplm2pwTmo7EFmF6XNr1Kj4gJIoFjxNT1u5TAsX8e12urr70DlP1PyuKKoRwD/IwyLN1qO0j6VPV91yBlFgEf7w8aAWyvk3QysAE4grKQpN8/VF//A2j2ooUNlN/9SDxZff0OZeHScH4APAicJendtmuHa/+cMlR/44B3jt5M4OlhSw3seV76x0e/hgNHSXOBewfJ/uUB0raR1D914jnbaxpqZcQUl8AxYgqqetI+B9xs+/tVclf1daDFGpt4+ark+ucdAnwIOMd2Oyb5b0OZtwivDD76PcvAQeVO1f31q6nfB9zK4HP2zq4Wg9R6E3DfMG0F2IPBexzvpaxOvnqQ/IZ+v7Yt6STKsPxtklZS3vEo4A+AD9p+bKhnSHoNgwfiA3k1sL2k/kD7R9Xcw0ZsYQyBI6UH/ZeBPwIeGiC/fo7hLF7qXX8UmNdgPaOxQ83vZCgP2f75OLYjYswSOEZMMZL2osxH/AVl5Wi//v8fDLSdzi+qa6DnvQ24BvgGJeBph022F1bt6R2kzIXAJyU9RVmosxk4APgb4PuUILHWOtuPD/CcDZRFQjOrq9Z9lGH9IdkebN5h/0KOpwape0Rsf1fSgcBSymr36ZTAdIHtbzfwiE8Bp4yw2v2Bj1Tf78nAPdgDGShw3I6RD1WvsX1PA+U22O4a4bNH603AvzdQbnfK3NOICSuBY8QUUgV5f0/pffl927Xz0TZXX2fyyl7HWfVp1WKapZTg4ovAh+qGQycU238jaS3wF5R5jdtQtuS5CvhUoz09VS9dQ/tHTgTVdjsjDf767/3vlL0/X6FaaHSw7dGulK/3PAPPcRzRKu2JxvZHeCmQjuh4CRwjpghJ7wcuBy4BPjrAquj+YcvdgfoV2LtTs79hFTReDiwGTmlkY+UxmidpfvX9tsAOlD0mbx/JQ2xfBVwlaTtguu3Nw90zGEl7AEfZvnwMz1hB2S+z3hclfbEu7dfqt4dpsI5XU4ZvL7K9YeStbJlhh6pbvFXSMZJeV5f2xhbWHzEhJXCMmAKqOYhfBE6zfeEgxe6n9Pq8mVdumHwwLx/K/Rhl654F1XY94+kpyl6Tv0sJIp6jLMD4GUOfFPIKVcD4T8AnbA+2kKJRB1B+p6MOHIG/BM4bpsyvMbZFLLsC51C2H5pQgaOkHXjpHyQ7UOYCPllTZCdgmqQ+YEdKT/FNwzz2v0nalvL323aUld47AjtT/gF0wTD3/5wydeHw6qr3/QHSIqaMBI4RU8NfAd8aImjE9rOSvgb8kaSL+4duJb0ZeAOl16o/+DoD+GQLgkZsHzVUfnVEYqO2AX6fV55CU1vfczS+Vc6QbN8jabvBhsGrvTXre3dfpgqCWuFARnhcYBM8zyBD4TVMWZz1JGVPyjcNU/7vKfN0f075h8YzlGkYT1XP2GnIysqWTD3DNXwokn6JsgBqtNbVHvEZMZEkcIyYGt4MXF8z3Fvr6ZqtSD5BOR7tXyRdQtkg+lPACtvfq8r8N8rq1WcHed7joxlSnaw6ZZXsYGeMt6DO4XoQX2aw/UCr4x8bCvhbsKfogQy8H2qjzqYc4Rkx4SRwjJgauoD3V1e9H1DN3bJ9v6SjKCuN/xFYRxmKPafuWQB/N0hdf8fEXwywewPbozxXfxb1QBrcZuUp22sbatn42auBOYK/sP1gS1ozidm+nSb1WkdMNAkcI6YA2w3/JVb9pXfwEPnfYRR/KdpexsTpRfnfDZR5MaAeRiPbrFxM2eeynW5uoMxmhtivMyIigWNEdLRqRfeKmqTNDLL3X5PnL97QrGcNo5dyjvOojges9jQcj3b+hLJxdqttpXzGY5mP+QKjP+Gm3nOM/BSgiI4lu9VzoSMiIiKiE01rdwMiIiIiojMkcIyIiIiIhmSOYwvMmTPH8+bNa3czIiIiIob1/e9/f53t7oHyEji2wLx581izZs3wBSMiIiLaTNKgC98yVB0RERERDUngGBERERENSeAYEREREQ1J4BgRERERDUngGBERERENSeAYEREREQ1J4BgRERERDUngGBERERENSeAYEREREQ1J4BgRERERDUngGBERERENSeAYEREREQ1J4BgRERERDUngGBERERENSeAYEREREQ1J4BgRERERDemowFHSUkkrmvi8JZJWj/Lev5I0s1ltiYgYrXlLV7W7CRExRUyowFHSPElPD5BuSXNG8bw/kfSwpGck3SnpTSO8f4mkawfJPgfYcaRtioiIiOhUDQeOkl4r6V8lHTdA3rGS7pP0nKR7JR1Rl7+3pFuqAO4xSX/ahLYP194/BD4KHEcJ8C4BviHpNSN4zDbVFRERETHlDRs4Spor6QvAD4BDBsg/ELgK+BgwG7gUuFHS7lX+TOBm4FvAHGAxsEzSu5r1EoM4C/iI7Xttb7V9FXADcNoInvHrwP7j0biIiIiITtNIj+NvUXrsDgGeHCD/dOAy26ttP2v7UuC7wMlV/onAj22fZ/sZ27cBFwD/c4BndQM7SNq+urokdY3wnah6FX8FqJ/48w/AYQ0+Y0fgeOAnkhYNUuxxST+XdNBI2xgRERHRabYdroDta4FrASQNVGQB8M66tJuBw2vyvz5A/sclybZr0vcCBMwDjgI+NVz7BrE7JVj9RV36fwJzh7tZ0jbAldX1JeAWST+xfUtd0d1sDxRMR0REREw6Y1ocU/UG7gw8XJf1KLBb9f1eg+TPoAxd1/p9YB3we7Y/b7vLdtcomrYZGGjF8yzgFYtvalWLcP6RMrfxU7YfpcyT/IKk8yT9UiMNkHSqpDWS1vT19Y2o8RERERET0VhXVc+qvj5Tl74Z2L6mzED51JRB0s7A24D/Abxf0lja1gu8SlJ3XfobeWUQW+9/AxuBE/p7LG3fD7yZEtQ+30gDbC+33WO7p7u7vhkRERERnWesgeML1dfpdekzeClYfGGQfHh5QPlXwArb/0wJ3D4w2kbZfgb4Z+DP+tMkTQc+TAkMh/Ju2++3/UJtou0+239TM/x9MS8FwBERERGT3rBzHIfR3wO3Oy9fODOXl3r2Hq/yqcvfYPtnANX2Pe8G3lDlfxD4lqS7bN89yrb9FXB71ev4A2AR8ARlGHpQ/QGjyoTORcBJwH+j9JxuW73Pt4DzbG8aZdsiIiIiOs6YehxtbwXupCxkqbUA6F9IcvtQ+ZJmU7bzeZ/t9dVz7wb+krIie7Rt+xHwG8D/A/ak7OP49gEWzAzmgqoNFwB7254N/DLwDkov6hpJu4y2fRERERGdZqw9jgAXAl+SdAdwF6WHbj/KVjYAlwMflfQnlGHiHuDPgd8DsP2UpH37g8Z+tpePtWG2+yiB32i8E/gL29+sed4vgB9S3mcxcBDwtbG2MyIiIqITjPnIQds3Ah+n9BquBxYCb+0fxrX9Y+BY4FRgA7Ac+CPbd9U8Y/1Y2zEOvgr8laQjJc2AMnwtaS9Jn6nK3DX47RERERGTy4h6HG3PGyT9EspQ8GD3/R/KiuZO8ufAf6ecQLOvpO0oe0z+mLIP5Zts/7SN7YuIAKD3vGPa3YSImCKaMVQ97mwLQNKlwHZNfPS1vHJz8v46f0HpHR3zkHlERETEZNARgWM/2xua/LynGWZD8IiIiIgoOipwjIiIl5u3dBUAvTMWvzxjWVP/nR0RATRhccxUJWm1pCXtbkdEREREqyRwHISkJZJuqkubL+n+drUpIiIiop1aEjhK2l7ShZJ+IulZSV+TtHuVt50k112jmnc4VD01ZU6V9JCk5yXdI+mwZrxjRERExGTXqh7HC4EDgYOB11IWpHxV0jSg//SV7W2rumaNQz1IOoGyvc4S4NXASmCVpN1GWV9ERETElDHui2MkdQEfoBzb93CVdgrwGHAo5bzrTba3jHM93wGWAp+wfXt122ckHQ2cApxd98huYOfqOTMp2wCNNqCNiIiI6Hit6HF8HfDz6uxoAKpTZe6lHNm3CyV4HO96APYGHqi77/aa/Fp7Uc64BrgC6AWuaUI7IyIiIjpSKwLHJ4DpkvasS59D6dXbBZgnaYukJyRdLWnuONTTX2afIfIBqIa2jwW2ldRje6HtriqtIdVcyjWS1vT19Y3gNSIiIiImpnEPHG0/AawCvijptZK6JH0a2B14AVgNzKYMAy8AZgDf7D8fuon1QDkFZpmkQyTNlHQ8cEJNfr+jgbXARZRh7BGzvdx2j+2e7u7u4W+IiIiImOBatTjmfcDjwF3AjyhzK+8E1tp+1vYG21tsPwAsovQCHt5/c7UNzkOSNkq6TtICSbMkrZK0qJF6qvwLKcHjSqAPWAxcWZPf39t4NnAO5fzthZL2b/LvIyIiIqLjtOTkGNtPUVYyAyBJwMPA3QOUfU7SI8AeNcnnA6cD36YEexdQ5jTeAlzfaD22twLnVld/mRV17TiTEtDeUOWfBazMtj0REREx1bXryMG3AQLuqM+QtBNlEcvD/Wm2axevXFxdY6qnqmsO8C6gp/r5UErgeXBNsYuq/CNpziKeiIiIiI7UksBR0lsoQ8dPAUdQhov/xPYvqqHmJ4HvAbsBnwV+SOlNbFo9Vf6+wM+BR4DXA5cCy20/CGD7NkkH2F7f/0zbBk6s7p8/0jZFRERETBat6nE8AvgXysKXHwCn2v5aTRtWAK+hrHq+CVhYDSs3sx6AecAXKKuoHwUuowSqL6oNGiMiIiLiJSodatGIqsfx87b3k7QauMb2iuHu6+np8Zo1a8a5dRERERFjJ+n7tnsGymvXHMeOZPtWYL/qx5OAze1rTURERERrJXAcJdtrhy8VERERMXkkcIyI6CDzlq4aML13xuLmVLBsQ3OeExGTUqs2AJ90JK2WtKTd7YiIiIholQSOg5C0RNJNdWnzJd3frjZFREREtFNLAkdJ20u6UNJPJD0r6WuSdq/JP1bSfZKek3SvpCPGo566sjMkPVadHBMRERERw2hVj+OFwIGUE1leCzwNfFXSNEkHAlcBHwNmUzblvnGwgG+09QxQ9kPArqOoIyIiImJKGvfAUVIX8AHg/bYftv1fwCmUwO5QyhnUl9lebftZ25cC3wVObnI9tWX3AD4IfGWIR3YDO1flZ1bPnzWSNkVERERMJq3ocXwd8HPbP+pPsL0JuBc4CFgAfL3unpuBQ5pcDwCSBFwOnMXQZ0/vBexZfX8F0Atc02hjJJ0qaY2kNX19fY3eFhERETFhtSJwfAKYLmnPuvQ5wK9RevUerst7lHJudbPq6a75+Qxgk+0vD/agamj7WGBbST22F9ruqtIaYnu57R7bPd3d3cPfEBERETHBjXvgaPsJYBXwRUmvldQl6dPA7sA2VbFn6m7bDGzfxHpeAJB0JLCE4YfBjwbWAhdRhrsjIiIiprxWLY55H/A4cBfwI8rG43dSehYBpteVn0FNMFltg/OQpI2SrpO0QNIsSaskLWqgnrWS9qEswlloe/1gDa16G88GzgEuARZK2n+U7x0RERExabTk5BjbT1F6+oAX5xk+DJwLPE/pFXyy5pa5vHz4+nzKIppvA4uBCyhzGm8Brm+gnrur+7uBW0oyUALUaZKOq4aiAc4E1tq+oXrGWcBKSYeN7u0jIiIiJod2HTn4NkDAbZQewaOA79XkL6AMOwNg+6CavIurayT13AH8O/DJuvwzKHMsTwOQdCgl8Dy4psxFQA9wJEMvpomIiIiY1FoSOEp6C2Xo+CngCGA58Ce2fyHpQuBLku6gDDGfBOwHHN/MeihB37q68huB6bZ7AWzfJumA2qFs2wZOrMrPH2mbIiIiIiaLVvU4HgH8C2Vo+AfAqba/BmD7Rkkfp8w/fBWlB/Kt1VY6TaunUUPNf4yIaLfe844ZJGdDS9sREVNTq+Y4fpJXDhPX5l9CWYgyrvUMUP60sdYZERERMVW0a45jR7J9K2UYHcqQ+ub2tSYiIiKitRI4jpLtte1uQ0R0nnlLVw1faBR6Zywel+e2zLIMtUd0glbt4zjpSFotaUm72xERERHRKgkcByFpiaSb6tLmS7q/XW2KiIiIaKeWBI6SZkj6O0lrJW2SdKuknipvO0muu55udj01ZU6tTqF5XtI92dg7IiIiojGt6nH8NHBYde0KrAa+IWlHYJeqzPa2VV2zxqEeJJ0AnEXZ5PvVwEpglaTdRllfRERExJTRqsDxQOBK2/9h+2nKEYKzgL0pgeMm21vGuR6ApcAnbN9ue73tz1BOrDllgGd1U06VQdJMSV3VsyIiIiKmpFYFjn8PvF/SPpJmAR8H7gPupQSOzTrKb6h6oASQD9TdcztwEK+0F7Bn9f0VQC9wTZPaGREREdFxWhU4fhF4mHJe9CbgY8BC2y9QAsd5krZIekLS1ZLmjkM9AE8A+9TdM4fSu/giSdOAY4FtJfXYXmi7q0prSDWXco2kNX19faN6mYiIiIiJpFWB47nArwL7UoZ/zwdulrQzZR7ibMow8ALKcYHflDSjyfVAObt6maRDquHn44ETgBfqnnM0sBa4iIGHsYdle7ntHts93d3dw98QERERMcGNe+BYBW0fBU60/YDtp2x/GngQ+B+2n7W9wfYW2w8Aiyi9gIfXPGN+tRJ6o6TrJC2QNEvSKkmLGqmnetSFlOBxJdAHLAaupASJ/XVNA84GzqEcg7hQ0v7j9xuKiIiI6Ayt6HF8HYDtB+vSf0DpGXwZ288BjwB71CSfD5xepd0CXEAJ9rYC1zdaj+2tts+1Pc/2DrbfSRmmvrum/JnAWts32F5HWYW9UtLsEb11RERExCTTiiMHHwGmS3qd7Ydq0t9AWdH8MpJ2oixiebg/zXbt4pWLq2tM9VR1zQHeBfTvKXkoZaueg2uKXVTlH0nzFvFEREREdJxx73G03QdcBayQ9OuSuiQtBd4MfFHSIkmHV0PP+wD/CPyQ0rPYtHoAJO1b5U2X9AbgRmB5fy+l7duAA2rPoXZxou1rx/q7iIiIiOhkrVoc8wHgDuAbwKPAW4H5tnspvZ4rgJ8CX6cEjQtsb21yPQDzKAHpJuCfgGspQ+Avsr1+FPVGRERETHqy3e42dAxJ84HP295P0mrgGtsrhruvp6fHa9asGefWRURERIydpO/b7hkorxVzHCcN27cC+1U/ngRsbl9rIiIiIlorgeMo1c6DjIiIiJgKEjhGxJQ1b+mqdjehaXpnLG53EyaHZRva3YKICa1Vi2MmHUmrJS1pdzsiIiIiWiWB4yAkLZF0U13afEn3t6tNEREREe3UksBR0gxJfydpraRNkm6V1FOTf6yk+yQ9J+leSUeMRz0DlH1M0opRvlZERETElNKqHsdPA4dV167AauAbknaUdCBl4+6PAbOBS4EbJe3ezHoGKPuhqkxERERENKBVgeOBwJW2/8P205Szp2dRjhY8HbjM9mrbz9q+FPgucHKT63mRpD2ADwJfGeJZ3cDOVfmZkrqqZ0VERERMSa0KHP8eeL+kfSTNAj4O3AfcCyygnBhT62bgkCbXA4AkAZcDZzH02dN7AXtW318B9ALXjKJNEREREZNCq7bj+SLwu8C/Vz9vpPQOzqT06j1cV/5RYLdm1WP7hZoyZwCbbH9Z0t8M9BBJ04BjgW0l9dheWKXPBz7fSEMknQqcCjB37tyRv0lERETEBNOqHsdzgV8F9qUEiudTehV3qvKfqSu/Gdi+WfVI6h9yPhJYwvDD4EcDa4GLgFNG0Q5sL7fdY7unu7t7NI+IiIiImFDGPXCsgraPAifafsD2U7Y/DTwIvL8qNr3uthnUBJPVNjgPSdoo6TpJCyTNkrRK0qIG6vkfkvahLMJZaHv9EO2dBpwNnANcAiyUtP/YfxMRERERna0VQ9WvA7D9YF36D4B9gOeB3YEna/Lm8vLh6/Mpi2i+DSwGLqieewtwfQP17FvldwO3lGmOQAlQp0k6znZXlXYmsNb2DQCSzgJWSjpsBO8cERERMem0InB8BJgu6XW2H6pJfwPwPeBO4Kjq+34LgBfPArN9UE3exdU10nouBD5Zd88ZlCHt0wAkHUoZyj64psxFQA9wJEMvpomIiIiY1MY9cLTdJ+kqYIWkUyhzB/8H8GbK4pG7gC9JuqP6/iRgP+D4ZtZjex11gZ+kjcB0273VM26TdEDtULZtAydW5eeP6OUjIiIiJpFWrar+ALAM+AZlk+/vA/OrgK1X0scp8w9fRemBfKvtTU2upyFDzX+MiMml97xj2t2EJtrQ7gZExBTQksDR9rPAX1TXQPmXUBaijGs9A5Q/bax1RkREREwVrepxnBRs30oZRocypL65fa2JiIiIaK0EjqNke2272xARERHRSgkcR0nSauAa2yva3ZaIyWTe0lXDF4pX6J2xuN1NiIEsy9zTmFxadXJMx5G0RNJNdWnzJd3frjZFREREtFMrTo5ZIsmDXEslbTdA+tPNrqem3KnVKTTPS7onG3tHRERENGbcA0fbK2yr9qKc4rIJ+DKwS1V0+5oys8ahHiSdAJxF2eT71cBKYJWk3cb6nhERERGTXbuGqs8Gvmj7CUrguMn2lnGuB2Ap8Anbt9teb/szlFNlThng3m7KqTJImimpCxhxQBsRERExWbR8cYykvYF3AHtVSbswDkf5DVAPwN7AA3VFbwcO4pX2Avasvr8COJry++ptakMjIiIiOkQ7ehw/Blxtu6/6eRdgnqQtkp6QdLWkueNQD8ATwD515eZQehdfJGkacCywraQe2wttd1VpDanmUq6RtKavr2/4GyIiIiImuJYGjpJ2Bv4Q+HxN8mrK8YCzgAXADOCbkmY0uR6A5cAySYdUw8/HAycAL9SVO5py1vVFDDyMPSzby2332O7p7u4e/oaIiIiICa7VPY4nAnfbvq8/wfaztjfY3mL7AWARpRfw8P4y1TY4D0naKOk6SQskzZK0StKiRuqpXEgJHlcCfcBi4EpKkNhf1zTK3MhzKMcgLpS0fxPePSIiIqKjtTpw/EPgn4cqYPs54BFgj5rk84HTq7RbgAsowd5W4PpG67G91fa5tufZ3sH2OynD1HfXFDsTWGv7BtvrKKuwV0qa3eA7RkRERExKLVscU2150wO8Z5hyO1EWsTzcn2a7dvHKxdU1pnqqsnOAd1XlkXQoZaueg2uKXVTlH8k4LOKJiIiI6BStXFV9FPCY7UdrE6uh5icp2+LsBnwW+CGlZ7Fp9VR17Qv8nNKj+XrgUmC57QcBbN8m6QDb6/vvsW3K0DeS5o+yTREREREdr5VD1b/Fy4eE+20LrAB+CnydEjQusL21yfUAzKMEpJuAfwKupQyBv6g2aIyIiIiIl7Ssx9H2Hw+SfhVw1XjXU+WtovRqRsQE1XveMe1uQofa0O4GRMQU0PINwDuZ7VuB/aofTwI2t681EREREa2VwHGUbK8dvlRERETE5JHAMSJeNG/pqnY3IUapd8bidjch2mFZpihEa7XjyMFJQdJqSUva3Y6IiIiIVkngOAhJSyTdVJc2X9L97WpTRERERDuNe+BYBWAe5FpalTlW0n2SnpN0r6QjxqOeuvIzJD0maUUTXjMiIiJi0hv3wNH2CtuqvYDXUfZS/LKkAynb8XwMmE3ZlPtGSbs3s54BbvkQsOsYXi0iIiJiSmnXUPXZwBdtP0HZgPsy26ttP2v7UuC7wMlNrudFkvYAPgh8ZYh7u4Gdq/IzJXUBs5rQpoiIiIiO1PJV1ZL2Bt4B7FUlLQDeWVfsZuDwJtfTny7gcuAs4DeAOYM8Yi9gz+r7K4CjKb+v3rG0KyIiIqJTtaPH8WPA1bb7ql68nYGH68o8ythPeHmxnrr0M4BNtgcavgZA0jTgWGBbST22F9ruqtIaIulUSWskrenrq29CREREROdpaY+jpJ2BPwQOqpL6h36fqSu6Gdi+ifX0px8JLAHeNMwjjgbWAl8FTgHWjLQNtpcDywF6eno80vsjIiIiJppW9zieCNxt+77q5xeqr9Prys2gJpistsF5SNJGSddJWiBplqRVkhY1UA+S9qEswlloe/1gDax6G88GzgEuARZK2n9krxkREREx+bQ6cPxD4J9rfl4HPA/Ur6Cey8uHr8+nLKLZA7gFuIDSI7gVuL6Beqju7wZukbRe0nrKyurF1ff9zgTW2r7B9jrKXMiVkmY3+I4RERERk1LLhqol7Qb0AO/pT7O9VdKdwFHA92qKLwBW1ZSrHXK+uLoarqdyOvDJurQzKHMsT6vuPZQylH1wTZmLqucdSQl0IyIiIqakVs5xPAp4zPajdekXAl+SdAdwF3ASsB9wfDPrqXoPXxb4SdoITLfdW5W5TdIBtUPZtk0Z+kbS/FG2KSIiIqLjtTJw/C3g7vpE2zdK+jhl/uGrgDuBt9re1Mx6GjXU/MeIiIiIqaxlgaPtPx4i7xLKQpRxrWeAsqc1o86IyaL3vGPa3YQYtQ3tbkBETAEt3wC8k9m+lTKMDmVIfXP7WhMRERHRWgkcR8n22na3ISIiIqKVEjhGNMG8pauGLxQxjnpnLG53EyIasyzTKjpZO44cnDQkrZa0pN3tiIiIiGiFBI5DkLRE0k11afMl3d+uNkVERES0S0sDR0k7SvqcpP+S9Lykf5e0XXW57nq62fXU5J9aHWH4vKR7JB3WnDeMiIiImLxaeXLMNsDXgP8E3gw8CbwB+AXlKECA7W1vGcd6kHQC5RjBPwDuB04FVkl6ve3Hx1J3RERExGTWyh7HJcBM4H22e20/Z/su21uBXYBNYw0aG6gHYCnwCdu3215v+zOU4w5PGeBZ3ZQjCZE0U1IXMKsJbYyIiIjoOK1cVf1+4HO2fzFA3i407xzooeoB2Bt4oC7tduCgAcruBexZfX8FcDTld9Y79mZGREREdJaW9DhK2hboAZ6VdKekZyTdK+kdVZFdgHmStkh6QtLVkuaOQz0ATwD71N06h5eGy/ufNQ04FthWUo/thba7qrRG2nKqpDWS1vT19Y30VSIiIiImnFYNVe8CbA98GPhz4NXA3wLXSnoDsBqYTRkGXgDMAL4paUaT6wFYDiyTdEg1/Hw8cALwQt2zjgbWAhcx8DD2kGwvt91ju6e7u3v4GyIiIiImuFYFjv3DxhfYvtP2JtsrgFXAibaftb3B9hbbDwCLKL2Ah/c/oNoG5yFJGyVdJ2mBpFmSVkla1Eg9Vd6FlOBxJdAHLAaupASJ/XVNA84GzqGcob1Q0v5N/p1EREREdJRWBY7rgOeBR+vSH6T0Cr6M7eeAR4A9apLPB06v0m4BLqAEe1uB6xutx/ZW2+fanmd7B9vvpAxT311T/kxgre0bbK+jrMJeKWn2iN46IiIiYhJpyeIY25Z0F3AI8P2arH2Bu+rLS9qJsojl4Zpn1C5eubi6xlRPVdcc4F2UuZFIOpSyMvvgmmIXVflH0rxFPBEREREdpZWrqv8WuELSA5Ttb95LCfBOroaan6zSdwM+C/yQ0rPYtHoAJO0L/JzSo/l64FJgue0HAWzfJukA2+v7H2jbVEPdkuaPok0RERERHa9lgaPtr1Yrpa+kDBt/Hzja9k+q1dArgNdQVj3fBCys2XuxKfVUReYBX6AMTz8KXEYJVGufsX6k9cbU1nveMe1uQkx5G9rdgIiYAlrZ44jtiyjDvvXpVwFXjXc9Vd4qSq9mRERERIxASwPHycD2rcB+1Y8nAZvb15qIiIiI1kngOAa21w5fKiIiImJySOA4BpJWA9dUe0VOGfOWrmp3EyKiTu+Mxe1uQkQMZ1nnz0Vu1T6OHUnSEkk31aXNl3R/u9oUERER0S4tDRwl7Sjpc5L+S9Lzkv5d0nZV3rGS7pP0XHW+9BHjUU9duRmSHpO0YgyvFRERETEltGyoWtI2wNeA/wTeTNm38Q3ALyQdSFlVvQj4DmUD7hslvd72Y82qZ4DiHwJ2Hc37REREREw1rexxXALMBN5nu9f2c7bvqvZqPB24zPbq6tzqS4HvUm3a3cR6XiRpD+CDwFeGeFY3sHNVfqakLmDWKNoUERER0fFaGTi+H/ic7YF6/hYAX69Lu5ly4ksz6wFAkoDLKWdQD3WE4F7AntX3VwC9wDWjaFNEREREx2tJ4FidDNMDPCvpTknPVPMY31H14u1MzbnUlUcZ4UbdQ9VTV/QMYJPtLw/xrGnAscC2knpsL7TdVaU10pZTJa2RtKavr28krxERERExIbWqx3EXYHvgw8CfU44C/FvgWsr8Q4Bn6u7ZXN3TlHokvQFA0pGU4ezhhsGPBtZSTqA5ZYTtwPZy2z22e7q7u0d6e0RERMSE06rAsX/Y+ALbd9reVO19uIoSxAFMr7tnBjXBZLUNzkOSNkq6TtICSbMkrZK0qIF6TpS0D2URzsKhzqOuehvPBs4BLgEWStp/dK8eERERMTm0KnBcBzxPGX6u9SAlQHwe2L0uby4vH74+n7KIZg/gFuACSo/gVuD6Bup5dXV/N3CLpPWS1lNWVi+uvu93JrDW9g2211HmQq6UNLvxV46IiIiYXFqyHY9tS7qLstjl+zVZ+wJ3Aa8BjgK+V5O3gNJT2P+Mg2ryLq6ukdZzKfDJutvOoMyxPA1A0qGUXtCDa8pcRJk7eSRDL6aJiIiImLRaeeTg3wJXSHqAEiC+lxLgnQz8APiSpDsoAd5JwH7A8c2sp+o9fFngJ2kjMN12L4Dt2yQdUDuUbdvAiVX5+aNoU0RERETHa1ngaPurkuYCV1KGjb8PHG37J5TNvj9OmX/4KuBO4K22NzW5nkafsX6k9UZERERMdiqdadGoqsfx87b3k7QauKZagDOonp4er1mzpgWti4iIiBgbSd+33TNQXiuHqicF27dShtGhDKlvbl9rIiIiIlongeMY2F7b7jZEREREtEoCxxjWvKWrhi8UEW3VO2Nxu5sQEQDLNrS7BeOqlWdVTyqSVkta0u52RERERLRKAsdBSFoi6aa6tPmS7m9XmyIiIiLaqSWBo6S3SnLddVOVt90AeU83u56aMqdWRxc+L+keSYc14x0jIiIiJrtWzXHcGfg32789QN4u1dftbW8Zx3qQdALl+MA/AO4HTgVWSXq97cfHWHdERETEpNaqoepdgJ8OkbepCUHjcPUALAU+Yft22+ttf4ZyuswpA5TtpgSiSJopqQuY1YQ2RkRERHSkVvU47sLgZzwPldfMegD2Bh6oS7sdOGiAsnsBe1bfXwEcTfl99Y6tiRERERGdqZU9ju+TtKWaX/jXknaoyZtX5T0h6erqyMBm1wPwBLBP3T1zKL2LL5I0DTgW2FZSj+2FtruqtIZUcynXSFrT19c3qpeJiIiImEhaFTieCXQBsynDwu8ALq3yVlfps4AFwAzgm5JmNLkegOXAMkmHVMPPxwMnAC/UPedoYC1wEQMPYw/L9nLbPbZ7uru7h78hIiIiYoJrSeBoe6PtzdX1HWAJpWdwpu1nbW+wvcX2A8AiSi/g4f33V9vgPCRpo6TrJC2QNEvSKkmLGqmnKnIhJXhcCfQBi4ErKUFif13TgLOBc4BLgIWS9h+v301EREREp2jXPo4PAAJ2r8+w/RzwCLBHTfL5wOlV2i3ABZRgbytwfaP12N5q+1zb82zvYPudlGHqu2vuORNYa/sG2+soq7BXSpo9qjeNiIiImCTadeTgmyjDw6/YAkfSTpRFLA/3p9muXbxycXWNqZ6qrjnAu4Ce6udDKb2UB9cUu6jKP5LmLeKJiIiI6Dit2gD8o5L2k7SDpN8BLgcutv20pEWSDq+GnvcB/hH4IaVnsWn1VPn7Svp1SdMlvQG4EVhu+0EA27cBB9h+cejaxYm2rx3r7yEiIiKik7Wqx3Eu8G3KApgfUXrxPl/ThhXAayirnm8CFtre2uR6AOYBX6AMTz8KXAZ8tvYBttePot6IiIiISU+2292GjiFpPvB52/tJWg1cY3vFcPf19PR4zZo149y6iIiIiLGT9H3bPQPltWuOY0eyfSuwX/XjScDm9rUmIiIiorUSOI5S7TzIiIiIiKkggeMkMm/pqnY3ISLapHfG4nY3ISJaYdmGtlbfrn0cO56k1ZKWtLsdEREREa2SwHEQkpZIuqkubb6k+9vVpoiIiIh2atU+jm+V5Lrrppr8YyXdJ+k5SfdKOmI86qkrO0PSY5JWjPK1IiIiIqaUVs1x3Bn4N9u/XZ8h6UDgKsoZ1f3nS98o6fW2H2tWPQP4ELDrCJ8fERERMWW1aqh6F+Cng+SdDlxme7XtZ21fCnwXOLnJ9bxI0h7AB4GvDFGsmxKIImmmpC7KxuIRERERU1IrA8fBznleAHy9Lu1m4JAm1wOAJFGOIjxrmLJ7AXtW318B9ALXjKJNEREREZNCKwPH90naIukhSX9dnSfdRenVe7iu/KPAbs2qp67MGcAm218e7CGSpgHHAttK6rG90HZXldYQSadKWiNpTV9f3yheJSIiImJiaVXgeCbQBcwGTgHeAVzKS0O/z9SV3wxs38R6AJB0JGUO5XDD4EcDaylnXZ8yinZge7ntHts93d3do3lERERExITSksDR9kbbm6urfwHM+4BtqiLT626ZQU0wWW2D85CkjZKuk7RA0ixJqyQtGq6eao7iPpRFOAttrx+srVVv49nAOcAlwEJJ+4/xVxARERHR8dq1j+MDgCg9js8Du9flz+Xlw9fnUxbR7AHcAlxA6RHcClzfQD27V/d3A7dIWi9pPWVl9eLq+35nAmtt32B7HWUu5EpJs0f+mhERERGTR7uOHHwT8AJlLuOdwFHA92ryFwAvnp9n+6CavIurayT1PE4JHD9Zl38GZY7laQCSDqX0Uh5cU+YioAc4kmEW3kRERERMZi0JHCV9FPgGpRfxTZRVzRfbflrShcCXJN0B3AWcBOwHHN/MeoCnqQv8JG0EptvuBbB9m6QDaoeybRs4sSo/f6RtioiIiJgsWtXjOBf4NmVo+keUXrzPA9i+UdLHKfMPX0XpgXyr7U3NrKdRQ81/nOh6zzum3U2IiLbZ0O4GRMQU0JLA0fafAn86RP4llIUo41rPAOVPG2udEREREVNFu+Y4diTbt1KG0aEMqW9uX2siIiIiWiuB4yjZXtvuNkRERES0UgLHUZK0GrjG9op2t2Ws5i1dNXyhiJjQemcsbncTImIoyybHPOR27eM44UlaIummurT5ku5vV5siIiIi2qnlgaOk/SVtlbSk+nk7Sa67nm52PTXpp1an0Dwv6R5Jh421roiIiIipoB09jp8GXPPzLtXX7W2rumYNcN9Y60HSCZSTYJYArwZWAqsk7daE+iIiIiImtZYGjpLeQ9lj8Z6a5F2ATba3jHM9AEuBT9i+3fZ625+hnFhzygCP6aacKkN11nVX9cyIiIiIKallgaOkbuBvgA/UZe1CE4/yG6IegL0p51fXuh04aICyewF7Vt9fAfQC1zSnlRERERGdpyWBoyRRToa50PaDddm7APMkbZH0hKSrJc0dh3oAngD2qUubQ+ldrH3ONOBYYFtJPbYX2u6q0hpty6mS1kha09fXN5LXiIiIiJiQWtXjeBbwjO2/GyBvNTCbMgy8AJgBfFPSjCbXA7AcWCbpkGr4+XjgBOCFunJHA2spRxYONIw9LNvLbffY7unu7h7+hoiIiIgJbtwDR0kLgUXA+wfKt/2s7Q22t9h+oCo7Bzi85hnzq5XQGyVdJ2mBpFmSVkla1Eg9lQspweNKoA9YDFxJCRL765oGnA2cQzkGcaGk/Uf7/hERERGTRSt6HD8NzAUelbRe0nrgjcAl9fskAth+DngE2KMm+Xzg9CrtFuACSrC3Fbi+0Xpsb7V9ru15tnew/U7KMPXdNXWdCay1fYPtdZRezJWSZo/5NxERERHRwVpxcsxhA9RzI/Bl4Cv1hSXtRFnE8nB/mu3axSsXV9eY6qnqmgO8C+ipfj6UslXPwTXFLqryj6SJi3giIiIiOs24B462H69Pk7QFWGf7yWqo+UnKtji7AZ8FfkjpWWxaPdXP+wI/p/Rovh64FFjev5DG9m2SDrC9vua5Bk6s7p8/kjZFRERETCYT4cjBbYEVwE+Br1OCxgW2t45DXfMoAekm4J+AaylD4C+qDRojIiIi4iWtGKp+Bds9Nd9fRdlCZ1zrqX5eRenVjBq95x3T7iZExJhtaHcDImIKaEvg2Kls3wrsV/14ErC5fa2JiIiIaK0EjqNke+3wpSIiIiImjwSOMSrzlq5qdxMiokbvjMXtbkLE1LFs6k4NmQiLYzqSpNWSlrS7HRERERGtksBxEJKW1G9QXp1gc3+72hQRERHRTi0PHCXtL2lrbW+dpGMl3SfpOUn3SjpiPOqpy58h6TFJK8ZaV0RERMRU0I4ex08D7v9B0oGU7Xg+BsymbMp9o6Tdm1nPAD4E7DrGOiIiIiKmjJYGjpLeA8wC7qlJPh24zPZq28/avhT4LnByk+upzd8D+CCDHEVY6QZ2rsrPlNRVPTMiIiJiSmpZ4CipG/gb4AN1WQsoJ8bUuhk4pMn19OcLuBw4i6HPnt4L2LP6/gqgF7hmNG2KiIiImAxaEjhWwdpVwIX950JX6V2UXr2H6255lFGc8DJYPXXOADbZ/vIQz5kGHAtsK6nH9kLbXVVao205VdIaSWv6+voaf4mIiIiICapVPY5nAc/Y/ru69P6h32fq0jcD2zexHgAkHQksYfhh8KOBtcBFwCmjaAe2l9vusd3T3d09mkdERERETCjjHjhKWggsAt4/QPYL1dfpdekzqAkmq21wHpK0UdJ1khZImiVplaRFDdSDpH0ovZELba8for3TgLOBc4BLgIWS9m/gVSMiIiImtVacHPNpyurlR8tIMlB6Gi8BvgM8D+wOPFlzz1xePnx9PmURzbeBxcAFwOuAW4Drh6unWizzE8qCl1tq8mcA0yQdVw1FA5wJrLV9A4Cks4CVkg4b7S8gIiIiYjJoReB42AD13Ah8mbKq+WrgKOB7NfkLgBfPtLN9UE3exdU10np+DnyyLv8MyhzL0wAkHUoZyj64psxFQA9wJEMvpomIiIiY1MY9cLT9eH2apC3AOttPSroQ+JKkO4C7gJOA/YDjm1lPlbSuLn8jMN12b/WM2yQdUDuUbdvAiVX5+SNpU0RERMRk0ooexyHZvlHSxynzD18F3Am81famNrVnfTvq7TS95x3T7iZExMtsaHcDImIKaEvgaLun7udLKHMex7WeAfJPa3adEREREZNV23scO4ntWynD6FCG1De3rzURERERrZXAcZRsr213GyIiIiJaKYHjGEhaDVxje0W72zJZzFu6avhCEfEKvTMWt7sJERPHssz5HS8tO6u6E0laIummurT5ku5vV5siIiIi2qVVZ1W/TdL3JD0t6QlJn5G0bU3+dpJcdz09HnVVZU6tTqJ5XtI92dw7IiIiYnit6nF8FfBnwKuBtwPHAR+vyd+l+rq9bVXXLEZnyLoknUA503pJVWYlsErSbqOsLyIiImJKaEngaPtLtm+3vdn2PcCllNNh+u0CbLK9pQV1LQU+UZVZb/szlFNrThngcd2Uk2WQNFNSF+UYw4iIiIgpp12LY3YEflzz8y6M33F+9XXtDTxQV+Z24CBeaS9gz+r7K4CjKb+z3uY2MSIiImLia+niGEldko6j7IH46ZqsXYB5krZU8xKvljR3nOp6AtinrvgcSu9i7f3TgGOBbSX12F5ou6tKa6T+UyWtkbSmr69vlG8RERERMXG0LHCUtB54CvgKcCFQuzJ5NTCbMgy8AJgBfFPSjHGoazmwTNIh1fDz8cAJwAt1jzkaWAtcxMDD2EOyvdx2j+2e7u7u4W+IiIiImOBaFjhWvXU7AUdRArV/qMl71vYG21tsPwAsovQCHt5fptoG5yFJGyVdJ2mBpFmSVkla1GhdlEByOWVRTB+wGLiSEiT21zUNOBs4h3IU4kJJ+zfnNxERERHRmVo6VG17k+07gfcC7xlsJbPt54BHgD1qks8HTq/SbgEuoAR7W4HrG63L9lbb59qeZ3sH2++kDFPfXXP7mcBa2zfYXkdZhb1S0uyxvH9EREREJ2vX4phfVF89UKaknSiLWB7uT7Ndu3jl4upqRl1zgHcBPdXPh1K26jm4pthFVf6RjN8inoiIiIgJrVUbgH9W0uslzaiGfK8CbrL94yp/kaTDq6HnfYB/BH5I6Vlsdl37Svp1SdMlvQG4EVhu+0EA27cBB9SeRe3iRNvXju03EREREdG5WjVU3UUJAp8CrgVuBf6gJn9bYAXwU+DrlKBxge2t41DXvCp/E/BPVZnTax9ge/0o6o2IiIiY1FoyVG17yTD5V1F6BltR1yogp8RMUL3nHdPuJkR0qA3tbkBETAHtmuPYsWzfCuxX/XgSsLl9rYmIiIhonQSOY1A7DzIiIiJiskvgGJPavKWr2t2EiJbonbG43U2IaMyyTKvoZC3dx3GykbRa0pJ2tyMiIiKiFRI4DkHSEkk31aXNl3T/YPdERERETFat2sfxbZK+J+lpSU9I+oykbevKHCvpPknPSbpX0hHjVVdN2RmSHpO0YjR1RUREREwlrepxfBXwZ8CrgbcDxwEf78+UdCBlO56PAbOBS4EbJe3e7LrqfAjYdRR1REREREw5LQkcbX/J9u22N9u+hxIYLqgpcjpwme3Vtp+1fSnwXeDkcagLAEl7AB8EvjLE47qBnavyMyV1AbNG2qaIiIiIyaBdcxx3BH5c8/MCyokxtW4GDhmHupAk4HLgLIY+e3ovYM/q+yuAXuCaJrQpIiIiouO0NHCU1CXpOMrG2Z/uT6P06j1cV/xRxnDCy0B11TgD2GT7y0PcPw04FthWUo/thba7qrRG6j9V0hpJa/r6+kb1DhERERETScv2cZS0HvhlykkrS4H+lcn9Q7/P1N2yGdi+yXUh6UhgCfCmYR5zNLAW+CpwCrBmJG2wvRxYDtDT0+OR3BsRERExEbWsx7HqrdsJOAo4AfiHKuuF6uv0ultmUBNMVtvgPCRpo6TrJC2QNEvSKkmLGqlL0j6URTgLba8frK1Vb+PZwDnAJcBCSfuP+KUjIiIiJpGWnhxjexNwp6T3Ao9K2g34L+B5YHfgyZric3n58PX5lEU03wYWAxcArwNuAa5vsK7TKQtebinTHIESoE6TdFwVcAKcCay1fQOApLOAlZIOG9MvICIiIqKDtevIwV9UX217q6Q7Kb2D36spswB48bw42wfV5F1cXSOqixI4frIu/wzKHMvTACQdShnKPrimzEVAD3AkQy+miYiIiJi0WhI4Svos8AXgEeDXgM8BN9nuX+18IfAlSXcAd1EWtOwHHD8Oda2rK78RmG67F8D2bZIOqB3Ktm3gxKr8/JG2KSIiImIyaFWPYxdlSHk28J/ASuB/9WfavlHSxynzD18F3Am8tRpubmpdjRhq/mNERETEVNWSwNH2kgbKXEJZiDLuddWVP22sdcbE1XveMe1uQkSLbGh3AyJiCmjXHMeOZftWyjA6lCH1ze1rTURERETrJHAcA9tr292GiIiIiFZJ4BhtM2/pquELRURDemcsbncTIlpnWaZmtEu7zqqeFCStlrSk3e2IiIiIaIUEjkOQtETSTXVp8yXdP9g9EREREZNVywJHSb8p6VuSNktaK+lySV1V3naSXHc93ex6asqcWh1f+Lyke3IiTERERMTwWtnjeAZwBWWfxrcArwcuq/J2qb5ub1vVNWsc6kHSCcBZlNNhXk3Z53FVdSRhRERERAyilYtjltju70X8oaSPATdL2oYSOG6yvWU867G9FVgKfML27VWZz0g6GjgFOLvuWd2U4wiRNBPYDhhtQBsRERHR0VoWONYEc/2eoQRiUALHppwBPUw9AHsDD9SVuR04iFfaC9iz+v4K4GjK76x3zA2NiIiI6DDtXByzCLi96gXcBZgnaYukJyRdLWnuONQD8ASwT12ZOZTexRdJmgYcC2wrqcf2QttdVdqwqnmUaySt6evrG9MLREREREwEbQkcJZ0M/DHwkSppNeVs6VnAAmAG8E1JM5pcD8ByYJmkQyTNlHQ8cALwQt3tRwNrgYsow9gjYnu57R7bPd3d3cPfEBERETHBtTRwlDRD0iXAJ4EjbP8AwPaztjfY3mL7AUov4Rzg8Jp751croTdKuk7SAkmzJK2StKiReioXUoLHlUAfsBi4khIk9t8/jTLf8RzK+dkLJe3f7N9HRERERCdp5XY8s4FbgdcCb7S9ZrCytp8DHgH2qEk+Hzi9SrsFuIAS7G0Frm+0HttbbZ9re57tHWy/kzJMfXdNsTOBtbZvsL2Osgp7ZfXsiIiIiCmplauqVwAPA++1/YuhCkraibKI5eH+NNu1i1curq4x1VPVNQd4F9BT/XwoZaueg2uKXVTlH0mTFvFEREREdJqWBI6SuoF3AHsPFMxVQ81PAt8DdgM+C/yQ0rPYtHqqMvsCP6f0aL4euBRYbvtBANu3STrA9vr+e2wbOLG6f/5I2hQRERExWbSqx3HX6uuDkurz3lm1YwXwGsqq55uAhTUroZtSj+0bgHnAFyjD049SNgf/bG3B2qAxxk/vece0uwkRk8iGdjcgIqaAlgSOtu8FXhHJ1bmqFfXYXkXp1YyIiIiIEWjlHMdJwfatwH7VjycBm9vXmoiIiIjWSeA4BrbXDl8qIiIiYnJI4DgGklYD19he0e62xMDmLV3V7iZEtETvjMXtbkJEY5ZlPm4na+eRgxOepCWSbqpLmy/p/na1KSIiIqJdWrkB+G9K+pakzZLWSrpcUldN/rGS7pP0nKR7JR0xHvXUlZ0h6TFJK0b1UhERERFTSCt7HM8ArgBeBbyFsofiZQCSDqSsqv4Y5czqS4EbJe3ezHoG8CFe2sInIiIiIobQyjmOS2w/XX3/Q0kfA26WtA3lKMHLbK+u8i+V9G7gZMqZ0U2pp3ZfSEl7AB8EvjLEs7qBnavyM4HtgFkjbE9ERETEpNCyHseaYK7fM5RADGAB8PW6/JuBQ5pcDwAqu4NfTjmDeqgjBPcC9qy+vwLoBa4ZaZsiIiIiJoN2Lo5ZBNwO7Ejp1Xu4Lv9RmrNR9yLg9rpTaM4ANtn+8mA3SZoGHAtsK6nH9kLbXVXasCSdKmmNpDV9fX1jaH5ERETExNCW7XgknQz8MXAoLw39PlNXbDOwfRPr6U87ElgCvGmY248G1gJfBU4B1oykbtvLgeUAPT09Hsm9ERERERNRS3scq1XMlwCfBI6w/QPghSp7el3xGdQEk9U2OA9J2ijpOkkLJM2StErSogbqQdI+lEU4C4c6j7rqbTwbOAe4BFgoaf8xvHpEREREx2tZj6Ok2ZR5jOuBN9run1u4Dnge2B14suaWubx8+Pp8yiKabwOLgQuA1wG3ANc3UA/V/d3ALWWaI1AC1GmSjquGogHOBNbavqF65lnASkmHjerlIyIiIiaBVg5Vr6AEgu+1/Yv+RNtbJd0JHAV8r6b8AmBVTbmDavIurq6G66mcTumFrHUGZY7laQCSDqUMZR9cU+YioAc4kqEX00RERERMWi0JHCV1A+8A9h4gmAO4EPiSpDuAu4CTgP2A45tZT9X7uK7uno3AdNu9VZnbJB1QO5Rt28CJVfn5I2lTRERExGTRqjmO/ZtsPyjJdddxtm8EPk6Zf7geWAi81famZtbT6EOGmv8YERERMVW1pMfR9r2AhilzCWUhyrjWM8A9p42lzpjYes87pt1NiGiRDe1uQERMAW3ZjqeT2b6VMowOZUh9c/taExEREdE6CRzHwPbadrchIiIiolUSOEZMYPOWrhq+UATQO2Nxu5sQ7bQsUxWiNdp55GBHk7Ra0pJ2tyMiIiKiVRI4DkLSEkk31aXNl3R/u9oUERER0U6tPnLwtZL+tXZrHEnbDbB1ztPNrqcm79Tq6MLnJd2T02AiIiIiGtOSwFHSXElfAH4AHFKXvUv1dXvbqq5Z41APkk4AzqKcDPNqYCWwStJuo6kvIiIiYippVY/jbwE7UoK5J+vydgE22d4yzvUALAU+Yft22+ttf4ZyzOEpA5TtphxFiKSZkrqAUQW0EREREZNBqzYAvxa4FkB6xf7cu9Ck85+HqQdgb+CBurTbgYMGKLsXsGf1/RXA0ZTfV28TmhoRERHRcSbC4phdgHmStkh6QtLVkuaOU11PAPvUpc2h9C6+SNI04FhgW0k9thfa7qrSGlLNpVwjaU1fX98Ymx0RERHRfhMhcFwNzKYMAy8AZgDflDRjHOpaDiyTdEg1/Hw8cALwQl25o4G1wEUMPIw9LNvLbffY7unu7h7+hoiIiIgJru2Bo+1nbW+wvcX2A8AiSi/g4f1lqm1wHpK0UdJ1khZImiVplaRFI6juQkrwuBLoAxYDV1KCxP66pgFnA+dQzs5eKGn/sb5nRERERKdre+BYz/ZzwCPAHjXJ5wOnV2m3ABdQgr2twPUjePZW2+fanmd7B9vvpAxT311T7Exgre0bbK+jrMJeKWn2WN4rIiIiotNNuCMHJe1EWcTycH+a7drFKxdXVzPqmgO8C+ipfj6UslXPwTXFLqryj6RJi3giIiIiOlHbA8dqqPlJyrY4uwGfBX5I6Vlsdl37Aj+n9Gi+HrgUWG77QQDbt0k6wPb6/ntsGzixun9+s9sUERER0SkmwlD1tsAK4KfA1ylB4wLbW8ehrnmUgHQT8E+UrXtOry1QGzRGRERExEta3uNoe17dz1cBV413PVXaKkqvZkRH6D3vmHY3ITrGhnY3ICKmgLYPVXcS27cC+1U/ngRsbl9rIiIiIlorgeMo2V47fKmIiIiIySOBY0QHmLd0VbubEBNc74zF7W5CtNuyTFeI8TcRFsd0JEmrJS1pdzsiIiIiWiWB4yAkLZF0U13afEn3t6tNEREREe3U0sBR0msl/auk4+rSj5V0n6TnJN0r6YjxqKeuzAxJj0laMZa6IiIiIqaKlgSOkuZK+gLwA+CQurwDKdvxfAyYTdmU+0ZJuzezngF8CNh1pHVERERETFWt6nH8LWBHSjD3ZF3e6cBltlfbftb2pcB3gZObXM+LJO0BfBD4yhDP6gZ2rsrPlNQFzBpFmyIiIiImhZasqrZ9LeWUFiTVZy8A3lmXdjNweJProUoXcDlwFvAbwJxBHrcXsGf1/RXA0ZTfV+9I2xURERExGbR1cUzVi7cz8HBd1qOM3wkvZwCbbH95iHZNA44FtpXUY3uh7a4qrSGSTpW0RtKavr6+MTc6IiIiot3avaq6f+j3mbr0zcD2za5M0pHAEoYfBj8aWAtcBJwymrpsL7fdY7unu7t7NI+IiIiImFDaHTi+UH2dXpc+g5pgstoG5yFJGyVdJ2mBpFmSVkla1EhFkvahLMJZaHv9EOWmAWcD5wCXAAsl7d/4K0VERERMTu0OHNcBzwP1K6jn8vLh6/Mpi2j2AG4BLqD0CG4Frm+wrtMpC15ukbRe0nrKyurF1ff9zgTW2r7B9jrKXMiVkmaP4L0iIiIiJp22Hjloe6ukO4GjgO/VZC0AVtWUO6gm7+LqGqnTgU/WpZ1BmWN5GoCkQylD2QfXlLkI6AGOpAS6EREREVPSRDir+kLgS5LuAO4CTgL2A45vZiVV7+HLAj9JG4HptnurMrdJOqB2KNu2gROr8vOb2aaIiIiITtL2wNH2jZI+Tpl/+CrgTuCttje1qT3r21FvxFB6zzum3U2ICW9DuxsQEVNAywNH2/MGSLuEshBlXOsZoMxpzawzIiIiYjJre49jJ7F9K2UYHcqQ+ub2tSYiIiKitRI4jpLtte1uQ0REREQrJXAcBUkHA9c0MhweMZ7mLV01fKGYEnpnLG53E6JVlmU+a7RPu/dxnLAk9UrqqUu7VdJ72tWmiIiIiHbqiMBR0lslue66aRTP2V3S9ZKelvQzSRdLqj+1JiIiIiIG0BGBI2WT7n+zrZrr2JE8QNK2wE3AY5QTaH4HmA98ptmNjYiIiJiMOiVw3AX46Rif8Xbgl4E/s/1T2/cBfwz8kaRfGqD8HGBnSdtI6pLUReaERkRExBTWSYHjWI/72xv4D9tba9LuBLYH9q8tKOk1wExgzyqvt7pqjyKMiIiImFI6KXB8n6Qtkh6S9NeSdhjhM54A9pZU+847A9sA3XVlf58SqP6e7Xtsd9nuAm5vtDJJp0paI2lNX1/fCJsaERERMfF0SuB4JtAFzAZOAd4BXDrCZ/wLpXfxM5J2kbQH8EXgeeCFurInAx8GDpG022gabHu57R7bPd3d9XFpREREROfpiMDR9kbbm6vrO8ASSg/kTABJH5b0U0lPSvq8pP0lvVrSE5J+pXrGJuAo4ADKsPN3gH+oqnhxM29J7wCmA9cAy4ELWvOWERERERNbRwSOA3gAELB7FTx+ihIQvhnYCKwCHgQut/1E/022/6/tI23vWG3efQewFfh/AJJ2BT4P/LFtA+cCvyXpj1r2ZhERERETVKeuEn4TZXj5cdubgZ1q8s6orkb8CXCd7eern68CLrb9XSi9lJLeDVwg6crmND0iIiKiM3VE4Cjpo8A3gIcpQePllADv6RE+53eB71J6K99XXW+uKfIe2+tr77F9N3B4df8o3yAiIiKi83VE4AjMBb4NzAJ+BFxEGVIeqY8AhwE/B24F5tvu7c+sDxojIiIi4iUdETja/lPgT5vwnLc1oTkRE0bvece0uwkxYWxodwMiYgroiMBxorA9H0DS9sBb2tuaiIiIiNZK4DgK1WKax9vdjoiIiIhWSuAYEeNm3tJV7W7ClNE7Y3G7mzA5LcsUgIhanbqPY9tJOlhSb7vbEREREdEqCRyHIKlXUk9d2q2S3tOuNkVERES0S0cFjpKWSnpM0rOSvilp3hif9yFJHutzIiIiIqaCjgkcJX0YOAl4K7Ar8GPgqxrlrtySZgF/0bwWRkRERExuHRE4SpoG/CXwEdv/Xm3U/UFgT8qG3qNxHnDNMGXmADtL2kZSl6QusqAoIiIipqiOCByBfYGdgX/tT7D9DPBvwCEjfZik36EcI/jXQ5R5DTCTEpzuD/RW18EjrS8iIiJiMuiUwHEv4DHbL9SlPwrsNpIHSeoGrgSWAFuGKPr7wDrg92zfY7vLdhdwe4P1nCppjaQ1fX19I2liRERExITUKYHjLOCZAdI3A9s3+hBJ2wIrgb+z/b1hip8MfBg4RNKIglMA28tt99ju6e7uHuntERERERNOpwSOLwDTB0ifQRVQSvqwpJ9KelLS5yXtL+nVkp6Q9CtV+c8DP7P9d0NVJukdVX3XAMuBC5r2JhEREREdqlMCx8eBX60WydSaCzwsaSbwKeAA4M3ARmAV8CBwue0nJM0FPgC8XdJ6Set56djAeyUtBZC0KyXA/GPbBs4FfkvSH43rG0ZERERMcJ2yQvhuYBvKwpR/A5D0S8BvA39pezOwU035M6qr1hOUhS61dgD+L/B24P4q7SrgYtvfBbC9SdK7gQskXdm0N4qIiIjoMB0RONp+VtJlwEWSjgd+CvwtcJvt+xp8xs8pq6JfVO3lCPB4tcUPwHtqvu+/927KKmxGuW1kRERERMfrlKFqKPs43gl8H/hPStD7vmZXUh80RkRERETRET2OALafBz5UXc165tNAuhAjxknvece0uwlTyIZ2NyAipoCOCRwnCtvzASRtD7ylva2JiIiIaJ0EjqNU9YA+PmzBiIiIiEmik+Y4RkREREQbJXCMiIiIiIYkcIyIiIiIhiRwjIiIiIiGJHCMiIiIiIYkcIyIiIiIhiRwjIiIiIiGJHCMiIiIiIYkcIyIiIiIhiRwjIiIiIiGJHCMiIiIiIYkcIyIiIiIhiRwjIiIiIiGJHCMiIiIiIbIdrvbMOlJ6gMebXc7xtEcYF27GxFjks+w8+Uz7Gz5/DrfZPoM97DdPVBGAscYM0lrbPe0ux0xevkMO18+w86Wz6/zTZXPMEPVEREREdGQBI4RERER0ZAEjtEMy9vdgBizfIadL59hZ8vn1/mmxGeYOY4RERER0ZD0OEZEREREQxI4RkRERERDEjhGREREREMSOMbLSNpL0nWSNkj6maR/kvSrdWUOlnSXpOck/VDSwrr811TPeFrSTySdK2laTf52kv5Xlfe0pH+UtHOr3nEqkPQqSf8g6SMD5OXzm0QkLZX0mKRnJX1T0rx2t2mqk/RaSf8q6bi69GMl3Vf92btX0hF1+XtLukXSM9Vn+qd1+TtKukLSU5LWS7pM0owWvNKUIek3JX1L0mZJayVdLqmrJn/Kf4YJHKPeR4FvAXOB/YFtgX/uz5S0O/A14GKgC/gYcKWkN1X504B/AR4HfgU4EvhD4CM1dZwPHAwcCLwW2BFYMW5vNIVI2lnS/wJ+CLx9gPx8fpOIpA8DJwFvBXYFfgx8VZLa2rApStJcSV8AfgAcUpd3IHAV5c/cbOBS4MbqzySSZgI3U/7/OwdYDCyT9K6ax3wJ2AnYG3gj8CbgM+P4SlPRGcAVwKuAtwCvBy6DfIYvsp0r14sXMKvu518BDOxa/Xw+cHVdmcuBK6vv3wY8BmxTk/9e4JHq+y7geeDXa/LnAluBee1+/06/KAHdjZT/4d0KfKQuP5/fJLko//D/L+B3a9J2ADYCv9Pu9k3FC3gPsJLyj+5e4LiavH8A/rqu/M3AWdX3fwzcWZf/V8C3q+9fDzwDdNXkv6VK+6V2v/tkuQb4O/CQ6ne8TT7DcqXHMV7G9tN1Sc9UX6dXXxcAX68rczMv/et6AfAt21vr8udJ2hU4FPix7f+oqfM/KT1kbx77G0xttr9r+x22bx+kSD6/yWNfYGfgX/sTbD8D/Bt1vV3RGravtb3Y9n0DZDfyZ2+g/IOrHuQFwHdtr6/Jv5PyD/s3jrHpURnk78Dtqu/zGZKh6hjeIuBh4D+rn/eqfq71KLDbYPm2n6T0Uu02yP31z4jxk89v8tgLeMz2C3Xp+SwmmGqO3M6M8M9elT+DMuw50J/NrZRpJfm8x88i4HbKlJx8hpT5axEDkvQ2ytyL97jqUwdm8VIvZL/NwPZD5FOlbT9Efu0zYvzk85s8hvosdmpxW2Jos6qvI/2zt7n6mj97bSDpZMrw86HkM3xRehynMEnrJLnmemOVPk3SJ4CvAMfb/kbNbS/w0rB1vxm89IdhoPzaMsPlR4MG+/yGkc9v8shn0Tn6e4VH+mevf7Vt/uy1kKQZki4BPgkcYfsH5DN8UQLHKcz2HNuque6RtD3wVeD3gB7bq+tuexzYvS5tLi91v78iX9Ic4JeARwa5v/4Z0YCBPr8GbsvnN3k8Dvxq7VZJlXwWE886ynSPEf3Zq/I32P7ZQPnVvLndyOfdNJJmUxYWvhZ4o+01VVY+w0oCx6j3GcpcjsNsPzJA/u3AUXVpC4BbavIX1G0HsgC42/ZTwB3APpJenM+hsk/k64D/05xXiCHk85s87qas9Dy4P0HSLwG/zUufZ0wA1Ty2Oxn+z95w+b8taYea/IOA54CBFuPE6KygBHFvt72uPzGfYY12L+vONXEuQMAm4KghyvwmpUv9XZQ5Gb8P/BTYo8rfgfKvqnOBmZRtKR4G3l3zjOuA1cBrgFdT9hX8XLvff7JdDLwdTz6/SXQBfwt8n9I78suU/edubHe7chleuR3PO4CngN+hDE1+oPqztmOV/6uUrZT+pMp/C/AT4KAqX8Aa4ErKHoLzqp//vN3vOlkuoJuywvnXBsnPZ2gncMz10kWZUO9Bro/UlHs38CCl234N8Nt1z9mP0jP1XBV0/FFdfhdlr7OnKd3/FwLT2/3+k+0aKHDM5ze5Lkrw//nqL7MNlM2Ff7nd7cr1ysCxSvsTyg4VzwHfBv5bXf5hwD3Vn80HgN+vy+/fwP9Z4AnKHoFq97tOlgv4jSH+Djwun2G5VL1IRERERMSQMscxIiIiIhqSwDEiIiIiGpLAMSIiIiIaksAxIiIiIhqSwDEiIiIiGpLAMSIiIiIaksAxIiIiIhqSwDEiIiIiGpLAMSIiIiIa8v8DP5xorKW1g2kAAAAASUVORK5CYII="/>

# 2. 출생아 수 및 합계출산율

* 출생아수 합계출산율 검색해서 인터넷에서 데이터 가져와서 사용



```python
import pandas as pd
# 위의 2개 행 데이터는 필요없어서 skiprows
# nrows=2를 통해 2개 행 데이터 가져오고 자동으로 첫번째 행은 컬럼명으로 사용하게 됨(아래 데이터 3줄인거 보면 이해 될것이다)
# index_col은 굳이 컬럼명 말고, 0번째 인덱스를 사용하고싶으면 0으로 적어도 됨.
df = pd.read_excel('stat_142801.xls', skiprows=2, nrows=2, index_col=0)
df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
      <th>2017</th>
      <th>2018</th>
      <th>2019</th>
      <th>2020</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>출생아 수</th>
      <td>471.300</td>
      <td>484.600</td>
      <td>436.500</td>
      <td>435.400</td>
      <td>438.400</td>
      <td>406.200</td>
      <td>357.800</td>
      <td>326.800</td>
      <td>302.700</td>
      <td>272.30</td>
    </tr>
    <tr>
      <th>합계 출산율</th>
      <td>1.244</td>
      <td>1.297</td>
      <td>1.187</td>
      <td>1.205</td>
      <td>1.239</td>
      <td>1.172</td>
      <td>1.052</td>
      <td>0.977</td>
      <td>0.918</td>
      <td>0.84</td>
    </tr>
  </tbody>
</table>
</div>



```python
df.index
# => df.loc['출생아 수'] 이렇게 한다면 에러가 떠서 키값 알아보기 위해 df.index 해봄
# 사실은 이렇게 알아보면서 rename할필요 없이 df.iloc를 사용해도 되긴하다
```

<pre>
Index(['출생아 수', '합계 출산율'], dtype='object')
</pre>

```python
df.index.values
```

<pre>
array(['출생아\xa0수', '합계\xa0출산율'], dtype=object)
</pre>

```python
df.rename(index={'출생아\xa0수':'출생아 수', '합계\xa0출산율':'합계 출산율'}, inplace=True)
df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
      <th>2017</th>
      <th>2018</th>
      <th>2019</th>
      <th>2020</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>출생아 수</th>
      <td>471.300</td>
      <td>484.600</td>
      <td>436.500</td>
      <td>435.400</td>
      <td>438.400</td>
      <td>406.200</td>
      <td>357.800</td>
      <td>326.800</td>
      <td>302.700</td>
      <td>272.30</td>
    </tr>
    <tr>
      <th>합계 출산율</th>
      <td>1.244</td>
      <td>1.297</td>
      <td>1.187</td>
      <td>1.205</td>
      <td>1.239</td>
      <td>1.172</td>
      <td>1.052</td>
      <td>0.977</td>
      <td>0.918</td>
      <td>0.84</td>
    </tr>
  </tbody>
</table>
</div>



```python
df.index.values
```

<pre>
array(['출생아 수', '합계 출산율'], dtype=object)
</pre>

```python
df.loc['출생아 수']
```

<pre>
2011    471.3
2012    484.6
2013    436.5
2014    435.4
2015    438.4
2016    406.2
2017    357.8
2018    326.8
2019    302.7
2020    272.3
Name: 출생아 수, dtype: float64
</pre>

```python
df.iloc[0]
```

<pre>
2011    471.3
2012    484.6
2013    436.5
2014    435.4
2015    438.4
2016    406.2
2017    357.8
2018    326.8
2019    302.7
2020    272.3
Name: 출생아 수, dtype: float64
</pre>

```python
df.iloc[1]
```

<pre>
2011    1.244
2012    1.297
2013    1.187
2014    1.205
2015    1.239
2016    1.172
2017    1.052
2018    0.977
2019    0.918
2020    0.840
Name: 합계 출산율, dtype: float64
</pre>

```python
df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
      <th>2017</th>
      <th>2018</th>
      <th>2019</th>
      <th>2020</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>출생아 수</th>
      <td>471.300</td>
      <td>484.600</td>
      <td>436.500</td>
      <td>435.400</td>
      <td>438.400</td>
      <td>406.200</td>
      <td>357.800</td>
      <td>326.800</td>
      <td>302.700</td>
      <td>272.30</td>
    </tr>
    <tr>
      <th>합계 출산율</th>
      <td>1.244</td>
      <td>1.297</td>
      <td>1.187</td>
      <td>1.205</td>
      <td>1.239</td>
      <td>1.172</td>
      <td>1.052</td>
      <td>0.977</td>
      <td>0.918</td>
      <td>0.84</td>
    </tr>
  </tbody>
</table>
</div>



```python
df = df.T # 행, 열변환(유용하니까 기억)
df
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>출생아 수</th>
      <th>합계 출산율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2011</th>
      <td>471.3</td>
      <td>1.244</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>484.6</td>
      <td>1.297</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>436.5</td>
      <td>1.187</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>435.4</td>
      <td>1.205</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>438.4</td>
      <td>1.239</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>406.2</td>
      <td>1.172</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>357.8</td>
      <td>1.052</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>326.8</td>
      <td>0.977</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>302.7</td>
      <td>0.918</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>272.3</td>
      <td>0.840</td>
    </tr>
  </tbody>
</table>
</div>



```python
import matplotlib.pyplot as plt
import matplotlib
matplotlib.rcParams['font.family'] = 'Malgun Gothic' # Windows
# matplotlib.rcParams['font.family'] = 'AppleGothic' # Mac
matplotlib.rcParams['font.size'] = 15 # 글자 크기
matplotlib.rcParams['axes.unicode_minus'] = False # 한글 폰트 사용 시, 마이너스 글자가 깨지는 현상을 해결
```


```python
plt.plot(df.index, df['출생아 수'])
plt.plot(df.index, df['합계 출산율'])
# 이렇게 보면 합계 출산율 그래프는 제대로 비교해 볼 수가 없으니 x축을 공유하는 쌍둥이를 만들겠음.
```

<pre>
[<matplotlib.lines.Line2D at 0x2958b564eb0>]
</pre>
<img src="data:image/png;base64,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"/>


```python
fig, ax1 = plt.subplots(figsize=(10, 7)) # 2,2같은거 했었는데 없애면 기본값으로 그래프 1개 만듬
ax1.plot(df.index, df['출생아 수'], color='#ff812d')

ax2 = ax1.twinx() # x 축을 공유하는 쌍둥이 axis (즉, y범주는 서로 다르게해서 사용!!!)
ax2.plot(df.index, df['합계 출산율'], color='#ffd100')
```

<pre>
[<matplotlib.lines.Line2D at 0x2958be419a0>]
</pre>
<img src="data:image/png;base64,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"/>


```python
fig, ax1 = plt.subplots(figsize=(13, 5))
ax1.set_ylabel('출생아 수 (천 명)')
ax1.set_ylim(250, 700) # y범위 제한
ax1.set_yticks([300, 400, 500, 600]) # y축 숫자 지정
ax1.bar(df.index, df['출생아 수'], color='#ff812d')

ax2 = ax1.twinx() # x 축을 공유하는 쌍둥이 axis
ax2.set_ylabel('합계 출산율 (가임여성 1명당 명)')
ax2.set_ylim(0, 1.5)
ax2.set_yticks([0, 1])
ax2.plot(df.index, df['합계 출산율'], color='#ffd100')
```

<pre>
[<matplotlib.lines.Line2D at 0x2958cd24640>]
</pre>
<img src="data:image/png;base64,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"/>


```python
fig, ax1 = plt.subplots(figsize=(13, 5))
ax1.set_ylabel('출생아 수 (천 명)')
ax1.set_ylim(250, 700)
ax1.set_yticks([300, 400, 500, 600])
ax1.bar(df.index, df['출생아 수'], color='#ff812d')
for idx, val in enumerate(df['출생아 수']): # 텍스트 넣기 (즉, 레이블 값 넣기)
    ax1.text(idx, val + 12, val, ha='center')

ax2 = ax1.twinx() # x 축을 공유하는 쌍둥이 axis
ax2.set_ylabel('합계 출산율 (가임여성 1명당 명)')
ax2.set_ylim(0, 1.5)
ax2.set_yticks([0, 1])
ax2.plot(df.index, df['합계 출산율'], color='#ffd100')
```

<pre>
[<matplotlib.lines.Line2D at 0x2958ff17be0>]
</pre>
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAzoAAAE1CAYAAAAveJVIAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAABeLklEQVR4nO3dd3yV5f3/8dcnATJIIICIyiwKjjpQQKVVRMWBIuKq4wtItVqgSrUqrWhdddvWUVH0p4LWXRxVlCo4oA5EtAwxIrhR9ggJIYGQz++P+05ystc5OSF5Px+P+5Fzruu+r/u6bo/hfHItc3dERERERESakoR4V0BERERERCTaFOiIiIiIiEiTo0BHRERERESaHAU6IiIiIiLS5CjQERERERGRJkeBjoiIiIiINDkt4l2BhpSQkOApKSnxroaIiIiIyE4rNzfX3b3Rd5g0q0AnJSWFLVu2xLsaIiIiIiI7LTPbGu861ESjj8RERERERERqS4GOiIiIiIg0OQp0RERERESkyVGgIyIiIiIiTY4CHRERERERaXIU6IiIiIiISJOjQEdERERERJocBToiIiIiItLkKNAREREREZEmR4GOiIiIiIg0OQp0RERERESkyVGgIyIiIiIiTY4CHRERERERaXIU6IiIiIiISJOjQEdERERERJocBToiIiIiItLkKNAREREREZEmR4GOiIiIiIg0OQp0RERERESkyVGgIyIiIiIiTY4CHRERERERaXIU6IiIiIiISJOjQEdERERERJocBToiIiIiItLkKNAREREREZEmR4GOiIiIiIg0OS3iXQEREREREREAM0sCugIdgHxgrbv/WJeyFOiIiIiIiEjcmFkyMCo89gdWA+uBZGBXM0sE3gT+4e7za1quAh0REREREYkLMzsJeACYDfwZ+MDd88uc0xk4CZhqZguBse6+udqy3T0GVW6cWrdu7Vu2bIl3NUREREREdlpmluvuraNU1mzgAnf/qobnXwy0dve7qz1XgY6IiIiIiNRUlAMd8xgFJFp1TURERERE4iJWQQ5ojo6IiIiIiDSwcG7O/tWc9iBwMDAMeMXd59TmHgp0RERERESkoXUG9gXOBZ4J0wYQLCn9acT75wlWXHvFzI5z949regPN0ZGmo3ArbPsK8pdB/nLYFvGTBEgfAm2GQtoxkJAa79qKiIiI7JSiPEdno7u3C18/CGx094nh+yeB9939QTP7HXCEu59b07LVoyM7l8Jc2PZ1mWBmGWxbDttXlD43cRdI6gWtj4bCHNj0FGx4CCwZ0o4Ngp70k6FV1/i0RURERESq6nX5JXBZ+PoF4KraFKxARxqfwlzI/yoIXoqCmEqDmY6QtFfQS9NqryCwabVXkJaYUabcfNgyB7Jfg82vwo+vBenJB4ZBz1BIPRQssUGaKSIiItKcmdn1QIqZXRcm9QO2mtk17n4L0MHd1wG4+yoz26VW5WvomsRFcTBTwTCz7T+WPrcomEnqBa16Ba9b9YKkPcsHMzXlDvlLIXs6bJ4OW94DdgS9QG1OCnp60k+AxLb1bamIiIhIkxKtoWtmtoNgs9Cyfuvurcxsvbt3iDh/k7tn1Lh8BToSM7UOZiKDmIifDRFsFGyEnDeDoCf7ddixAWgBrY8MenvaDIWk3rGvh4iIiEgjF8VAZ7u7t6wgfVsY6HwF9HP3jWbWAZjv7j+rcfkKdKReCnPD4KWiYWZlgpkWu5YZXhY5zKwR9Zz4DsidGwY90yHvsyC91V4lQ9xaHwkJreJbTxEREZE4iGKgs83dy32high0ngRmuvvjZjYKONHdz6tx+Qp0pFqVBTP5y6Dgp9Lnlgpmyg4za0TBTG1s+w42vxYEPTlvg+dDQnowtC19KLQZErRbREREpBlowEDnSOBlguWnzwHOcPfZNS5fgY4AULil9DCzyICmwmCmomFmO3EwU1OFWyD7rXBBg+nhs7FgEYP0cIhb8kFgFu+aioiIiMRENAMdYE8g8ouTAcuKAiAzOx0YDvzb3V+oVfkKdJqRwi2le2Yi581UGsxUNMysTXzq39i4Q96CIODZPB22zgvSW3YOFjNoMzRYxlp79oiIiEgTEsVAJw8opHSgA7Dd3ev9hVOBTlPkDptfLL/XTMHK0ue16FTFnBkFM7W2fTVkzwiGuGW/EezdY8nB0tfFe/Z0i3ctRUREROolmhuGxpICnabq892hYFUYzPSKWJ656OeeCmZiqXBbuGdP2Nuz7asgPfnAkt6e1MO0Z4/Eh3sQiBesDn5PFKyG7asgISX4jCb/PHgtIiJSAQU6jVCzCnTyl0GL3SAxPd41EXfI/zJiz57/EuzZ0wHSTwp7e46v+55AIkUKtwQ9i0XBS8GqMu8j0jy3ioISguXUkw8MjpQDg7lnLbtq/pmIiCjQaYyaVaAjjdeOTcHQts2vhXv2rAcSS/bsSQ/37NEXSgEo3BoRsFQVxKwKAp2KJO4CLXcLenhbFP3sVD6tMBvyFsHWRZC3MHi97ZuSchLahkFPUQB0ECTvDwmN/t86ERGJIgU6jZACHWl0fAfkfhSxZ8/iIL14z56TofVA7dnT1BTmlfSwbC/T21IqbVUQfFQksX1JgFI2YGkZEcy06AhWbi+2mtuxOdhLKm8RbA2Dn7xFwdA3ACwYClvc8xP2/rTqAZZQ9/uKiEijpUCnEVKgI43etu9Llq7OeStiz57jg56e9CHQslO8aykVKdxWdcASGdgUZlVcRmK76ntdWu4GiR3jG/x6IWz/rnTPz9ZFwYqOhP+mJKRB8gFlen8O0NxAEZEmQIFOI6RAR3YqhVuCDUqLNivd/iNgkNI/6O1pMxSS+2iIWyz5dihYU36IWEVBzI6NFZeR0LYGvS67BUu6JyQ1bPuirXAL5C0pHQDlLQqGaxZp2aN0z0/KgUGPkBbmEBHZaSjQaYQU6MhOyz344lg0xC13HuDas6em3IMv4YU5wVCwwpzg2JENBWsrD2J2rK+4vIT0iIAlsgemgiAmIblh29rYuMP2FWHQszAMghZB/lKCrRMASwnm+kQufJB8ALRoH9eqi4hIxRToNEIKdKTJKN6z57Vwz55ssKRgz570odDmZGjVPd61rBt38G3lA5LIIGVHZMCSHb6PzI+4tjA7nKRfze+6hNalA5aWZYaQFad1UkAZDYVbIS+zJAAqmgMUGVy27FJm6NuB4UIdLeJXbxERiUmgY2b/cPdLo1qmAh2RnVzhtmDJ6uI9e5YH6ckHlAQ9qYfHbmiQ76giICkTcOwo06NSUdqObKCghjdPCOaCJKQFS6knpAW9LZWlJUbmpwfvE3cJgpfEtNg8H6k596BXrezCB3mZFH8mLAmS9is//K1Fx7hWXUSkOYlRoLPN3aM6AVWBjkhTk/9lEPAU79lTEO7ZMyQc4nZ0yYaRlfaA1CBIKcr3rTWvm6XUPSCp6HxL0Ryl5qBwG+R/UXroW96iICgq0mK3Cnp/9tGKhSIiMRCNQMfMzgciV1i6FZgY8X6xu88ws/8DTgH+7e7P1Ooe8Qp0zCwduAU4C2gPfA0c6O7bzWwocBvQC/gSuMzd3464tjfwIDAAWA/81d3vre6eCnSk2dmxCbLfDOf2zIAd62pxcYswoEgvE4yk1S6tOEhprQnnEl0Fa2Dr4tIrv+UvCYY+AtACkvct3fOTfGAQFClAFhGpsygFOjcC3ao45b/ASmAK8CgwGhjt7jNrfI94BDpmlgi8C3wPXAOsAg4C5gN9gFnAucBsgkbdBezr7j+YWWsgE3gAuA/oC7wCXOjuL1Z1XwU60qz5jmARg9y5wfCfSgOSop/6S7jshHw75C8rM/RtUbAgQpHEXUoPfUs+EJL308IRIiI11FCLEZjZq8BT7v6smZ0JjHL3YTW+Pk6BzoXA74B+7l5YJu854Gt3vzoibRbwX3e/0czGEjRyQET+tcCx7n50VfdVoCMi0kwVbAg25C01/O2ziKGXicFCB0Urv6X0g9ZH7fxLfouIxEC0Ah0z29fdM8ukzXT348LXPwL7uHu2maUCy9y9c03Lj9fSNb8G7isb5IQGA6eVSZsFHB2RP6OC/GvMzLw5TToSEZGaadEe0o4KjiK+A7Z9Vbr3J/cjyHouyE9oE8xra3s6pJ8YDL8UEZFoWgiUHULSP+J1mrtnA7h7rpnVquu9wQMdM2sB9AP+YWYfEgxZWw5cC8yhZL5OpO+ALuHrPYGXKshPBnYB1pa538XAxQCtWmkojoiIhCzsxUnqTTBdNLQjC7a8B1kvwuZ/w6ang4Uv0ocEQU+boZDYNm7VFhFpQiqaMFlVp0VCbQqv1clR0gFIAsYDfyBYbeHvwDSCoAcgt8w1W8JrANIqySfinGLu/rC793P3fi1aaO8FERGpRmLbYFn2ro/Cfqug51vQ/teQ+yH8MAI+3xW+ORk2PAYFtVngQ0REyqgoqCkws6LeifVmthuAme0CbKpN4fEIdIqGq/3N3T9092x3nwq8RrDwAJTvwkqmJLjZXkk+lA+ARERE6s5aBBvxdp4E+66APd+HDpdA3hJYcSF8vht8fSysewC2r4x3bUVEmoICSkad/Rc4L3z9f8B7tSkoHl0c64B8guFmkb4kWGIuH+hKsBJbkW6UDGdbEeZTJj/L3TdEvbYiIiIAlgCtfxEcu/8Vtn4Km1+ErBfgp9/BT5dA6gBoe0YwxK1Vj3jXWERkZ+SUdMb8FXjPzE4BDgYG1qagBg903N3N7CPgF8AnEVk/Bz4CdgOOAz6OyBtM0OMDQSR3HMGS05H5b8WqziIiIqWYQWrf4Oh0M+R/HszpyXoBVl4RHCmHBEFPmzMgee9411hEpDFKNLPryqSlEc7dcffFZjYQOIFgX81FtSk8XstLn0qw8c/ZBAHNCOBmYB/gcOBxYDhB4HM+8GeCfXSyzawzwT46fwIeI1jY4EXgFHf/qKr7anlpERGJufzl4UIGLwaruAEk7Rf29JwRLGGtDUtFZCcWxeWlH6wk6zJ3z693+fFajdnMLgWuIliM4BOCBs0L88YRBDK7Ah8Cv3P3zyOuHUiwWei+wFfA1e7+7+ruqUBHREQa1LYfYPNLQeCz5b9AIbTaMxja1vYMSDlUQY+I7HQaasPQ+opboBMPCnRERCRuCtZA1stB0JPzFlAALbtAm9ODwKf1EcGS1yIijZwCnUZIgY6IiDQKBRsh+9VgTk/2G+D50GJXaDM8CHrSjgFrGe9aiohUSIFOI6RAR0REGp0dOZD9ehj0vAaFWyAxA9oMCxYySD8OElLiXUsRkWLRCHTM7GdAx2pO+x+QTrCI2Qe1XWFZgY6IiEhjUbgVsmfC5hdg8yuwYxMktIb0k4M5PeknQWJavGspIs1clAKdvwOnEWwb80OY3J5gz81NBMtM/wp4BUgEdgD93P2nGt9DgY6IiEgjVLgNtrwb9PRsfjmY42NJkH5CGPScAi3axbuWItIMRXPompltdPd24esHgY3uPjF8fzfB7jR/MLN7CGKX39e07HhsGCoiIiLVSWgF6ccHhz8AW94rWbZ68ytAi2AuT9szoO3wYI6PiMjOp6pel+OAYeHrB4CXa1OwenRERER2Jl4IWz8u2aB021dAQrBqW9szglXcWnWJdy1FpAmL4j46RwGvAicTbBJ6BZANTHb398xsk7tnhOcmAFnunl7j8hXoiIiI7KTcIW9RSdCTvyRITz0sXLb6DEjaM751FJEmJ4qBTiGwpIKs3u6eZGYb3L19xPlZ7t62xuUr0BEREWki8pYGCxlkvQhbPwnSkg8Kh7edDkn7aYNSEam3KAY629293Fr6ZrbN3VuZ2fdAL3fPN7NU4Et3r3GXdUJ9KygiIiKNRPLesOtE6DUf9vkGdv8bJKTB6uvhy/3hy31h1TWQ+0nQGyQiEl/V/SL6HDgyfP2L8H2NqUdHRESkqdu+ErJeCnp7cmYDO6Blj6CXp+0ZkHo4mP72KSI1E8UenW3u3qqydDMbAUwAbgL+DPzN3Z+ocfkKdERERJqRgnXBqm1ZL0DOTPDt0GJ3aHtaEPS0HgimRVlFpHINFeiEr+8DhgMvu/v4WpWvQEdERKSZ2pEFm18Lgp7sGeBbIbEDtDk1CHrSjoWEpHjXUkQamSgvRvBt2WSgc0UBUK3Lr2mgEy7pdjTBOLm9gA5APrAWmA/Mcvev6luhWFKgIyIiUonCLZD9RrhB6atQmA0JbaDNyUEvT0o/SD5AgY+IRDPQOaySLHf3efUuv7pAx8wMGA9cThBxvQt8CawDkoFdgcMINvT5DPiju1e0TFzcKdARERGpgcJ8yHkrDHpegR3rgnRrGQQ7Kf0gpW8Y/OwfbG4qIs1GtAKdWKtJoPM+kAnc7u7LqzgvATiLYKLQRHd/JZoVjQYFOiIiIrXkDtu/DVZq2zo/PD6BHZuCfGsVLGGd0hdS+4XBz35BUCQiTVJTCnSOc/eZNS4wWOP65+7+cX0rF20KdERERKLAHbZ9HQQ9uWHgs/UTKNwc5FsSpPQJe37C3p/kfbXIgUgT0WQCnaZEgY6IiEiMeCFsW16m5+dTKMwJ8i0FUg4u3fOTtDdYYnzrLSK11qwCHTNLc/ecKNQnphToiIiINCAvhPwvS4a75YbBj+cG+QmtIfngksAnpS8k9daePiKNXDQDHTP7G8FKa9Vy9z/UquzKAh0zexQ4j4p3LLUw/UDgQeAY4C3gFHfPr00FGpICHRERkTjzHZC/NGLY23zYuiBY2hogIR1SDikJfFL7Qas9FfyINCK1CXTMrCfwCHCfu79cQf4/gJHAS9WV5e6/rlU9qwh0OgDpwEKCgMaAm4HNwB3h+3OBQQQrst0DvOXud9SmAg1JgY6IiEgj5AWQl1nS81Mc/IR/O01oGwQ/xT0//aDVz8Bq9EdgEYmymgQ6ZtYNuIag46QlcE4lgU46sBy4wN1fi2Y9K50V6O7rgfVmtsPdvwsrkg1sing/DBjv7p+b2fUEwU6jDXRERESkEbIWkHJAcBD+wda3Q97npRc8WHcv+LYgP7FdyRLXRT0/Lbsr+BFpPA4l6DT5BfBqZSe5e7aZTQL+AEQ10KnJqmsb3L19+PpBYKO7Twzfrwc6unuhmbUCVrt7u2hWMJrUoyMiIrITK9wGeZ9F9PrMh7zFQVAEkNih9GIHKf2gZRcFPyJRVts5Omb2LXBZRT06YX4roIO7r4xODcNyqwp0zGwHJfNxoGSi0HZ3TzKzjZGBjZltdvc20axgNCnQERERaWIK84NgJ7LnJ+8zoCDIT+xYOvBJ7Qst9lDwI1IP0Q50YqW6Be0LgU6UXgnBgJ/C13lmlhD26LQAtsagjiIiIiIVS0gKApnUftAhTCvcCnmLSi91nf0msCPIb7Fb6SFvKf2g5W7xaoHIzqiFmc2PeP+wuz8ct9pUorpAx919Q9lEK/kryPcECxUsAA4I34uIiIjET0IKpB4WHEUKc2HrwtJLXWe/TvA3XaBl54g5P0U9P7vGpfoiO4ECd+8X70pUp75bFL8IXGtmY4CJwAv1r5KIiIhIlCWkQusBwVGkcEuwulvksLfNr1I8Yr9l1zDoCXt/UvpCi13iUXsRqYP6BjqTgDnAaoJenQvqWyERERGRBpHQGlr/MjiK7MiGrf8r3fOzOWJ7jxZ7QPK+kLQPJO1b8rrF7pr3I9LIVBfoJJrZY2XSiv8vdvccM+sH9AC+dffCKNdPREREpOEkpkPawOAosiOrJPjJ+wzyv4CN/4TCzSXnJLSF5H1KAqCkfYP3rXoGy2eLSIOr7v+8iZWkX1P0Igxuvo5ajUREREQak8S2kDYoOIq4Q8HKIOjJy4T8zOB19kzY+HjJedYKWvUKg6AwAEraB5L3DnqURCRmqt1HpynR8tIiIiISczuyIH9p6QAoLxO2fUXxym8QbHCatE84/G3fktctOsat6iI1UdvlpeNFgY6IiIhIQyjMD4Kd/MwwCPoifP0FeG7JeYkdIgKgiLlALbuDJcSv/iKhaAQ6ZnYW8OsqTvkAuBV4ChgOTAPOr81UGQU6IiIiIvHkhbB9RQUBUCbsWFtyniVD0t4l83+Kh8L1goTk+NVfmp0oBTr7A30J5v//P+A3ZU75HtgX+D/gauB24El3f6DG91CgIyIiItJIFayPmAcUBkD5mbDtW4qXwSYBWv2s9PC3otct2sWx8tJURXvompltc/dWFaT/F5jg7h+a2UDgVnc/osblKtARERER2ckUboX8L8v3AuV/CZ5fcl6LThUHQC27aDlsqbMYBDrb3b1l+DoNyHf37Wa2Fujk7oVm1hJY5e4dalqu1jsUERER2dkkpEDKQcERyXcEvT1lA6CsZ2HDpojr08L5P2UCoKS9wFo2ZEtEAP4d8foGIAv4C5BQNCcnDHxqNUlNPToiIiIiTZ07FKwpGfqWFzEMbvuKiBNbBMFO2Q1Rk/YJ9hgSIXarrpnZcOA2YIC7bzKzje7eLswzgh6dTjUtTz06IiIiIk2dGbTsFByR+wEB7MgOlsOOXAo7PxM2TwcKSs5r2SViQ9R9IHl/SDkEEtMasiXSBJlZC+AS4HzgRHffFGb9YGa93f1LYB/gx1qVqx4dERERESnHt0P+V+UDoPwvoDAnPCkBkn8OKf0h9dDgSN5fw9+auGj16JjZh0AC0At4Gxjt7jkR+XcAGcA44CHge3e/qcblVxbomNl1NSxjmrt/Hl6T6e771vTmDU2BjoiIiEg9ucP2HyFvIeR+DFvnQe482LE+yLdkSDk4CHpSwuCn1Z5a/KAJiWKgcwKQCOwFjARmA1e7+/YwfxdgHrA7sAI4zN031Lj8KgKdB8sknQB8AXxXJv3/ufun4TVb3T2lpjdvaAp0RERERGLAHbZ9EwY9HweBz9ZPwLcG+YntSvf6pPSHlrvFt85SZ7GYoxPOwbkFOAI4qahnx8xSgT7A/9yLPlA1LLOmQ9fM7CVgiru/UsU5ue6eWpsKNCQFOiIiIiINxAsgb0npXp+8z4AdQX7LrqV7fVL6asGDnUSsFiMIy74T6OPux9e3rCoXIzCzZKCtu68uk743sMbdN9a3AiIiIiLSBFmLiCWww03vC3Nh6/+CoCd3XhAAZb1QdEGw0EFxr8+hkHwAJJTbR1KatmuBO8ysZdEQtrqqskfHzE4GfufuJ5nZDcB/3H2umb0G/Nfdby9zvnp0RERERKTmCtbB1vklwU/uPNixNsizJEjpE9Hr0x+SekHttlORKItlj0401TjQiUibAPwfwWSgvDLnK9ARERERkbpzh+3fRcz1Cef7FIbf4RLaQmr/iGFv/aHlHvGtczOzswQ6NQ6Hzaytmf0NuBA4uWyQI9IQFi9eTGJiIlOnTi1Oe/DBB+nVqxcpKSnsv//+PPPMM5Vef+aZZ9KjR48q77Ft2zauv/56unfvTlJSEj179mTFihVVXiMiIiJRYgatekDGWbDHXbDnbPh5FvReDF0ehYxzoGA9rLkDvhsOmZ0hswt8ezqsuR2y34IdWfFuhTQClc7RMbMfgFQg1cx+AnYBlgOHa26OxMvEiROxiOUpX3nlFa6++mpefPFFBgwYwLvvvsuvfvUrunbtyhFHHFHq2vnz5/PKK6+wxx5V/9Xn/PPPZ926dfznP/+hZ8+efPnll7Rp0yYm7REREZEasMRgf57k/aH9BUFa4VbYuqBkoYPcebD5pZJrkvYpGe6WeigkHwQJSXGpvpRnZg/U8dJrahqLVLUYwcnAQOBcYDzQM/z5nJn9yt03mdmtwFjAAQP06ZGYmTZtGjk5OfTp06c47ZNPPuGYY47hmGOOAWDIkCEMGDCAjz76qFSgU1BQwMUXX8z48eOZNm1apfd46623ePfdd/n6669JSQlWSj/ggANi0yARERGpu4QUaD0gOIoUbCg93yf7Ddj4RJBnLSG5T+ngJ2lvzfeJn6LFztIIRozdG5G3H7A38FLZiyhetq96lQY67r7IzLoCWe7+CfAJ8K9wh9LZZnZkWKGnanozkbpau3YtV155JW+++SbnnXdecfrpp5/OQw89xKxZs/jlL3/JrFmz+OKLL5g0aVKp62+77Tb69OnDoEGDqgx0pkyZwm9/+9viIEdERER2Ii3aQ/rxwQHhfJ8VpXt9Nj4O68PvCQltILVfyVyflEOhZWdtbtoA3P1GADPrBJxR9D5MOwM4NTKtLqpcXrqSSv3RzDoCj7r7WZREYzViZscDb5RJfs3dh4b5Q4HbgF7Al8Bl7v52xPW9gQeBAcB64K/ufi/SZLk7I0eO5PLLL6d3796l8g466CAmTpzIcccdV5z23HPP0atXr+L3c+bM4YknnuDjjz/mvffeq/Jec+fO5cgjj+TEE09kzpw5dO3alauuuorf/OY30W2UiIiIxJ4ZtOoaHG3PCNJ8B+QvLVnoIHcerPsbFK1k3GL30r0+Kf2gRbv4tUHqrLpAZxHwjwrSJwCX1PGe7YEP3P2XZTPMrC/wT4LhcrOB0cArZravu/9gZq2BWcADwClA3zD/B3d/sY71kUbuxhtvJDU1ld///vfl8v7zn/9w++238+abbzJgwABmzpzJxRdfTNeuXRkwYAA//fQT559/Ps8//zwZGRnV3uvHH3/k/vvv56677mLatGm88847nHvuuey2224MHTo0Bq0TERGRBmWJkLxfcDA6SCvMg7yFEfv7fAyb/11yTavepVd6S+kDCclxqHyTtBewh5l9D3QC8oEsYHl999KpMtBx9x+AHypIXwfcUMd7diDoianIBGCyu/8nfP9g2HV1AXAjMAr4MWL/nv+GK8FdCijQaYKeffZZnnnmGebNm1dh/rXXXssNN9xQ3KNz2mmnkZmZyfXXX8/LL7/MKaecwoQJE+jfv3+N7peQkMCvf/1rTjzxRABOOeUULrroIqZMmaJAR0REpKlKSIbUw4KjyI5NkDu/pNcn523YVDRjo2gz1P4lG5wm7RMEUVJjZnYs8AIwCfgXsIJgzk4f4DLgbTM7pq7BTrWzr8zsJjOr8RA3MzvAzM6q4pQOwLpK8gYDM8qkzQJ+UU3+4WYaTNkUTZw4ke+//57u3buTkZFBRkYGCxYsYNy4cQwdOpTPP/+83GIBBx10EEuWLGHatGl8+umnXH311cXXnnPOOXz//fdkZGRUOIytc+fOdO/evVRa7969Wb26ViM0RUREZGeXmAHpg2HXidDjZdjvJ9h3BXR/ETpeGeRvehpWXABf7g9bqh4eLxW6HTjf3a9w97nuvsLdv3D3Z4FfAgXA2XUtvCbLTLQEPjWz0WZW6apqZtYrXCbuKSCzivI6ACPNbJuZLTez28ws1cwyCIa1fV3m/O+ALuHrPSvJTyZY/rpJitw75ttvv8XMKjyKeiGKFBYWMnnyZH75y3KjBEuZN28eRx11FG3btqVjx45cdNFFZGdnx7JJNTZnzhwyMzNZsGBB8bHffvtx00038cgjj9CzZ0+WLFlS6pqFCxfSo0cPTj/9dL755ptS1953333sscceLFiwgH79+pW734ABA/jggw9KpS1ZsoS99947pu0UERGRnUDLztD2NNj9Nug5C36+EXpnQtfHg0UNpLb2cfd/V5Th7jsIVl3br66FV9tT4+5Xm9nzBMPK7jGzT4GlBMPPkgjG0g0gCIjuB65099wqivwzMDF83Y9gvs0ewDVhWtlrt1CybHVaJflQydLWZnYxcDFAq1atqqhW4xW5d0yPHj1w91L5BQUF7Lffflx88cXFaf/85z+58847+e677+jZs2eV5a9atYo//elPHHHEEaxatYrzzz+f8ePHM2XKlOg3ppa6dOlSLq1Vq1bssssu7LbbbvzhD3/g2muvZc8996R///68/fbb3HHHHTz66KOkpaWRlpZW6tpdd92VFi1aFG8aumzZMi688EJeffVV2rZty+9//3sGDhzIYYcdxpAhQ/jPf/7DE088wYcfftgQzRUREZGdiSVA8j7BIXXxk5n1d/ePK8k/nGDefp3UaEiau/8PONfMUsIb9iLofdlAsGDBbe5eVS9OZFmbI97ONrPRwEfAdWFa2WgkmZLgZnsl+VA+ACq638PAwwCtW7f2is5pzCraO6asqVOn0qZNG0477bTitOeff56LLrqIlJSUckstlzVs2LDi1+np6Vx11VWMHz++3nVvCBdccAFmxqWXXso333zDXnvtxeTJkznzzDNrdH1WVhaZmZnk5ubStm1bDjnkEB577DGuvfZaRowYwT777MPzzz/Pz3/+8xi3RERERKTZ+QvwspldC7zo7lnhdJT9gD8ABwN1XvrWyvYONLRwJbUcYH+CvXqOjIzqzOxK4Ah3H25mM4E33f2uiPx+wCx3z6juXq1bt/YtW7ZUd1qjsXbtWvr371+8d8wll1zC6NGjS51TUFBAr169mDRpEieddFK5MqZOnco999zDggULanzfJ554ggceeIC5c+fWswUiIiIi0tSYWa67t45SWccRjOwaAGwjGKW1EZgC3OnuG+padq330YmB/gQ9Nd8BHwLHAZHdV4OB18LX74X5d5XJfyv21WxYVe0dE+m5554jKSmJIUOG1PueOTk5fPTRR9x2223cd9999S5PRERERKQq7j4TmBn25HQCctw9Jxpl12QxgqgysyvMbP9wAYKjgEeASWGD7gauMrOjzCzZzH5L0NMzNbz8EYIV1saF+UcQdGvd2dDtiLWq9o6JdO+993LJJZcUz+Gpqz59+pCens6QIUM4++yzGThwYL3KExERERGpKQ+silaQA3EIdIBuwDsEixlMItiQ9EoAd3+FoOvqn8Am4BzgeHfPDvN/BIYSLC6QRTD35iJ3/6hhmxBbRXvHVLcYwMKFC1m8eDHnn39+ve+5YMECtmzZwrx581i0aBGDBg2ioKCg3uWKiIiIiMRD3OfoNKSdZY5Oz549WblyJUlJJQvJ5eTk0KpVK4455himT58OwFVXXcWXX37Jv/9d4ap8QN3m6OTn55ORkcHMmTM54ogj6twOEREREWl6ojlHJ5YawxwdKWPOnDnlelOGDRvGqFGjGDFiRHHaSy+9xJ///Oeo3z9ybx4RERERkWgzs+uAHpXlu/sFZvZY+WS/sKb3qPPQNTOr8y6lUrUuXbrQo0ePUkfk3jEA33zzDV999RVHHnlkrctftmwZAwcOJCsrC4BrrrmGBQsWsHXrVpYvX86IESPYe++9Oeyww6LaLhERERGR0LcEe3NWdgCMAJaEx+fh+xqrVY+Omf0OeCZc5m0K8FxtrpfomTdvHhkZGdVuBlqRsnvHuDvDhg1jzZo17LHHHgwfPpzJkyfTokWMO/xuOjC25cfLdYviXQMRERGRRs3dn6jBaTvc/W9Fb8zsL7W5R63m6JjZ18Ax7v6tmW1195Ta3CzedpY5Os2GAh0RERGRnU605+iYWVegLfC5uxeWuU9qZe+rU+Oha2a2D0FU9W2Y1HxWMRARERERkagysxQz+zfwFTAH+NbM+kWr/NrM0bkWuDdaNxYRERERkWZtYvizvbu3B64AnjKzltEovEaBjpkNA/oS7FsjIiIiIiJSX+cAvy3aJNTd/wV8BhStiFWvJYCrnG1uZueENyrauHNbRHaimZ1VQQWWu/un9amUiIiIiIg0ee3cfVWZtG+BTuFrN7N2BPFGrYOe6pbVGgfsD7wPZFZw7dgKbvpvQIGOiIiIiIhU5Qcz6+PuCyLS+gNFK7IlA+soiTdqtUZAlYGOuw80s7TwZvcRBD5F8t39mNrcTEREREREJHQ38LSZjQfWABcC2919IYC713nPT6jBPjrunmNm5wH/M7PD3P2j+tyw2dOSyiIiIiIiuPsTZpYC3AO0A94AzohW+TXaEdLd88zsRuA64ORo3VxERERERJovd38IeCgWZdemO+g5oJ+ZtY9FRUREJDoWL15MYmIiU6dOBWDZsmUMGTKEDh060K5dO8466yxWrlxZ6prZs2dz2GGHkZKSQrdu3fjzn//Mjh07qr3XmjVrSEtL44YbbohBS0REROquxoGOuzswG+gZJtVruTcRqZ+6fJkFeOONNzj88MNJSUmhXbt2TJ48ucLyv/zyS8ys1LH//vvHskkSJRMnTsSs5Ff06tWrGT16NF999RWLFy9m+/btnHfeecX5q1atYsiQIYwYMYJ169bx+uuv88ILL3D77bdXe6+bbrqJvLy8mLRDRESaFzM7INzWJipqO8FnpLvPD1/PiVYlRKT2avtlFmDGjBmMGjWKCRMmsGHDBr744gsGDRpUYfkbNmxgjz32wN2Lj88++yyWTaqXugR+v/jFL8oFc/Pnz6+g9NIacy/GtGnTyMnJoU+fPsVpRxxxBGeffTYZGRl06dKF66+/njlz5rB9+3YAMjMzSU5O5tJLL6V169bsv//+jBgxgrlz51Z5r3nz5vHOO+8wZMiQWDZJRESajwOB4dEqrFaBjrvnR7w+IVqVEJHaqcuX2YKCAsaNG8fkyZM5/fTTSUlJoVOnTuyzzz4V3mP9+vV06NChIZoTFXUJ/DZs2MCbb75ZKpjr169ftfdqrL0Ya9eu5corr+Shh6oe6pydnU3Hjh1p2TLYePqwww6jffv23HvvvWzZsoVFixbx1FNPMWrUqErLyMvL48ILL+SBBx4gKSkpqu0QERGJhnot2SYiDa+uX2bfffddAE499dQa3Wf9+vXssssu9aprQ6lL4Ad1C+Yaay+GuzNy5Eguv/xyevfuXeE5W7du5aOPPuKKK67gjjvuKE5PTU3lqaee4k9/+hNpaWkcdNBBDBo0iLPOOqvS+1166aUMHjyYo446KuptERGRps/MtppZbuQBPAaMKpseHkXnd63pPRToiOxE6vNldu7cuRx88MHcfPPNdOzYkY4dOzJ69Gg2bdpUYTnr169nzpw5tGzZku7duzNmzBjWr18fi2bVS10DP3dn48aNtQrmGnMvxo033khqaiq///3vK8wfPnw4qampDBgwgEMPPZTTTjutOG/FihUMHz6cv//972RlZTF//nzeeecd/va3v1VY1tSpU1m0aBF33nlnTNoiIiLNwj7AvmWO3sCeFaTvG3H+TzW9gQIdkZ1Ifb7M/vjjj8ydO5ctW7awdOlSPvzwQ5YvX85vfvObCsu6+OKL2bhxI9nZ2bz44ossWbKEM888k2BdksahPoHfpk2b2LFjB7169SIjI4NBgwbx/vvvV3m/xtqL8eyzz/LMM88wZcqUSs95+eWXycvL4/PPP6egoIBDDjmEzZs3A3DPPfdw9NFHM3bsWNq0aUPfvn155JFHKhyiN2fOHK699lr+9a9/FQeNIiIiteXu39XxqH5J0JACHZGdRH2/zCYkJNCxY0fuuOMO2rdvz1577cWkSZN44YUXyM7OLldW69atSU9PJzk5mb59+zJt2jRmz57NF198EbM21lZ9Ar+2bduybt06cnJyWLJkCQMHDmTw4MFkZmZWWFZj7sWYOHEi33//Pd27dycjI4OMjAwWLFjAuHHjGDp0aPF5SUlJ7LPPPjz00EO4O6+88goAn3/+OQcccECpMg866CA2b97MDz/8UCr9hhtuYO3atRx44IHF93rllVe4/fbbOfDAJrohsoiIxISZXWRmJ5lZTMbKK9AR2UnU98ts586d6datW6kyi3pBVq9eXe39O3XqRIcOHfjuu++i2Kq6i0bg16FDB1q2bEnnzp256aabOOaYY4pXbYvU2Hsx5syZQ2ZmJgsWLCg+9ttvP2666SYeeeSRCq8pWmUOoGfPnixZsqRU/sKFC0lMTKRr19JDoZ9++mmWLl1a6l6DBw9mzJgxvP7667FpoIiINFUPArcCK8xsjpmdHs3CW9Tm5DDaOsjd34pmJUSkenPmzKGgoKBU2rBhwxg1ahQjRoyo8JrIL7MDBgzg/vvvJz8/v3h+yZIlS0hKSqJ79+7V3v/bb79l3bp19OzZs9pzG8LEiRNZuXJlqbrn5OQwbtw4pk2bxvTp04HSgd+ee+7JK6+8Uunz2m+//SoM5CJ7MYrk5uYyffp0XnzxRRYtWhTl1tVOly5dyqW1atWKXXbZhd12243bb7+dgQMHctBBB7Fx40buuOMO8vLyigPk3/3ud/Tr149HHnmEs88+m+XLl3PxxRczduxYkpOTWbZsGRdeeCGvvvoqu+22W7l7paamFi/6ICIiUgvb3b2PmbUBzgTuMLNLgPPcfVV9C69tj87ewDX1vamI1F6XLl3o0aNHqaPsl9kPPviALVu2sGLFCi699NJSX2aPPvpoOnXqxNixY1m7di1Lly5lzJgxXHHFFbRs2ZJly5YxcOBAsrKyAHjooYeYN28eubm5fPrpp5x11lkMHz680rkwDa2+vRgV+fjjjysM5Hb2XozU1FRGjRpFhw4dGDBgAFu2bOHDDz+kbdu2AOy7777MmjWLxx9/nM6dO3PGGWdw+umnFy9GkJWVRWZmJrm5ufFshoiIND0O4O6b3f0x4OfA58AnZrZ3fQuvVY+OiDReRV9mV6xYQceOHTnuuONKfZmFYCjXmDFj6NatG+np6fz617/mxhtvBEp/mW3bti1ZWVkMHz6cDRs20K1bN84991yuvvrqeDWvnPr2YsybN694U9Ft27bx97//nQULFvD4448D7PS9GJEbn44fP57x48dXef6AAQP473//W2Fev379WLt2baXXTps2rW6VFBERieDu24BLzOxb4G0z61ufnp1KAx0z20oYZUVIAFqG61yXuyQ8f293/6GCfBGJstp+me3evTszZsyoMK/sl9kJEyYwYcKE6FQ0DqoL/Fq1asXtt9/ORRddRHp6OgMGDOCjjz4qHgpXNvATERGRhuHufzWzPYAXzOwIr+OSr1bZdWZW/aD9iq2ozbJvDal169a+ZcuW+Fbipia6KtF1dZijoGchIiIistMxs1x3bx2Fcra6e0oleS2BT4FH3P3eupRfaY+OuzeOpZVERERERKQp2q+yDHffbma/pxYbhJZVozk6ZjYUOBLoBuQAXwNPaoiaiIiIiIjUhbt/U03+2/Upv8pV18xsNzObD9wJbAdmA0sJoq/PzOza+txcRERERESaLzO7ycxqvECamR1gZmfV5NzqCr0feNvdy81INrMewGwzm+3uFS/VIyIiIiIiUrmWwKdm9nfgGXfPr+gkM+sFXA4cAZxXk4KrC3SOAzpVlOHu35rZA8DxgAIdEZFY0KIdIiLShLn71Wb2PDABuMfMPiUYQbYeSCKIRQYQBET3A1e6e402dqsu0MkD2oQ/K9IWyK7JjUSkGk3xC62+zEqcLV68mD59+vDoo48yevRoAKZPn87VV1/NsmXL6N27N/fccw/HHHNMqeveeOMNrr/+ehYuXEhycjK33XYbY8aMKVf+V199xVVXXcVbb71FYmIixx57LPfccw+dO3duiOaJiDQJ7v4/4FwzSwEOB3oB7YENwCLgNnfPrG251QU6TwNPmdkod18ZmWFmpwEXAYfV9qYiIlVqikEfKPCLg4kTJ2Jmxe8/+eQTRo4cyTPPPMNRRx3F1KlTGTZsGJmZmXTt2hWAGTNmMHr0aB588EGGDBnC5s2b2bhxY4Xl/+1vf+O4445jypQp5OTkcMkll3DGGWcwd+7cBmmfiEhT4u5bgXfCo96qC3QmAJOBb81sHvADkAb8HMgHznP3r6NRERERkWiaNm0aOTk59OnTpzjtzjvvZMyYMZx44okAjB07lhdeeIHHHnuM66+/noKCAsaNG8fkyZM57bTTAEhJSaFTpwpHcXPnnXeSlpYGQNu2bZk0aRKdO3dm5cqV7L777rFtoIiIVKnKVdfcfbu7XwjsCUwC5gL/AkYCP3f3mbGvooiISO2sXbuWK6+8koceeqhU+qxZsxgyZEiptMGDB/PBBx8A8O677wJw6qmn1ug+RUFOkdTUVAC2bdtWl2qLiEgUVRnoFHH3Fe7+rLvf5+7/dPcP3N1jXTkREZHacndGjhzJ5ZdfTu/evYvTN23axIYNG+jZs2ep87t3786KFSsAmDt3LgcffDA333wzHTt2pGPHjowePZpNmzbV6N7PPPMMPXv2pFu3blFrj4iI1E2NAh0REZGdxY033khqaiq///3vS6Xn5OQAJb0uRVq3bk1+frCa6Y8//sjcuXPZsmULS5cu5cMPP2T58uX85je/qfa+M2bMYMKECTzwwAOl5gWJiEh81HhzHhERkcbu2Wef5ZlnnmHevHnl8lq2bAmUH1aWl5dXHPwkJCTQsWNH7rjjDgDat2/PpEmT6NOnD9nZ2aSnp5crt7CwkJtvvpl7772Xf/3rX5xwwgnRbpaIiNRBpYGOmf0SeASobIjal+4+3Mz+AZwFTHP3S2JQRxERkRqZOHEiK1eupHv37sVpOTk5jBs3jqOOOoqkpCR++OEHdtttt+L877//vng4W+fOncsNOysa/rZ69epygU5+fj5nnnkmq1atYv78+fzsZz+LVdNERJoFMzvN3V+KRllV9eh8DhRtGmDA60DkDM4tZnYuMAj4LXCDmY1w9yejUTEREZHamjNnDgUFBaXShg0bxqhRoxgxYgTnnnsuM2fOpH///sX5s2bN4uSTTwZgwIAB3H///eTn55OUlATAkiVLSEpKKhU8FZkwYQLZ2dnMmTOHlJSUGLZMRKTZmAREJdCpdI6Ou28EMsPjc2BH+LMo7Qvg/4Cr3P3fwFXAedGolIiISF106dKFHj16lDpatWrFLrvswm677cbll1/OXXfdxezZs8nLy+Ohhx7is88+K95M9Oijj6ZTp06MHTuWtWvXsnTpUsaMGcMVV1xBy5YtWbZsGQMHDiQrKwt357HHHuOaa65RkCMiUgdm1sbMTjSzXSKTI/J/ER59w/d/q0351S1GsApYGf5sDayOSLsN2B+YHZ77HsH+OiIiIo3SsGHDuOWWWxg5ciQZGRk8++yzvPnmm6WGpL388susXLmSbt26ceSRR3Lsscdy4403ApCVlUVmZia5ublkZ2eTk5PD8ccfj5mVOu655544tVBEZOdgZu2AhcDDwGdm1iXMipw2Mxt4FHghfD+yNveocjECdy8XCJmZFS0tbWbnhTuY4u55Zta6NjcXERGJtfnz55d6P27cOMaNG1fp+d27d2fGjBkV5vXr14+1a9cWv9dOCyIidTYOeMPdx5jZH4E/ApcShBsPEPTsrHX3fc3sm/CaWi1pWZflpW80s4vC1zvK5BXWoTwREREREWlejgImh68fAgZG5K0IjyJ1+qtSrQIdMzsPOAeYHiatK+pmMrPOwIa6VEJERERERJqVzsByAHffBCQVZbj7re5+S31vUGWgE04Q6hBOAvonwepqg9x9ZXjKTILAh/DnrPpWSEREREREmrxWQF7E+7Ijxeqtug1D14c/E4GPgWHuvjoi/0HgQzM7GvgF8MtoV1BERERERJqcDUAn4EczMyI6YMysI3WbYlNKlQW4e0uCaGtP4EVgdjh8rSj/C+AYYC5wrLt/Xt8KiYiIiIhIk7cAOCF8/UtgWUTej5Seo1Mn1UZKHvjG3e8ABgNXmdnVEfn/c/e/uPuntb25mR1gZjvMbHRE2lAzW2xmeWa2yMyOKXNNbzN7y8xyzewHM/t9be8rIiIiIiJx9Qhwm5ldD0whWGYaAHdvFXa4FEk3s/FEzOOpieqGrpXi7ivMbBjwkpndU7S0dD3cSsQqCuFmQP8EziVYN3s08IqZ7evuP4TLV88CHgBOAfqG+T+4+4v1rIuIiDRmNx0Y7xrExnWL4l0DEZEG5+4fm9klBN/773T3osXOIldYez78+QpwMCX76dRIrQKdsFI/AP1qe11ZZnYmkEbQbVVkAjDZ3f8Tvn/QzM4ALgBuBEYBP7r77WH+f8MdUi8lGFonIiIiIiI7AXf/F/CvMskWkX9Z+PPCupRf60k+ZrZ7XW5UpoyOwF8JVnGLNBgou0vbLIKFDqrKPzycxCQiIiIiIjuvD6JVUI0DHTPbK3z5VX1uGAYk/wTudvcvI9IzgPbA12Uu+Q7oEr7es5L8ZGCX+tRLRERERETiy93PiFZZ1e2jc3f4szXwUVFyFeefWoN7Xg/kuvu9ZdLTwp+5ZdK3UDLxKK2SfKhkcpKZXWxm881sfkFBQQ2qJyIiIiIiDcnMOprZ/0WzzOp6dH4X/twHWFpBhTaHx3dh0jNVFWZm5xBMOPp1Bdnbw5+tyqQnUxLcbK8kH8oHQAC4+8Pu3s/d+7VoUespSSIiIiIiEnt7UBJ7REVNv/mfCLxRQXoC8HPgf+H76ubJ3ArsDnwXMaUmjWAVtdlAPtAVWBVxTTdKhqutCPMpk5/l7huqbYWIiIiIiMSdmf2qTFJ3oEMF6e+6+5rwmk/d/ZCa3qPaQMfMWhEs83xCBdmF7v5dLdYBGFjBPV8BngCeJOgROg74OCJ/MPBa+Pq9MP+uMvlv1bQCIiIiIiISd2MrSPupTLoTdHisCd8fVJsbVBromFl7gh6bJ4C33f3riLzdqcOKbe5ebodTM9sGrHP3VeGcoMfN7H2COUHnA/sDZ4WnPwJcYWbjgMcIlrn+A8GeOiIiIiIishNw96NjfY+qenRuIghmTidY7SzSCoJhajnRrIy7v2Jm1xCsyrYr8CFwvLtnh/k/mtlQ4D7gboIV4C5y948qK1NERERERHYeZnaQuy+sbzmVBjrufomZ/Ra4F5gEDCvJ8sSwEpvrWwF371fm/QMEc3YqO38O0Ke+9xURERERkcbFzPYAZplZP3f/rtoLqlDd8DN396uAPcysaEycNuYUEREREZGoMrMOwOvADfUNcqDm82weAUZUkd/CzA6tb2VEREQkembMmEH//v1JS0tjjz32YMKECUTuKbd9+3bMrNSRlpZWnN+jR49y+WZGcnJyRbejsLCQG2+8kW7dupGamsqhhx7KzJkzY95OEdm5mVlLMzudYI7+k+4+KRrlVrfqWlHvzSzgwgryixYoWAM8RTB3R0RERBqBNWvWcPfdd3PwwQezbNkyfvWrX9G6dWuuv/56ANavXw9Afn4+rVqV3aYOvv3223Jpv/71r0lPT6/wfvfffz+PPfYYL730Evvuuy/Tpk1j+PDhLF68mJ49e0avYSLSJJjZWoKV1doSxCUXu/uj0Sq/uh6dieHPb4GO4WsvynT3PuHPvdy9l7v3ilbFREREpH7OP/98jjjiCFq3bk2fPn0YO3Yss2bNKs5fv3496enpFQY5FVm6dCkvvPACEydOrDD/k08+4YwzzqBv376kpqYyatQoOnfuzP/+978KzxeRZu9wYAAwiGC/zdvNbHxRppmdY2bLzOxLM1tW28Kr7NFx97vCnwVm1qfonrW9iYiIiMRfdnY2nTt3Ln6/fv16dtlllxpff/PNNzNmzBh22223CvPPOeccxo4dy3nnncd+++3Hs88+y44dOzj66JivIisiOyF3/yp8+RXwoZk9DLxmZp3c/RpgNvCbupZf7YahERXZFP5MqevNREREpOFt2rSJd999l8cff5yXXnqpOH39+vV8++23tGrVil122YWjjjqKO+64g27dupUr47vvvmPatGl8+eWXld5nyJAhnH766fTv3x+AFi1aMHv2bNq3bx/9RolIk+PuP5jZUcA8M1vs7s8CK+taXq03/RQREZGdR0ZGBu3atWPEiBFcfvnl7L///sV5J554Ihs3biQnJ4dZs2aRl5fH8ccfT15eXrlyJk2axMknn0zXrl0rvdfDDz/M9OnT+eijj8jKymLy5MmceuqpfPXVV5VeIyISyd03Ar8Glte3LAU6IiIiTdimTZvYvHkzM2fO5Pnnn+fss88uzktJSaFt27a0atWK/fbbj2eeeYZ169bxzjvvlCpj+/btTJ06lTFjxlR6n8LCQq699lruv/9+Dj30UNq0acOFF17Iaaedxh133BGz9olI0+Pu77n7/PqWo0BHRESkiUtPT2fAgAE8+eSTTJs2jRUrKl4kNTk5mZ/97Gd8913p7SvefPNNCgsLGTRoUKX3WLduHWvXruWAAw4olX7QQQexZMmSerdBRJoHMzu7+rNqRoGOiIhIM5GQEPyzb1bxukKbN2/myy+/LLcU9EsvvcQpp5xCixaVT+3t2LEjaWlp5YKahQsX0qNHj/pVXESaPDM7P3w5JVplKtARERFpoi677DIyMzPJy8tj8eLFjBw5kqFDhxavvPbMM8/wzjvvkJOTwxdffMGvfvUrevXqxbHHHluqnJkzZ3LkkUeWK3/ZsmUMHDiQrKwszIzLL7+c8ePH88knn5Cdnc2UKVN44oknGD9+fLlrRUTM7NPwZ3ugaIyrReQfEh4Hhu//Vpvya7zqmoiIiOxcNm3axLHHHsvGjRvp1q0b5513HldddVVxfkFBAaNHj2bVqlXsscceDB06lGeffZbExMTic1avXs3333/PIYccUq78rKwsMjMzyc3NpW3btlx//fWkpaVxzjnnsHLlSn7+85/z6quvcthhhzVIe0Vkp1O0OsohwIIK8j8GvgfaAB2AscAVNS1cgY6IiEgTNXXq1CrzR44cyciRI6s8p1OnTrh7hXn9+vVj7dq1xe8TExOZMGECEyZMqHVdRaRZOwV4pYL0Le7+MzPbGL6v1X6eCnRERERERCQuzGx34DTggCpOq/ivLdVQoCMiIiIiIg3KzA4HEoGZwF/dPSsi71yisJaAAh0REREREWlohxIMRdsTeK9M3kXUcphaRbTqmoiIiIiINCh3vw/YAZxDmSWl3f0Ydz+6vvdQoCMiIiIiIvHg7v5v4GszGxLtwhXoiIiIiIhIPD0DnFVFfrKZTaCWsYvm6IiIiOyMbjow3jWIjesWxazoGTNmcN1115GZmUmbNm0YMWIEt956Ky1aBF+H1q5dy5VXXslrr73Gtm3bGDBgAPfeey/77LNPqXLeeOMNrr/+ehYuXEhycjK33XYbY8aMKXe/wsJC/vKXv/Doo4+ybt069t9/f2655RaOO+64mLVRZCdTNA9nNnBDBfmPhz+fBnoBT9SmcPXoiIiISLOwZs0a7r77blavXs3rr7/Oyy+/zC233FKc/+CDD7L33nvz+eef880339CzZ09OOukktm7dWnzOjBkzGDVqFBMmTGDDhg188cUXDBo0qML73X///Tz22GO89NJLrFu3jksuuYThw4fz9ddfx7qpIjuLX4Y/1wDflc1090vDn79x94vc/aLaFK5AR0RERJqF888/nyOOOILWrVvTp08fxo4dy6xZs4rz//CHPzBx4kR23XVXOnTowD/+8Q9WrVrFggULACgoKGDcuHFMnjyZ008/nZSUFDp16lSux6fIJ598whlnnEHfvn1JTU1l1KhRdO7cmf/9738N0VyRRs/d54U/3d2L5ujMiVb5CnRERESkWcrOzqZz587F79PS0krlt2jRgqSkJLZt2wbAu+++C8Cpp55ao/LPOeccXnzxRebPn09ubi6PPfYYO3bs4Oij672YlEiT5e4nRKsszdERERGRZmXTpk28++67PP7447z00kuVnjdjxgx27NjBIYccAsDcuXM5+OCDufnmm/nHP/4BwMknn8w999xDRkZGueuHDBnC6aefTv/+/YEgcJo9ezbt27ePfqNEpBz16IiIiEizkZGRQbt27RgxYgSXX345+++/f4XnzZ8/n1GjRnH33XeTnp4OwI8//sjcuXPZsmULS5cu5cMPP2T58uX85je/qbCMhx9+mOnTp/PRRx+RlZXF5MmTOfXUU/nqq69i1j4RKaFAR0RERJqNTZs2sXnzZmbOnMnzzz/P2WefXe6chx9+mMGDB/PXv/6VCy+8sDg9ISGBjh07cscdd9C+fXv22msvJk2axAsvvEB2dnapMgoLC7n22mu5//77OfTQQ2nTpg0XXnghp512GnfccUfM2ykiGromIiIizUx6ejoDBgzgySefpHv37qxYsYIuXbrg7owZM4a3336bd999lz59+pS6rnPnznTr1q1UWu/evQFYvXp1cc8PwLp161i7di0HHHBAqfMPOuggnn766dg0TERKUY+OiIiINEsJCcHXILNgK4977rmH9957j7lz55YLcgAGDBjAJ598Qn5+fnHakiVLSEpK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UtybU9aaNWsYOnQop556KkcddVSF5wD06dOH9PR0hgwZwtlnn83AgQOjVf2dirunR7zOcff/RSvIgfhsGHqPme1rZslmdgDBnjnT3b1o5bW7gavM7KjwnN8SDHGbGuY/AhxuZuPC/COAPwB3NnBTRERERKSJyMvLY9y4cVx33XW8/fbbHHTQQaXyFy5cyOLFizn//PMrvP7999+nb9++HHrooUyZMqXKey1YsIAtW7Ywb948Fi1axKBBgygoKIhaWyQQj6FrGQS9L+2A74GngbuKMt39FTO7hiAA2pWgh+f4cKgb7v6jmQ0F7iMIir4CLnL3jxqyESIiIiLSNGzcuJEhQ4aQkZHBggUL2GWX8gv5Pvnkkxx//PEVLgP9+OOPc9lll/Hggw9yzjnn1Oieqamp9OnTh2eeeYaMjAzmzp3LEUccUe+2SIkGD3TcfXQNznkAeKCK/DlAn+jVSkRERESaq9GjR9OzZ0+efPJJEhIqHvD00ksv8ec//7lc+scff8z48eN5++236du3b63vbWbFh0RXY5ijIyIiIiISF2vXruWVV17hxhtvrDTI+eabb/jqq6848sgjy+VNnTqVoUOHVhrkLFu2jIEDB5KVlQXANddcw4IFC9i6dSvLly9nxIgR7L333hx22GHRa5QACnREREREpBlbuXIlAL179y7Vu2JmvPzyywDMmzePjIwMevbsWeH1Tz/9dLlr+/TpA0BWVhaZmZnk5ga7oLg7w4YNo127dhx//PF06dKFt956ixYt4rYYcpOlJyoiIiIizdaBBx6Ie9VbLZ599tmcffbZFea9+OKLVV7br18/1q5dW/z+1ltv5dZbb619RaXW1KMjIiIiIiJNjgIdERERERFpchToiIiIiIhIk6NAR0REREREmhwFOiIiIiIi0uQo0BERERERkSZHy0uLiIiISNN004HxrkH0Xbco3jXYaahHR0REREREmhwFOiIiIiIi0uQo0BERERERkSZHgY6IiIiIiDQ5CnRERERERKTJUaAjIiIiIiJNjgIdERERERFpchToiIiIiIhIk6NAR0REREREos7M/mRmP5jZVjN708x6NOT9FeiIiIiIiEhUmdl44HzgeGB34Efg32ZmDVUHBToiIiIiIhI1ZpYAXA1c5u6Z7r4J+B3wM2BgQ9VDgY6IiIiIiETTz4H2wNtFCe6eC3wA/KKh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"/>


```python
fig, ax1 = plt.subplots(figsize=(13, 5))
ax1.set_ylabel('출생아 수 (천 명)')
ax1.set_ylim(250, 700)
ax1.set_yticks([300, 400, 500, 600])
ax1.bar(df.index, df['출생아 수'], color='#ff812d')
for idx, val in enumerate(df['출생아 수']):
    ax1.text(idx, val + 12, val, ha='center')

ax2 = ax1.twinx() # x 축을 공유하는 쌍둥이 axis
ax2.set_ylabel('합계 출산율 (가임여성 1명당 명)')
ax2.set_ylim(0, 1.5)
ax2.set_yticks([0, 1])
ax2.plot(df.index, df['합계 출산율'], color='#ffd100', marker='o', ms=15, lw=5, mec='w', mew=3)
# 마커 속성 사용
```

<pre>
[<matplotlib.lines.Line2D at 0x29590425f70>]
</pre>
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAzoAAAE1CAYAAAAveJVIAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAABjB0lEQVR4nO3dd3xUVf7/8dcnCWkkEJooICCKvaBgYVVExYIiYlvULyCrqwussriu7Iqube29oejPVXTtoqKyooIF1oKICiKigg1RSmghIQQI+fz+uJNk0jPJTOr7+XjcR2buuffcc68xzGfOOZ9j7o6IiIiIiEhTElffDRAREREREYk2BToiIiIiItLkKNAREREREZEmR4GOiIiIiIg0OQp0RERERESkyVGgIyIiIiIiTU5CfTegLsXFxXlKSkp9N0NEREREpNHKzc11d2/wHSbNKtBJSUlh06ZN9d0MEREREZFGy8w213cbqqPBR2IiIiIiIiKRUqAjIiIiIiJNjgIdERERERFpchToiIiIiIhIk6NAR0REREREmhwFOiIiIiIi0uQo0BERERERkSZHgY6IiIiIiDQ5CnRERERERKTJUaAjIiIiIiJNjgIdERERERFpchToiIiIiIhIk6NAR0REREREmhwFOiIiIiIi0uQo0BERERERkSZHgY6IiIiIiDQ5CnRERERERKTJUaAjIiIiIiJNjgIdERERERFpchToiIiIiIhIk6NAR0REREREmhwFOiIiIiIi0uQo0BERERERkSZHgY6IiIiIiDQ5CnRERERERKTJUaAjIiIiIiJNTkJ9N0BERERERATAzJKAnYF2wBYg091/rUldCnRERERERKTemFkyMCK07QusAtYCycAOZhYPvA3c7+7zqluvAh0REREREakXZnYS8CAwC/gn8JG7byl1TGfgJGCymS0ARrv7xirrdvcYNLlhatmypW/atKm+myF1KX81ZE2F/ExI2AFanxr8FBEREZEaMbNcd28ZpbpmAee7+/fVPP4ioKW7313lsQp0pEnKXw2//RWyXgTfWrzfEqH1UOh0JyR0qL/2iYiIiDRSUQ50zGMUkCjQkaYnfzUsPRy2Lq34mMTdYLePFOyIiIiIRCiagU4saY6OND2/XVZ5kANB+W+XQdcn66ZNIiIiIlIkNDdn3yoOewg4EBgMvObusyO5hgIdaXzcYXsm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evfuTWpqKiNGjKBz58588cUX5R4vIs3eYUBfoD/Bepu3mNnYwkIzO9vMlpjZd2a2JNLKK+3RcffbQz/zzaxX4TUjvYiIiIjUv+zsbDp37lz0fu3atbRv377a599www2MGjWKHXfcsdzys88+m9GjR3Puueey995789xzz7F9+3aOPjrmWWRFpBFy9+9DL78HPjazR4D/mllHd78SmAX8sab1V7lgaFhDNoR+ptT0YiIiIlL3NmzYwPvvv88TTzzBK6+8UrR/7dq1/PTTTyQmJtK+fXuOOuoobr31Vrp27Vqmjp9//pkpU6bw3XffVXidgQMHcvrpp3PwwQcDkJCQwKxZs2jbtm30b0pEmhx3/8XMjgLmmtlCd38OWFHT+iJe9FNEREQaj4yMDNq0acOwYcO49NJL2XfffYvKTjzxRNavX09OTg4zZ84kLy+P448/nry8vDL1TJw4kZNPPpmdd965wms98sgjTJs2jU8++YSsrCwmTZrEqaeeyvfff1/hOSIi4dx9PfAHYGlt61KgIyIi0oRt2LCBjRs3MmPGDF544QWGDh1aVJaSkkLr1q1JTExk77335tlnn2XNmjW89957JerYtm0bkydPZtSoURVep6CggKuuuooHHniAQw45hFatWnHBBRdw2mmnceutt8bs/kSk6XH3D9x9Xm3rUaAjIiLSxKWnp9O3b1+eeuoppkyZwvLl5SdJTU5OZpddduHnn0suX/H2229TUFBA//79K7zGmjVryMzMZL/99iux/4ADDmDRokW1vgcRaR7MbGjVR1WPAh0REZFmIi4u+GffrPy8Qhs3buS7774rkwr6lVde4ZRTTiEhoeKpvR06dCAtLa1MULNgwQK6d+9eu4aLSJNnZueFXj4erToV6IiIiDRR48aNY/HixeTl5bFw4UKGDx/OoEGDijKvPfvss7z33nvk5OTwzTff8Pvf/56ePXty7LHHlqhnxowZHHnkkWXqX7JkCf369SMrKwsz49JLL2Xs2LF89tlnZGdn8/jjj/Pkk08yduzYMueKiJjZ56GfbYHCMa4WVn5QaNs/9P7OSOqvdtY1ERERaVw2bNjAsccey/r16+natSvnnnsul19+eVF5fn4+I0eOZOXKlXTq1IlBgwbx3HPPER8fX3TMqlWrWLZsGQcddFCZ+rOysli8eDG5ubm0bt2aa665hrS0NM4++2xWrFjBPvvsw+uvv86hhx5aJ/crIo1OYXaUg4D55ZR/CiwDWgHtgNHAZdWtXIGOiIhIEzV58uRKy4cPH87w4cMrPaZjx464e7llffr0ITMzs+h9fHw848ePZ/z48RG3VUSatVOA18rZv8nddzGz9aH3Ea3nqUBHRERERETqhZntBJwG7FfJYeV/21IFBToiIiIiIlKnzOwwIB6YAdzh7llhZecQhVwCCnRERERERKSuHUIwFG1X4INSZRcS4TC18ijrmoiIiIiI1Cl3vw/YDpxNqZTS7n6Mux9d22so0BERERERkfrg7v4q8IOZDYx25Qp0RERERESkPj0LnFVJebKZjSfC2EVzdERERBqj6/ev7xbExtVfxqzq6dOnc/XVV7N48WJatWrFsGHDuOmmm0hICD4OZWZm8re//Y3//ve/bN26lb59+3Lvvfey5557lqjnrbfe4pprrmHBggUkJydz8803M2rUqDLXKygo4F//+hf//ve/WbNmDfvuuy833ngjxx13XMzuUaSRKZyHMwu4tpzyJ0I/nwF6Ak9GUrl6dERERKRZWL16NXfffTerVq3ijTfeYOrUqdx4441F5Q899BB77LEHX3/9NT/++CM9evTgpJNOYvPmzUXHTJ8+nREjRjB+/HjWrVvHN998Q//+/cu93gMPPMBjjz3GK6+8wpo1a7j44osZMmQIP/zwQ6xvVaSxODz0czXwc+lCd78k9POP7n6hu18YSeUKdERERKRZOO+88zjiiCNo2bIlvXr1YvTo0cycObOo/K9//SsTJkxghx12oF27dtx///2sXLmS+fPnA5Cfn8+YMWOYNGkSp59+OikpKXTs2LFMj0+hzz77jDPOOIPevXuTmprKiBEj6Ny5M1988UVd3K5Ig+fuc0M/3d0L5+jMjlb9CnRERESkWcrOzqZz585F79PS0kqUJyQkkJSUxNatWwF4//33ATj11FOrVf/ZZ5/Nyy+/zLx588jNzeWxxx5j+/btHH10rZNJiTRZ7n5CtOrSHB0RERFpVjZs2MD777/PE088wSuvvFLhcdOnT2f79u0cdNBBAMyZM4cDDzyQG264gfvvvx+Ak08+mXvuuYeMjIwy5w8cOJDTTz+dgw8+GAgCp1mzZtG2bdvo35SIlKEeHREREWk2MjIyaNOmDcOGDePSSy9l3333Lfe4efPmMWLECO6++27S09MB+PXXX5kzZw6bNm3i22+/5eOPP2bp0qX88Y9/LLeORx55hGnTpvHJJ5+QlZXFpEmTOPXUU/n+++9jdn8iUkyBjoiIiDQbGzZsYOPGjcyYMYMXXniBoUOHljnmkUceYcCAAdxxxx1ccMEFRfvj4uLo0KEDt956K23btmW33XZj4sSJvPTSS2RnZ5eoo6CggKuuuooHHniAQw45hFatWnHBBRdw2mmnceutt8b8PkVEQ9dERESkmUlPT6dv37489dRTdOvWjeXLl9OlSxfcnVGjRvHuu+/y/vvv06tXrxLnde7cma5du5bYt/vuuwOwatWqop4fgDVr1pCZmcl+++1X4vgDDjiAZ555JjY3JiIlqEdHREREmqW4uOBjkFmwlMc999zDBx98wJw5c8oEOQB9+/bls88+Y8uWLUX7Fi1aRFJSEt26dStxbIcOHUhLS2PRokUl9i9YsIDu3btH90ZEpFwKdERERKRZGDduHIsXLyYvL4+FCxcyfPhwBg0aVJR57dFHH2XcuHG0a9eu3POPPvpoOnbsyOjRo8nMzOTbb79l1KhRXHbZZbRo0YIlS5bQr18/srKyMDMuvfRSxo4dy2effUZ2djaPP/44Tz75JGPHjq3L2xZpthToiIiISLOwYcMGjj32WNq0acOZZ55J//79ef7554vKV6xYwUUXXYSZldjGjRtXdMzUqVNZsWIFXbt25cgjj+TYY4/luuuuAyArK4vFixeTm5sLwDXXXMPIkSM5++yz2WmnnZg0aRKvv/46hx56aJ3et0hzpTk6IiIi0ixMnjy50vJ169ZVWUe3bt2YPn16uWV9+vQhMzOz6H18fDzjx49n/PjxEbVTpDkws82AR3JK6Pg93P2X6pygQEdEREREROranjU877fqHqhAR0RERERE6pS7/2xmp9fkvOoeq0BHRERERETqwyWhn72Bb4Gc0Pu9gTXA6lLHO/BydStXMgIRERGRZmb69OkcfPDBpKWl0alTJ8aPH09+fn6JY6ZNm8Z+++1HcnIy+++/P++++25RWX5+Ptdeey3dunUjJSWFXr168d///rfca73//vtlEjwUbqNGjYrpfUrD5u5Hu/vRwPfAn8LezwZuLHwfth0TSf0KdERERESamdWrV3P33XezatUq3njjDaZOncqNN95YVP7ZZ58xfPhwbr/9dtavX8/o0aMZPHgwv/wSzAGfNWsWS5cuZcaMGWRmZjJ27FjOOOMMFi5cWOZa/fv3x91LbOvXr6dt27ZceOGFdXbP0vCYWS8z24GgpyaSxATVokBHREREpJk577zzOOKII2jZsiW9evVi9OjRzJw5s6j8tttuY9SoUZx44omkpKQwevRoDjvsMB577DEADj30UJ566il233130tLSOP/88+nXrx/Tpk2r1vXvuOMO+vfvT+/evWNyf9JofA4sA3oBE8zsyND+D4Cfalu55uiIiIhI43b9/vXdgti4+ss6u1R2dnbRwqkAM2fO5JVXXilxzIABA3jvvfcASEtLK1NHamoqW7durfJaGzZs4P777+fDDz+sZaulCdgEtAb2AAYC/8/MNgAXu/u82lauHh0RERGRZmrDhg1MnTqVJ554ggkTJhTtW7duHT169ChxbLdu3Vi+fHm59axevZp3332X/v37V3nNiRMncthhh7HvvvvWuv3S6Lm7F7j7Yne/C9gL+DfwlpldVtvK1aMjIiIi0gxlZGSQlZVFy5YtueWWW4oCj5ycIPFVampqieNbtmzJli1bytSzevVqBg0axKmnnspRRx1V6TXz8/N58MEHefjhh6N0F9KUuLsT9Oq8DbxmZl3c/dKa1qceHREREZFmaMOGDWzcuJEZM2bwwgsvMHToUABatGgBUGYYWl5eXpng58MPP6R3794ccsghPP7441Vec9q0acTHx3PSSSdF6S6kkbPydobWyjkKOM7MxtW0cgU6IiIiIs1Ueno6ffv25amnnmLKlCksX76c9u3bk5SUVJRhrdCyZctKDGd74oknGDRoELfffjsPPPAACQlVDxR6+umnOe2004iL00dQAaBHRQXuvgE4E2hf08r1WyYiIiLSzBUGHmZGfHw8ffv2ZcaMGSWOmTlzJsceeywAn376KWPHjmXmzJmcffbZ1brGli1beOONNzj99NOj23hptNw9s4ryb9z9qprWr0BHREREpJkZN24cixcvJi8vj4ULFzJ8+HAGDRpUlHnt0ksv5fbbb2fWrFnk5eXx8MMP89VXXzFy5EgAJk+ezKBBgypMD71kyRL69etHVlZW0b6PPvqIbdu2cdhhh8X8/kRAgY6IiIhIs7NhwwaOPfZY2rRpw5lnnkn//v15/vnni8oHDx7MjTfeyPDhw8nIyOC5557j7bffJj09HYAVK1bw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"/>


```python
fig, ax1 = plt.subplots(figsize=(13, 5))
ax1.set_ylabel('출생아 수 (천 명)')
ax1.set_ylim(250, 700)
ax1.set_yticks([300, 400, 500, 600])
ax1.bar(df.index, df['출생아 수'], color='#ff812d')
for idx, val in enumerate(df['출생아 수']):
    ax1.text(idx, val + 12, val, ha='center')

ax2 = ax1.twinx() # x 축을 공유하는 쌍둥이 axis
ax2.set_ylabel('합계 출산율 (가임여성 1명당 명)')
ax2.set_ylim(0, 1.5)
ax2.set_yticks([0, 1])
ax2.plot(df.index, df['합계 출산율'], color='#ffd100', marker='o', ms=15, lw=5, mec='w', mew=3)
for idx, val in enumerate(df['합계 출산율']): # 텍스트 넣기(즉, 레이블값 넣기)
    ax2.text(idx, val + 0.08, val, ha='center')
```

<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAzoAAAE1CAYAAAAveJVIAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAACAPklEQVR4nO3deZzN1f/A8dd7FrObsS/D0JQ1IhRKtqiUnVJCWhS+iPo2lfSlRWmlLMmvRBsVEUqhQhKiLFmyJbuZwYxZzH5+f3zuLHfmzn5n9X4+Hp/H3Ps553M+53wM7vueTYwxKKWUUkoppVR54lLSFVBKKaWUUkopZ9NARymllFJKKVXuaKCjlFJKKaWUKnc00FFKKaWUUkqVOxroKKWUUkoppcodDXSUUkoppZRS5Y5bSVegOLm4uBgvL6+SroZSSimllFJlVmxsrDHGlPoOkysq0PHy8iImJqakq6GUUkoppVSZJSKXS7oOeVHqIzGllFJKKaWUyi8NdJRSSimllFLljgY6SimllFJKqXJHAx2llFJKKaVUuaOBjlJKKaWUUqrc0UBHKaWUUkopVe5ooKOUUkoppZQqdzTQUUoppZRSSpU7GugopZRSSimlyh0NdFSZcvToUbp27cry5cuzzTNr1iwaNmyIp6cnjRs3ZsGCBXbpERERDB8+nEqVKuHn58fgwYOJiIgAYP369YiIw2PkyJFF1zCllFJKKeVUGuioMuH48eM89thjtGjRgs2bN2eb78iRI6xcuZIvv/ySixcv8sYbbzB69Gi+//77tDyDBw8mOjqa/fv3s3v3bk6dOsXw4cMB6Ny5M8YYu+PixYtUrlyZESNGFHUzlVJKKaWUk7iVdAWUyott27YRFRXF5s2b6dWrV7b5AgMD+e6773B1dQWgV69eDBkyhGXLlnHHHXewe/duNmzYwJkzZ6hYsSIACxcu5Oqrr+bYsWPUr18/S5lvvvkmnTt3pnXr1kXSNqWUUkop5Xwa6KgyYeDAgQwcODDXfJ6enlnOeXt7ExkZCcDBgwepU6dOWpADUL9+fQIDA9m2bVuWQCciIoKZM2fy66+/Fq4BSimllFKqWOnQNVWuXb58meXLl9O5c2cAateuzcmTJ4mJiUnLk5CQwKVLlwgLC8ty/ezZs2nXrh3NmjUrriorpZRSSikn0EBHlVsxMTHcfffdBAcHc//99wPQtm1bGjRowKOPPsqZM2cIDQ3lscceIzk5GXd3d7vrk5KSmDNnDmPHji2J6iullFJKqULQQEeVS/v27aNt27Z4eHiwcuVK3NysUZqurq6sWrWKmJgYmjRpQosWLWjevDm1a9emRo0admWsWrUKV1dX7rzzzpJoglJKKaWUKgQNdFS5s2bNGm6++WYeeeQRli5dio+Pj116nTp1WL58OREREZw5c4ZRo0Zx8uRJrr/+ert8n332Gf369cPFRf+aKKWUUkqVNboYgSpXTpw4wd13383nn3/OXXfdladrPvroI1q1akVQUFDaufj4eL777ju+++67oqqqUkoppZQqQvpVtSrzQkJCmDlzJgCLFy+mWbNmOQY5P/30E+fPnycmJobPPvuMKVOm8Pbbb9vl2bx5M4mJibRr165I666UUkoppYqGBjql3NGjR+natSvLly/PNs+sWbNo2LAhnp6eNG7cmAULFmSbd9WqVYgI69evd5geGhqKr68vU6ZMKVS9i9ORI0c4fvw4AGfOnGHz5s2IiN0REBCQln/x4sVcddVVVK9enQ8//JCVK1dyww032JW5bds2mjZtioeHR3E2RSmllFJKOYkGOqXU8ePHeeyxx2jRogWbN2/ONt+RI0dYuXIlX375JRcvXuSNN95g9OjRfP/991nyJicnM2nSpLTNNB158cUXiYuLc0obisqxY8fo27dv2vulS5fyxhtvAPD2229jjMlyREREpOWfN28ely5dIiYmhp9++om2bdtmucfTTz/Nzp07i7glSjmfM74c+eSTT2jevDne3t7Ur18/7e9XqpSUFF5++WXq1q2Lp6cnHTp04K+//iqC1iillFIFp4FOKbVt2zaioqLYvHkzNWvWzDZfYGAg3333HS1btsTLy4tevXoxZMgQli1bliXv9OnTuf766+16NzLf8+eff6ZHjx7OaoZSqpg488uRiIgIFi5cSHh4OJ9++imvvfYaCxcuTEt/8803WbRoEd9++y2nTp2iTZs23HHHHXb7UymllFIlTQOdUmrgwIF8/vnnNG/ePMd8np6eWXpovL29SUhIsDv3999/M2vWLN58802H5cTFxfHwww8zZ84cHa6lVBnkzC9Hxo4dS6tWrfD29qZDhw7cf//9rFu3Li192rRpvPvuu1x33XVUqVKFt99+Gy8vL5YsWVKkbVRKKaXyQwOdcuby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"/>


```python
fig, ax1 = plt.subplots(figsize=(13, 5))
fig.suptitle('출생아 수 및 합계출산율') # 총 제목 넣고 끝
ax1.set_ylabel('출생아 수 (천 명)')
ax1.set_ylim(250, 700)
ax1.set_yticks([300, 400, 500, 600])
ax1.bar(df.index, df['출생아 수'], color='#ff812d')
for idx, val in enumerate(df['출생아 수']):
    ax1.text(idx, val + 12, val, ha='center')

ax2 = ax1.twinx() # x 축을 공유하는 쌍둥이 axis
ax2.set_ylabel('합계 출산율 (가임여성 1명당 명)')
ax2.set_ylim(0, 1.5)
ax2.set_yticks([0, 1])
ax2.plot(df.index, df['합계 출산율'], color='#ffd100', marker='o', ms=15, lw=5, mec='w', mew=3)
for idx, val in enumerate(df['합계 출산율']):
    ax2.text(idx, val + 0.08, val, ha='center')
```

<img src="data:image/png;base64,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YDp16sS6detSJQto2rQpa9euZfr06fz222889thjidfeeuutbNu2jdDQUH766adU96tevTq1a9dOdq5evXrs3bs3VV0REREppKwYBDaEcrdD9Qlw/q9wQTSctxSqjYfQvhBQ36sb2PDUdYf+l3q6WnrccW+/IcnIGOB259xDzrklzrkdzrkNzrlpwGV4mSZ65bTxrKzR8Qd+M7P+ZpZuVjUzO9+XJu4jYH0G7ZUH+prZcTPbbGbPmVmwmYXiTWtLuWPUP0AN3/tz0ykPJEVShKIk6d4xW7duxczSPBJGIRLEx8fz5ptvctlll2XY/rJly7jiiisoU6YMFStWZMCAAURHR+fnI2XZokWLWL9+PStXrkw8GjVqxNNPP83bb79N3bp1Wbs2+SzKVatWUadOHbp3787ff/+d7NrXXnuNatWqsXLlSlq2bJnqfm3atGHx4sXJzq1du5b69evn63OKiIhIASsW4GVbq3Af1Hof6m+ACw5C+ftO1cnuupuTWqeTiQbOuTSjQefcSbysa41y2nimIzXOucfM7FO8aWXjzOw34E+86WcBQGWgDV5A9DrwH+fckQyafAIY7nvfEpgIVAMe951Lee1hTqWtDkmnHNJJbW1m9wD3AJQoUSKDbp25ku4dU6dOHVJmyouLi6NRo0bcc889iec++OADXnjhBf755x/q1q1LRvbs2cOjjz5K27Zt2bNnD7fffjtDhw7lvffey/uHyaYaNWqkOleiRAkqVKhAlSpVePDBBxkxYgTnnnsuF198Md9//z3PP/8877zzDiEhIYSEhCS7tlKlShQvXjxx09BNmzZx11138eWXX1KmTBnuv/9+2rVrR6tWrejYsSPffPMN77//fuKaHxERETmL+IUmTy+d3Uxqudr49Kywy8wuds79mk55a2BhThvP0pQ059zvwL/MLMh3w/PxRl8O4CUseM45l9EoTtK2DiX5uNDM+gNLgSd951JGI4GcCm5OpFMOqQOghPtNAiaBl146K308k6S1d0xKkydPpnTp0nTr1i3x3KeffsqAAQMICgpiwoQJGd6jS5cuie9LlSrFww8/zNChQ3Pd99PhzjvvxMwYMmQIf//9N+eddx5vvvkmPXv2zNL1UVFRrF+/niNHjlCmTBmaN2/Ou+++y4gRI+jTpw8NGjTg008/5YILLsjnJxEREZEzXumbYNeQrE1f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ulWrVpQrV45XX32Vw4cPs3r1aj766CP69euXbhuxsbHcddddTJw4kYCAgDx9DhERkbyQq5RtInL65fTL7IIFCwC46aabsnSf/fv3U6FChVz19XTJSeAHOQvmztRRDOccffv25YEHHqBevXpp1jl69ChLly7loYce4vnnn088HxwczEcffcSjjz5KSEgITZs2pX379tx8883p3m/IkCF06NCBK664Is+fRUREij4zO2pmR5IewLtAv5TnfUdC/ZpZvYcCHZFCJDdfZpcsWcJFF13Es88+S8WKFalYsSL9+/cnMjIyzXb279/PokWL8Pf3p3bt2gwcOJD9+/fnx2PlSk4DP+ccBw8ezFYwdyaPYjz11FMEBwdz//33p1netWtXgoODadOmDZdccgndunVLLNuxYwddu3bllVdeISoqiuXLl/PDDz/w8ssvp9nW5MmTWb16NS+88EK+PIuIiJwVGgANUxz1gHPTON8wSf1dWb2BAh2RQiQ3X2Z37tzJkiVLOHz4MH/++Se//PILmzdv5u67706zrXvuuYeDBw8SHR3NF198wdq1a+nZsydeXpIzQ24Cv8jISE6ePMn5559PaGgo7du35+eff87wfmfqKMa0adOYOnUq7733Xrp1Zs6cSWxsLOvWrSMuLo7mzZtz6NAhAMaNG8eVV17JoEGDKF26NC1atODtt99Oc4reokWLGDFiBJ999lli0CgiIpJdzrl/cnhknhLUR4GOSCGR2y+zxYoVo2LFijz//POUK1eO8847jwkTJvD5558THR2dqq2SJUtSqlQpAgMDadGiBdOnT2fhwoVs2LAh354xu3IT+JUpU4aIiAhiYmJYu3Yt7dq1o0OHDqxfvz7Nts7kUYzhw4ezbds2ateuTWhoKKGhoaxcuZLBgwfTqVOnxHoBAQE0aNCAt956C+ccs2bNAmDdunU0adIkWZtNmzbl0KFDbN++Pdn5UaNGER4ezoUXXph4r1mzZjFmzBguvLCIbogsIiL5wswGmNkNZpYvc+UV6IgUErn9Mlu9enVq1aqVrM2EUZC9e/dmev/KlStTvnx5/vnnnzx8qpzLi8CvfPny+Pv7U716dZ5++mmuuuqqxKxtSZ3poxiLFi1i/fr1rFy5MvFo1KgRTz/9NG+//Xaa1yRkmQOoW7cua9euTVa+atUq/Pz8qFkz+VTojz/+mD///DPZ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kbPCvn37GDt2LHv37uXrr79m5syZjB49OrH8jTfeoH79+qxbt46///6bunXrcsMNN3D06NHEOnPmzKFfv36EhYVx4MABNmzYQPv27dO83+uvv867777LjBkziIiI4L777qNr16789ddf+f2oIoXFZb7XfcA/KQudc0N8r3c75wY45wZkp3EFOiIiInJWuP3222nbti0lS5akWbNmDBo0iPnz5yeWP/jggwwfPpxKlSpRvnx5xo8fz549e1i5ciUAcXFxDB48mDfffJPu3bsTFBRE5cqVU434JFixYgU9evSgRYsWBAcH069fP6pXr87vv/9+Oh5X5IznnFvme3XOuYQ1Oovyqn0FOiIiInJWio6Opnr16omfQ0JCkpUXL16cgIAAjh8/DsCCBQsAuOmmm7LU/q233soXX3zB8uXLOXLkCO+++y4nT57kyitznUxKpMhyzl2XV21pjY6IiIicVSIjI1mwYAFTpkxhxowZ6dabM2cOJ0+epHnz5gAsWbKEiy66iGeffZbx48cDcOONNzJu3DhCQ0NTXd+xY0e6d+/OxRdfDHiB08KFCylXrlzeP5SIpKIRHRERETlrhIaGUrZsWfr06cMDDzxA48aN06y3fPly+vXrx9ixYylVqhQAO3fuZMmSJRw+fJg///yTX375hc2bN3P33Xen2cakSZOYPXs2S5cuJSoqijfffJObbrqJLVu25NvzicgpCnRERETkrBEZGcmhQ4eYN28en376Kb169UpVZ9KkSXTo0IGXXnqJu+66K/F8sWLFqFixIs8//zzlypXjvPPOY8KECXz++edER0cnayM+Pp4RI0bw+uuvc8kll1C6dGnuuusuunXrxvPPP5/vzykimromIiIiZ5lSpUrRpk0bPvzwQ2rXrs2OHTuoUaMGzjkGDhzI999/z4IFC2jWrFmy66pXr06tWrWSnatXrx4Ae/fuTRz5AYiIiCA8PJwmTZokq9+0aVM+/vjj/HkwEUlGIzoiIiJyVipWzPsaZOZt5TFu3Dh++uknlixZkirIAWjTpg0rVqzg2LFjiefWrl1LQEAAtWvXTla3YsWKhISEsHbt2mTnV61aRZ06dfL2QUQkTQp0RERE5KwwbNgw1q9fT2xsLGvWrKFv37506tQpMfPa22+/zbBhwyhfvnya11955ZVUrlyZQYMGER4ezp9//snAgQN56KGH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"/>
