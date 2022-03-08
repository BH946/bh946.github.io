---
title:  "[Python]Web_Scraping(Selenium, Requests, BeautifulSoup)"
categories : WebScraping
tag : [python, 웹스크래핑, 웹파싱, bs4, headless]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
---


**=> 정적, 동적 인터넷 파싱 및 동작법을 설명한다.**



## 0. User_agent

**사용자가 접근하는 것처럼 보이게 하는 방법**

* 예로 쿠팡의 경우 유저가아니면 접근 못함.

### 가져오는 법

**1. 구글에 user agent string을 검색해서 what is my user agent? 들어간다.**

**2. 안에 있는 user agent를 복사한다. 그리고 나서**

**3. url = "http://nadocoding.tistory.com"**

**4. headers = {"User-Agent":"복붙"}**

**5. res = requests.get(url, headers=headers) 이렇게 3문장!**



## 1. 셀레니움(Selenium), BeautifulSoup => 동적

=> 주관적인 내 경험들만 작성. (자세히는 관련 공식문서 참고)

### Selenium, BeautifulSoup 준비물

```python
1. 크롬 드라이버 다운
2. pip install selenium
3. pip install bs4
```

### Selenium, BeautifulSoup 사용법

**기본 import**

```python
from selenium import webdriver # 셀레니움 모듈 import
from bs4 import BeautifulSoup # bs4 모듈 import
```

**좀 더 다양한 import**

```python
from selenium.webdriver.common.keys import Keys # Key입력 가능
from selenium.webdriver.common.by import By # explicitly wait 사용위해
from selenium.webdriver.support.ui import WebDriverWait # explicitly wait 사용위해
from selenium.webdriver.support import expected_conditions as EC # explicitly wait 사용위해
from selenium.webdriver.common.action_chains import ActionChains # 스크롤바 관련
```

**최초 실행 방법**

```python
browser = webdriver.Chrome() # 다운받은 크롬 드라이버 사용하는것. 괄호 내부는 경로.
browser.get("http://naver.com") # 홈페이지 접근
browser.set_window_position(1, 1) # 위치 설정
browser.set_window_size(1020, 800) # 창 크기 설정
```

**find_element(s).. 사용법(s는 복수형)**

* by_class_name()란 속성이 'class'인것의 값을 찾음.

  ```python
  browser.find_element_by_class_name("link_login") # 'link_login' 찾기(활용가능)
  ```

* by_id()란 속성이 'id'인것의 값을 찾음.

* by_tag_name()란 '< >' 태그 의미.

* by_css_selector()란 css로 가져온 값 찾음.

* by_xpath()란 xpath로 가져온 값 찾음.

* by_link_text()란 a태그같은 hyperlink를 텍스트를 통해 찾을 수 있다.

* by_partial_link_text()란 위와 동일하지만 텍스트를 부분만 입력해서 찾을 수도 있다.

* 복수형

  ```python
  browser.find_elements... # 리스트형태로 반환.
  ```

**Wait활용 (implicitly wait VS explicitly wait)**

* implicitly wait란 웹페이지가 넘어올때까지 wait. 그렇다면 time.sleep()과 차이점은?

  * 무작정 기다리는게 아니라 10초를 줬으면 10초안에 페이지 넘어오면 더 기다리지 않고 끝냄.

    ```python
    browser.implicitly_wait(10) <-> time.sleep(10) 차이점 잘 기억.
    ```

* explicitly wait란 웹페이지의 내가 필요한 그! 부분이 표시될때까지 wait.

  * import가 반드시 필요.(위의 explicitly wait사용부분)

    ```python
    WebDriverWait(browser, 10).until(EC.presence_of_element_located((By.CSS_SELECTOR, '#search_btn'))) # '#search_btn'이 10초안에 보이면 더 안기다리고 끝냄.
    ```

**send_keys()를 이용한 키입력 or 마우스 클릭**

* 키 입력(조건 : input같은 태그에 text같은 곳에서 가능)

  * key 입력위해 위에서 import할때 key관련 import 해야함.

  ```python
  browser.find_element_by_id("id").send_keys(Keys.CONTROL, 'v')
  # => Ctrl+v를 입력(붙혀넣기 할때 유용)
  browser.find_element_by_id("id").send_keys('실제 아이디 입력')
  # => 문자열로 기입시 해당 문자열 입력
  ```

* 마우스 클릭

  ```python
  browser.find_element_by_class_name("link_login").click()
  ```


**bs4 활용,,**

* Selenium + BeautifulSoup

  * **html 정보 출력 : browser.page_source**

  ```python
  # 위에서 구한 browser... 사용
  # requests와 차이????? browser.page_source를 사용했다는 점만 다를 뿐!
  soup = BeautifulSoup(browser.page_source, "lxml")
  ```

* find() 사용은 requests에서와 동일.

  ```python
  btn_primary = soup.find("button", attrs={"class":"btn btn-primary"}).text
  ```

**화면 스크롤 다루기**

* 맨 아래 화면으로 이동

  ```python
  browser.execute_script("window.scrollTo(0, document.body.scrollHeight)")
  ```



**종료 방법**

```python
browser.close() # 현재 탭만 종료
browser.quit() # 전체 종료
```



**응용**

* elements형으로 받아온 리스트형에서 '품절'이란 텍스트의 element형 찾기.

  ```python
  # 품절 체크
  search_Label=browser.find_elements_by_tag_name('label')
  i=0
  while(search_Label[i].text !='품절' and i+1<len(search_Label)):
      i += 1
  search_Label[i].click()
  ```

  

### 부가정보) 1. 셀레니움_헤드리스(Selenium_Headless)

**화면이 안 나타난다는 큰 장점.(그만큼 속도가 더 빠름)**

* 네이버 로그인은 headless불가 : element.send_keys(Keys.CONTROL, 'v') 이게 안먹히기 때문.
  => 지금처럼 임의로 접근 할까봐 막아둠. (다른 방법으로 해야함)

#### Selenium_Headless 준비물

```python
그냥 Selenium과 동일.
```

#### Selenium_Headless 사용법

```python
options = webdriver.ChromeOptions() # 크롬 드라이버의 크롬옵션 활용
options.headless = True # 옵션에서 헤드리스 온
options.add_argument("window-size=1920x1080")
options.add_argument("disable-gpu")
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36") # 이부분이 사람처럼 보이게 해주는 부분!!

browser = webdriver.Chrome(options=options) # 기존에서 새로만든 options를 적용!(중요)
browser.maximize_window() # 전체화면(추천)

# => 기본적으로 여기까지는 코드 전부 작성추천
```



### 부가정보) 2. 셀레니움_스크롤(ActionChains)

#### ActionChains

```python
# ActionChains 를 사용하기 위해서.
from selenium.webdriver import ActionChains
# id가 sometag 인 element 를 찾음
some_tag = browser.find_element_by_xpath('//*[@id="seller-content"]/ui-view/div/ui-view[2]/div[1]/div[2]/div[3]/div/div/div/div/div[3]/div[2]/div/div/div[1]/div[5]/span')
# somthing element 까지 스크롤
action = ActionChains(browser)
action.move_to_element(some_tag).perform()
```

#### 사용법

**초기화**

```python
from selenium import webdriver
browser = webdriver.Chrome()
browser.maximize_window()
```

**페이지 이동**

```python
url = "https://play.google.com/store/movies/top"
browser.get(url)
```

**지정한 위치로 스크롤 내리기**

```python
browser.execute_script("window.scrollTo(0, 1080)") # 1920 x 1080
browser.execute_script("window.scrollTo(0, 2080)")
```

**화면 가장 아래로 스크롤 내리기**

```python
browser.execute_script("window.scrollTo(0, document.body.scrollHeight)")
```

**현재 문서 높이를 가져와서 저장**

```python
prev_height = browser.execute_script("return document.body.scrollHeight")
```

**반복 수행**

```python
import time
interval = 2 # 2초에 한번씩 스크롤 내림

# 현재 문서 높이를 가져와서 저장
prev_height = browser.execute_script("return document.body.scrollHeight")

# 반복 수행
while True:
    # 스크롤을 가장 아래로 내림
    browser.execute_script("window.scrollTo(0, document.body.scrollHeight)")

    # 페이지 로딩 대기
    time.sleep(interval)

    # 현재 문서 높이를 가져와서 저장
    curr_height = browser.execute_script("return document.body.scrollHeight")
    if curr_height == prev_height:
        break

    prev_height = curr_height
print("스크롤 완료")
```

**스크린샷**

```python
browser.get_screenshot_as_file("google_movie.png")
```





## 2. 리퀘스트(Requests), BeautifulSoup => 정적

* raise_for_status() : 문제시 에러 후 종료

* status_code : 에러숫자 확인

  * 200대 : 정상 => requests.codes.ok # ok=200
  * 300대 : 웹문제

  * 400대 : 코드문제
  * 500대 : 서버문제

### Requests 준비물

```python
1. pip install requests
2. pip install bs4
```



### Requests 사용법(나중에 합치고 수졍)

**기본 import**

```python
import requests
from bs4 import BeautifulSoup
```

**기본 사용방식**

* Requests + BeautifulSoup

  ```python
  response = requests.get(f"https://jinnyhands.com/product/search.html?banner_action=&keyword={keyword}") # 예시..
  response.raise_for_status() # 문제시 에러 후 종료
  response.encoding=None # 인코딩 None으로 설정.
  html = response.text # 가져온 response내용을 text로 추출
  soup = BeautifulSoup(html, 'html.parser', from_encoding='utf-8') # html파서 사용
  ```

* 다양하게 find() 사용 (기본적으로 find하나는 젤 처음 찾은 값 한개 반환)

  ```python
  div = soup.find('div', attrs={'class':'thumbnail'}) # div 태그의 class속성의 thumbnail속성값에 속한 부분들 추출. 이런식으로 파싱해나감.
  + find_all()도 존재
  + find_next~~등 형제 부모 자식 들도 찾아갈 수 있는 방법 존재.
  => 이런건 나중에 자세히 설명
  ```

**기본적으로 dict형식{}으로 안에 내용들은 str형식으로 구성되었다 생각하면 됨.**

```python
a = div.find('a') # a태그 가져옴.
url = a['href'] # str 형식으로 나옴(dict형식을 key값으로 접근한거임.)
# <a href='~~' ..../> 형식일텐데
# a태그의 href라는 속성이 이녀석이 key역할. 즉, url값은 '~~' 이녀석으로 반환되는것.
```

**파싱에 주로 사용하기 때문에 데이터 가공방법이 많음. 정리는 따로 하겠다.(이부분도 수정하자) 간단히 여기선 사용한것들만 정리하겠다. + 따로 데이터 가공부분으로 해서 정리하는게 나을듯?**

* [-1].. 슬라이스 

  ```python
  datas = soup.find_all('script')[-1] # 마지막 인덱스 접근
  stock = stock[0][index_1:index_2] # index_1~index_2데이터 슬라이스
  ```

* split() : 문자열을 원하는 값 기준으로 데이터 배열로 나눔

  ```python
  stockList = stock.split(',') # 문자열을 ','기준으로 배열로 나눠서 저장
  ```

* find().. 문자열에 사용 (못 찾으면? -1 반환)

  ```python
  index = stockList[19*i+8].find(':') # ':'을 찾아서 해당 index 반환
  ```

* 배열 []가 배열로 안될때가 있는데 list()로 괄호에 감싸면 되는경우도 있음.

* zip() : 여러 리스트를 각각의 첫번째 요소들 부터 시작해서 차례로 나열해 하나의 리스트로 만듬.

  * 예시로 List들 to zip() to DataFrame

    ```python
    one = [~~~] ... 배열들 선언
    last = zip(one,two,three,four,five) # 데이터가공
    df = pd.DataFrame(last)
    # 응용..
    writer = pd.ExcelWriter('buyExcel.xlsx')
    df.to_excel(writer, index = False) # 저장한 df를 xlsx로. 판다스 이용
    ```

* replace() : 원하는 값을 전부 다른값으로 바꿔줌.

  ```python
  search = search.replace('-', '') # '-'을 전부 없애줌.
  ```

  



## 3. Selenium(셀레니움) + Requests 같이 사용한 응용방식

**로그인필요 사이트 파싱할 시 Requests로 세션에 헤더나 쿠키정보 넣는게 매우 복잡한 사이트가 있음.**

* 셀레니움으로 로그인 => 로그인 기록을 Requests에 적용해서 응용하는 방식 사용하자.



### 응용 준비물

**Selenium, Requests 에서 필요했던 준비물들 import 똑같이 하기.**



### 응용 방법

**1. Selenium을 통한 browser 접근 및 로그인까지 하기.**

**2. 로그인 페이지 설정 시작**

* requests의 세션객체 생성

  * session사용 이유는 로그인을 유지하기 위해.(세션이 원래 그런 역할이니까)

    ```python
    s = requests.Session() # requests의 세션객체 생성
    ```

* headers 구성

  * 유저인척 하기위해 'F12' 눌러서 확인할 수 있음.

    ```python
    headers = {
    'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36'
    }
    ```

* cookie 구성

  * 먼저, headers 적용

    ```python
    s.headers.update(headers)
    ```

  * 아까 구한 browser을 이때 이용.

    ```python
    for cookie in browser.get_cookies(): # dict 형식의 cookie 하나씩 가져옴
        c = {cookie['name'] : cookie['value']} # dict 형식으로 c를 저장
        s.cookies.update(c) # 바로바로 적용.
    ```

**3. 저장한 s를 이용해 s.get(url) 형식으로 접근하면 로그인 필요한 페이지 접근 가능.(끝)**



## 4. bs4 (BeautifulSoup) - "lxml파서 사용"

* 위에선 계속 html파서 사용했었음.



### 파서의 종류

* **html파서 : html기반**

  ```python
  soup = BeautifulSoup(html, 'html.parser', from_encoding='utf-8') # html파서 사용
  ```

* **lxml파서 : 마크업기반(속도 더 우수)**

  ```python
  soup = BeautifulSoup(res.text, "lxml") # lxml파서 사용
  ```



**간단한 사용법**

* get_text() : text가져옴.

  ```python
  print(soup.title)
  print(soup.title.get_text())
  ```

* a태그 element 출력(예시)

  ```python
  print(soup.a)
  ```

  * a태그 element 의 속성 정보를 출력

    ```python
    print(soup.a.attrs)
    ```

  * a태그 element 의 href 속성 '값' 정보를 출력

    ```python
    print(soup.a["href"])
    ```

* find함수 : 태그에 속성 이용해서 찾기

  ```python
  # class="Nbtn_upload" 인 a element 를 찾아줘
  soup.find("a", attrs={"class":"Nbtn_upload"})
  # class="Nbtn_upload" 인 어떤 element 를 찾아줘
  soup.find(attrs={"class":"Nbtn_upload"})
  
  webtoon = soup.find("a", text="독립일기-52화 한강 산책")
  print(webtoon)
  ```

* previous, next, parent, siblings : 형제 태그 이전, 이후, 부모 태그, 뒤에 's'를 붙히면 형제들 전부 찾아줌.

  * ```python
    rank1 = soup.find("li", attrs={"class":"rank01"})
    
    rank1.parent
    rank1.next_sibling
    rank2 = rank1.next_sibling.next_sibling
    rank3 = rank2.next_sibling.next_sibling
    rank2 = rank3.previous_sibling.previous_sibling
    ...등등
    
    rank2 = rank1.find_next_sibling("li")
    print(rank2.a.get_text())
    ```



### 1. 웹툰(예시)



**네이버 웹툰 전체 목록 가져오기**

```python
url = "https://comic.naver.com/webtoon/weekday.nhn" # 예시
...생략

# 네이버 웹툰 전체 목록 가져오기
cartoons = soup.find_all("a", attrs={"class":"title"})
# class 속성이 title 인 모든 "a" element 를 반환
for cartoon in cartoons:
    print(cartoon.get_text())
```

**만화 제목 구하기**

```python
url = "https://comic.naver.com/webtoon/list.nhn?titleId=675554"
...생략

cartoons = soup.find_all("td", attrs={"class":"title"})
title = cartoons[0].a.get_text() # 속성가져올땐 대괄호
link = cartoons[0].a["href"]
# print(title)
# print("https://comic.naver.com" + link)

for cartoon in cartoons:
     title = cartoon.a.get_text()
     link = "https://comic.naver.com" + cartoon.a["href"]
     print(title, link)
```

**평점 구하기**

```python
total_rates = 0
cartoons = soup.find_all("div", attrs={"class":"rating_type"})
for cartoon in cartoons:
    rate = cartoon.find("strong").get_text()
    print(rate)
    total_rates += float(rate) # rate가 string 일 것이기 때문에 실수형으로 바꿔줌
print("전체 점수 : ", total_rates)
print("평균 점수 : ", total_rates / len(cartoons))
```



### 2. 쿠팡(예시)



**유저로 우회하기**

```python
url = "https://www.coupang.com/np/search?q=%EB%85%B8%ED%8A%B8%EB%B6%81&channel=user&component=&eventCategory=SRP&trcid=&traid=&sorter=scoreDesc&minPrice=&maxPrice=&priceRange=&filterType=&listSize=36&filter=&isPriceRange=false&brand=&offerCondition=&rating=0&page=1&rocketAll=false&searchIndexingToken=&backgroundColor="

headers = {"User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36"} 

res = requests.get(url, headers=headers)
res.raise_for_status()
soup = BeautifulSoup(res.text, "lxml")
```

**정규식 활용**

```python
items = soup.find_all("li", attrs={"class":re.compile("^search-product")})
```

**데이터 가공**

```python
for item in items:
    # 광고 제품은 제외
    ad_badge = item.find("span", attrs={"class":"ad-badge-text"})
    if ad_badge:
        print("  <광고 상품 제외합니다>")
        continue

    name = item.find("div", attrs={"class":"name"}).get_text() # 제품명
    
    # 애플 제품 제외
    if "Apple" in name:
        print("  <Apple 상품 제외합니다>")
        continue
    
    price = item.find("strong", attrs={"class":"price-value"}).get_text() # 가격
    
    # 리뷰 50개 이상, 평점 4.5 이상 되는 것만 조회

    rate = item.find("em", attrs={"class":"rating"}) # 평점
    if rate:
        rate = rate.get_text()
    else:
        rate = "평점 없음"
        print("  <평점 없는 상품 제외합니다>")
        continue

    rate_cnt = item.find("span", attrs={"class":"rating-total-count"}) # 평점수
    if rate_cnt:
        rate_cnt = rate_cnt.get_text()
        rate_cnt = rate_cnt[1:-1] # 예 : (26) 의 괄호를 뺌
        #print("리뷰 수", rate_cnt)
    else:
        print("  <평점수 없는 상품 제외합니다>")
        continue
        
    if float(rate) >= 4.5 and int(rate_cnt) >= 50:
        # print(name, price, rate, rate_cnt)
        print(f"제품명 : {name}")
        print(f"가격 : {price}")
        print(f"평점 : {rate}점 ({rate_cnt}개)")
        print("바로가기 : {}".format("https://www.coupang.com" + link))
        print("-"*100) # 줄긋기
```

**페이지 추가**

```python
for i in range(1, 6):
    #print("페이지 :", i)
    url = "https://www.coupang.com/np/search?q=%EB%85%B8%ED%8A%B8%EB%B6%81&channel=user&component=&eventCategory=SRP&trcid=&traid=&sorter=scoreDesc&minPrice=&maxPrice=&priceRange=&filterType=&listSize=36&filter=&isPriceRange=false&brand=&offerCondition=&rating=0&page={}&rocketAll=false&searchIndexingToken=&backgroundColor=".format(i)

    res = requests.get(url, headers=headers)
    res.raise_for_status()
    soup = BeautifulSoup(res.text, "lxml")

    items = soup.find_all("li", attrs={"class":re.compile("^search-product")})

    for item in items:
    ... 생략..
```



### 3. 다음 - 영화



**풀코딩**

```python
import requests
from bs4 import BeautifulSoup

for year in range(2015, 2020): #2019까지
    url = "https://search.daum.net/search?w=tot&q={}%EB%85%84%EC%98%81%ED%99%94%EC%88%9C%EC%9C%84&DA=MOR&rtmaxcoll=MOR".format(year)
    res = requests.get(url)
    res.raise_for_status()
    soup = BeautifulSoup(res.text, "lxml")

    images = soup.find_all("img", attrs={"class":"thumb_img"})

    for idx, image in enumerate(images): # idx로 순번,enumerate는 이미지
        print(image["src"])
        image_url = image["src"]
        if image_url.startswith("//"): # //로시작하는거있으면
            image_url = "https:" + image_url # https: 추가!

        print(image_url)
        image_res = requests.get(image_url)
        image_res.raise_for_status()

        with open("movie_{}_{}.jpg".format(year, idx+1), "wb") as f:
            f.write(image_res.content) 
            # with부터 파일저장코드 wb와 content는 이미지?!
        
        if idx >= 4: # 상위 5개 이미지까지만 다운로드
            break
```

