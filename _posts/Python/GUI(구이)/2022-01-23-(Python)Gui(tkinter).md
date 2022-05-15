# GUI(구이)

* tkinter 라이브러리 사용

* 속성들 간단히 정리

  * padx, pady, width, height 의 차이는 크기가 다름(상대, 절대)

  * pack()은 그냥 중앙에 차례로 위치했다면, grid(row, column)을 통해 위치를 지정 가능

    ```
    # side 위치, fill 가득채우기, expand 전체 채우기
    pack(side="top") # 이런식으로 속성넣어서 위치 지정도 가능하다는 점.
    frame_burger.pack(side="left", fill="both", expand=True)
    ```

  * +로 columnspan등은 grid에서 옆에 열과 합침.

  * root.quit로 간단히 종료



## 1. create_frame



#### 사용법

**기본 import**

```
from tkinter import *
```

**객체 생성**

```
root = Tk() # 이것만 입력시 빈 창 실행
```

**기본 속성**

* title : 제목변경

  ```
  root.title("Nado GUI") # 제목 변경
  ```

* geometry, resizable(boolean,boolean) : 크키 조절, 크키 변경 불가 설정

  ```
  root.geometry("640x480") # 가로 * 세로 크키조절
  # root.geometry("640x480+300+100") # 가로 * 세로 + x + y
  
  root.resizable(False, False) # x(너비), y(높이) 값 변경 불가 (창 크기 변경 불가)
  ```

* mainloop : 이벤트 메시지 루프로서 키보드나 마우스 혹은 화면 Redraw와 같은 다양한 이벤트로부터 오는 메시지를 받고 전달하는 역활을 한다.

  ```
  root.mainloop() # 마지막에 작성해주자.
  ```



## 2. button(버튼)



**기본방식**

```
btn1 = Button(root, text="버튼1")
btn1.pack() # 그냥 중앙에 적용해줌.
```

**다양한 속성 적용**

* padx, pady, width, height

  ```
  btn2 = Button(root, padx=5, pady=10, text="버튼2")
  btn2.pack()
  
  btn3 = Button(root, padx=10, pady=5, text="버튼3")
  btn3.pack()
  
  btn4 = Button(root, width=10, height=3, text="버튼4")
  btn4.pack()
  ```

* fg, bg : 글자, 배경 색상변경

  ```
  btn5 = Button(root, fg="red", bg="yellow", text="버튼5") # fg:글자색, bg:배경색
  btn5.pack()
  ```

* PhotoImage : 이미지 적용

  ```
  photo = PhotoImage(file="gui_All/gui_basic/img.png")
  btn6 = Button(root, image=photo)
  btn6.pack()
  ```

* command : 함수 적용

  ```
  def btncmd(): # 버튼 클릭시 함수 실행을 위한 선언
      print("버튼이 클릭되었어요")
  
  btn7 = Button(root, text="동작하는 버튼", command=btncmd)
  btn7.pack()
  ```

  

## 3. label(라벨)



**기본방식**

```
label1 = Label(root, text="안녕하세요")
label1.pack()
```

**다양한 속성적용**

* 이미지, 함수 적용

  ```
  photo = PhotoImage(file="gui_All/gui_basic/img.png")
  label2 = Label(root, image=photo)
  label2.pack()
  
  def change():
      label1.config(text="또 만나요")
      global photo2 # 전역 해줘야 정상 출력
      photo2 = PhotoImage(file="gui_All/gui_basic/img2.png")
      label2.config(image=photo2)
  
  btn = Button(root, text="클릭", command=change)
  btn.pack()
  ```

  

## 4. text, entry

* text는 여러줄 입력시 사용, entry는 한줄만 입력시 사용 추천.

**기본방식**

```
# TEXT는 여러줄 입력시 쓰고
txt = Text(root, width=30, height=5)
txt.pack()
txt.insert(END, "글자를 입력하세요") # 기본값
# Entry는 한줄만 입력할거면 쓰자
e = Entry(root, width=30)
e.pack()
e.insert(0, "한 줄만 입력해요") # 현재는 값이 비어있으므로 END를 써도 동일
```

**함수 이용한 텍스트 내용 출력 예시**

```
def btncmd():
    # 내용 출력
    print(txt.get("1.0", END)) # 라인 1부터 0컬럼 부터 END까지 가져와라
    print(e.get())
    
    # 내용 삭제
    txt.delete("1.0", END)
    e.delete(0, END)

btn = Button(root, text="클릭", command=btncmd)
btn.pack()
```



## 5. listbox



**기본 구성**

```
# single과 extended의 차이는 한개 클릭 또는 여러개 클릭 가능
# height를 0으로 하면 전부 다보여줌. 1이면? 한줄만 보여주는것.
listbox = Listbox(root, selectmode="extended", height=0)
listbox.insert(0, "사과")
listbox.insert(1, "딸기")
listbox.insert(2, "바나나")
listbox.insert(END, "수박") # 데이터있는 마지막줄 다음부터 insert한다는 의미
listbox.insert(END, "포도")
listbox.pack()
```

**함수이용한 예시**

```
def btncmd():
    # 삭제
    # listbox.delete(END) # 맨 뒤에 항목을 삭제
    # listbox.delete(0) # 맨 앞에 항목을 삭제

    # 갯수 확인
    # print("리스트에는", listbox.size(), "개가 있어요")

    # 항목 확인 (시작 idx, 끝 idx)
    # print("1번째부터 3번째까지의 항목 : ", listbox.get(0,2))

    # 선택된 항목 확인 (위치로 반환 (ex) (1, 2, 3))
    print("선택된 항목 : ", listbox.curselection()) # index값 반환

btn = Button(root, text="클릭", command=btncmd)
btn.pack()
```



## 6. checkbox

* IntVar(), StringVar() ,,, 존재
* variable을 name이라고 생각하면 이해 간단. (html과 비교해서)

**예시**

```
chkvar = IntVar() # chkvar 에 int 형으로 값을 저장한다
chkbox = Checkbutton(root, text="오늘 하루 보지 않기", variable=chkvar)
# chkbox.select() # 자동 선택 처리
# chkbox.deselect() # 선택 해제 처리
chkbox.pack()

chkvar2 = IntVar()
chkbox2 = Checkbutton(root, text="일주일동안 보지 않기", variable=chkvar2)
chkbox2.pack()
```

```
def btncmd():
    print(chkvar.get()) # 0 : 체크 해제, 1 : 체크 => int형으로 나옴.
    print(chkvar2.get())

btn = Button(root, text="클릭", command=btncmd)
btn.pack()
```



## 7. radiobutton

* checkbox와 다르게 한개만 선택하는것이 radiobutton이다.
  * 속성 variable에 동일한 변수 넣은곳이 radiobutton들의 한 세트로 구성된다. (즉, 같은name)

**예시**

```
Label(root, text="메뉴를 선택하세요").pack()

burger_var = IntVar() # 여기에 int 형으로 값을 저장한다.
btn_burger1 = Radiobutton(root, text="햄버거", value=1, variable=burger_var)
btn_burger1.select() # 기본값 선택
btn_burger2 = Radiobutton(root, text="치즈버거", value=2, variable=burger_var)
btn_burger3 = Radiobutton(root, text="치킨버거", value=3, variable=burger_var)

btn_burger1.pack()
btn_burger2.pack()
btn_burger3.pack()

Label(root, text="음료를 선택하세요").pack()

drink_var = StringVar()
btn_drink1 = Radiobutton(root, text="콜라", value="콜라", variable=drink_var)
btn_drink1.select() # 기본값 선택
btn_drink2 = Radiobutton(root, text="사이다", value="사이다", variable=drink_var)

btn_drink1.pack()
btn_drink2.pack()
```

```
def btncmd():
    print(burger_var.get()) # 햄버거 중 선택된 라디오 항목의 값(value)을 출력
    print(drink_var.get()) # 음료 중 선택된 값을 출력

btn = Button(root, text="클릭", command=btncmd)
btn.pack()
```



## 8. combobox

```
import tkinter.ttk as ttk # 추가로 필요
```

**예시**

```
values = [str(i) + "일" for i in range(1, 32)] # 1 ~ 31 까지의 숫자
combobox = ttk.Combobox(root, height=5, values=values)
combobox.pack()
combobox.set("카드 결제일") # 최초 목록 제목 설정

readonly_combobox = ttk.Combobox(root, height=10, values=values, state="readonly")
readonly_combobox.current(0) # 0번째 인덱스 값 선택
readonly_combobox.pack()
```

```
def btncmd():
    print(combobox.get()) # 선택된 값 표시
    print(readonly_combobox.get())

btn = Button(root, text="선택", command=btncmd)
btn.pack()
```



## 9. progressbar



**기본사용**

* mode를 indeterminate 사용 => 차오르는 바가 아니라 좌우로 왕복하는 초록바가 움직임.

  ```
  progressbar = ttk.Progressbar(root, maximum=100, mode="indeterminate")
  progressbar.start(10) # 10 ms 마다 움직임
  progressbar.pack()
  ```

* mode를 determinate 사용 => 처음부터 끝까지 초록바가 차오르는 움직임.

  ```
  progressbar = ttk.Progressbar(root, maximum=100, mode="determinate")
  progressbar.start(10) # 10 ms 마다 움직임
  progressbar.pack()
  ```

* progressbar.stop() : 작동 중지

**사용자가 원하는데로 로딩하는 방식**

* **variable 속성을 이용**

  ```
  p_var2 = DoubleVar() # 실수형태(92.4% 등등)
  progressbar2 = ttk.Progressbar(root, maximum=100, length=150, variable=p_var2)
  progressbar2.pack()
  ```

* **로딩을 보이기 위한 함수 작성**

  ```
  def btncmd2():
      for i in range(1, 101): # 1 ~ 100
          time.sleep(0.01) # 0.01 초 대기
  
          p_var2.set(i) # progress bar 의 값 설정
          progressbar2.update() # ui 업데이트
          print(p_var2.get())
  
  btn = Button(root, text="시작", command=btncmd2)
  btn.pack()
  ```



## 10. menu(우리가 아는 메뉴탭)



#### 객체생성

```
menu = Menu(root)
```

**File 메뉴**

```
menu_file = Menu(menu, tearoff=0)
menu_file.add_command(label="New File", command=create_new_file) # 함수는 있다고 가정
menu_file.add_command(label="New Window")
menu_file.add_separator() # 구분선
menu_file.add_command(label="Open File...")
menu_file.add_separator()
menu_file.add_command(label="Save All", state="disable") # 비활성화
menu_file.add_separator()
menu_file.add_command(label="Exit", command=root.quit)

menu.add_cascade(label="File", menu=menu_file)
```

**Edit 메뉴 (빈 값)**

```
menu.add_cascade(label="Edit")
```

**Language 메뉴 추가 (radio 버튼을 통해서 택1)**

```
menu_lang = Menu(menu, tearoff=0)
menu_lang.add_radiobutton(label="Python")
menu_lang.add_radiobutton(label="Java")
menu_lang.add_radiobutton(label="C++")
menu.add_cascade(label="Language", menu=menu_lang)
```

**View 메뉴**

```
menu_view = Menu(menu, tearoff=0)
menu_view.add_checkbutton(label="Show Minimap")
menu.add_cascade(label="View", menu=menu_view)
```

#### 적용

```
root.config(menu=menu)
```



## 11. messagebox(메세지박스)

```
import tkinter.messagebox as msgbox # 추가로 필요
```

**예시**

```
# 기차 예매 시스템이라고 가정
def info():
    msgbox.showinfo("알림", "정상적으로 예매 완료되었습니다.")

def warn():
    msgbox.showwarning("경고", "해당 좌석은 매진되었습니다.")

def error():
    msgbox.showerror("에러", "결제 오류가 발생했습니다.")

def okcancel():
    msgbox.askokcancel("확인 / 취소", "해당 좌석은 유아동반석입니다. 예매하시겠습니까?")

def retrycancel():
    response = msgbox.askretrycancel("재시도 / 취소", "일시적인 오류입니다. 다시 시도하시겠습니까?")
    if response == 1: # 재시도
        print("재시도")
    elif response == 0: # 취소
        print("취소")

def yesno():
    msgbox.askyesno("예 / 아니오", "해당 좌석은 역방향입니다. 예매하시겠습니까?")

def yesnocancel():
    response = msgbox.askyesnocancel(title=None, message="예매 내역이 저장되지 않았습니다. \n저장 후 프로그램을 종료하시겠습니까?")
    # 네 : 저장 후 종료
    # 아니오 : 저장 하지 않고 종료
    # 취소 : 프로그램 종료 취소 (현재 화면에서 계속 작업)
    print("응답:", response) # True=1, False=0, None=그외
    if response == 1:
        print("예")
    elif response == 0:
        print("아니오")
    else:
        print("취소")

Button(root, command=info, text="알림").pack()
Button(root, command=warn, text="경고").pack()
Button(root, command=error, text="에러").pack()
Button(root, command=okcancel, text="확인 취소").pack()
Button(root, command=retrycancel, text="재시도 취소").pack()
Button(root, command=yesno, text="예 아니오").pack()
Button(root, command=yesnocancel, text="예 아니오 취소").pack()
```



## 12. frame(레이아웃같은 느낌이라 생각)



**예시**

```
Label(root, text="메뉴를 선택해 주세요").pack(side="top")
Button(root, text="주문하기").pack(side="bottom")

# 메뉴 프레임
frame_burger = Frame(root, relief="solid", bd=1) # bd는 외곽선
frame_burger.pack(side="left", fill="both", expand=True) # side 위치, fill 가득채우기, expand 전체 채우기
Button(frame_burger, text="햄버거").pack()
Button(frame_burger, text="치즈햄버거").pack()
Button(frame_burger, text="치킨햄버거").pack()

# 음료 프레임
frame_drink = LabelFrame(root, text="음료")
frame_drink.pack(side="right", fill="both", expand=True)
Button(frame_drink, text="콜라").pack()
Button(frame_drink, text="사이다").pack()
```



## 13. scrollbar(스크롤바)

* scrollbar를 속성으로 적용 해주는 것임. 직접 위치도 지정해야함.

**예시**

```
# 위젯에 스크롤바 넣기위해서는 frame이 필요
frame = Frame(root)
frame.pack()

scrollbar = Scrollbar(frame)
scrollbar.pack(side="right", fill="y")

# set 이 없으면 스크롤을 내려도 다시 올라옴.
listbox = Listbox(frame, selectmode="extended", height=10, yscrollcommand = scrollbar.set)

for i in range(1, 32): # 1~ 31일
    listbox.insert(END, str(i) + "일") # 1일, 2일...
listbox.pack(side="left")

scrollbar.config(command=listbox.yview)
```



## 14. grid(그리드)

* 그리드는 계산기 생각하면 간단
* 그리드 형식으로 row, column을 지정해주는 특징

**예시(계산기 형태)**

```
# 맨 윗줄
btn_f16=Button(root, text="F16", width=5, height=2) 
btn_f17=Button(root, text="F17", width=5, height=2)
btn_f18=Button(root, text="F18", width=5, height=2)
btn_f19=Button(root, text="F19", width=5, height=2)

btn_f16.grid(row=0, column=0, sticky=N+E+W+S, padx=3, pady=3) # 지정한 방향으로 위젯을 늘려라
btn_f17.grid(row=0, column=1, sticky=N+E+W+S, padx=3, pady=3)
btn_f18.grid(row=0, column=2, sticky=N+E+W+S, padx=3, pady=3)
btn_f19.grid(row=0, column=3, sticky=N+E+W+S, padx=3, pady=3)

# clear 줄
btn_clear=Button(root, text="clear", width=5, height=2)
btn_equal=Button(root, text="=", width=5, height=2)
btn_div=Button(root, text="/", width=5, height=2)
btn_mul=Button(root, text="*", width=5, height=2)

btn_clear.grid(row=1, column=0, sticky=N+E+W+S, padx=3, pady=3)
btn_equal.grid(row=1, column=1, sticky=N+E+W+S, padx=3, pady=3)
btn_div.grid(row=1, column=2, sticky=N+E+W+S, padx=3, pady=3)
btn_mul.grid(row=1, column=3, sticky=N+E+W+S, padx=3, pady=3)

# 7 시작 줄
btn_7=Button(root, text="7", width=5, height=2)
btn_8=Button(root, text="8", width=5, height=2)
btn_9=Button(root, text="9", width=5, height=2)
btn_sub=Button(root, text="-", width=5, height=2)

btn_7.grid(row=2, column=0, sticky=N+E+W+S, padx=3, pady=3)
btn_8.grid(row=2, column=1, sticky=N+E+W+S, padx=3, pady=3)
btn_9.grid(row=2, column=2, sticky=N+E+W+S, padx=3, pady=3)
btn_sub.grid(row=2, column=3, sticky=N+E+W+S, padx=3, pady=3)

# 4 시작 줄
btn_4=Button(root, text="4", width=5, height=2)
btn_5=Button(root, text="5", width=5, height=2)
btn_6=Button(root, text="6", width=5, height=2)
btn_add=Button(root, text="+", width=5, height=2)

btn_4.grid(row=3, column=0, sticky=N+E+W+S, padx=3, pady=3)
btn_5.grid(row=3, column=1, sticky=N+E+W+S, padx=3, pady=3)
btn_6.grid(row=3, column=2, sticky=N+E+W+S, padx=3, pady=3)
btn_add.grid(row=3, column=3, sticky=N+E+W+S, padx=3, pady=3)

# 1 시작 줄
btn_1=Button(root, text="1", width=5, height=2)
btn_2=Button(root, text="2", width=5, height=2)
btn_3=Button(root, text="3", width=5, height=2)
btn_enter=Button(root, text="enter", width=5, height=2) # 세로로 합쳐짐

btn_1.grid(row=4, column=0, sticky=N+E+W+S, padx=3, pady=3)
btn_2.grid(row=4, column=1, sticky=N+E+W+S, padx=3, pady=3)
btn_3.grid(row=4, column=2, sticky=N+E+W+S, padx=3, pady=3)
btn_enter.grid(row=4, column=3, rowspan=2, sticky=N+E+W+S, padx=3, pady=3) # row 2개 합침

# 0 시작 줄
btn_0=Button(root, text="0", width=5, height=2)
btn_point=Button(root, text=".", width=5, height=2)

btn_0.grid(row=5, column=0, columnspan=2, sticky=N+E+W+S, padx=3, pady=3) # column 2개 합침
btn_point.grid(row=5, column=2, sticky=N+E+W+S, padx=3, pady=3)
```



## 15. 간단한 메모장 만들기

* 참고로 파일 탭만 구현

```
import os
from tkinter import *

root = Tk()
root.title("Window 메모장")
root.geometry("640x480")

txtname = "mynote.txt"

def open_txt():
    if os.path.isfile(txtname):
        with open(txtname, "r", encoding="utf8") as file:
            txt.delete("1.0", END)
            txt.insert(END, file.read())

def close_txt():
    with open(txtname, "w", encoding="utf8") as file:
        file.write(txt.get("1.0", END))

menu = Menu(root)

# 파일
menu_file = Menu(menu, tearoff=0)
menu_file.add_command(label="열기", command=open_txt)
menu_file.add_command(label="저장", command=close_txt)
menu_file.add_separator()
menu_file.add_command(label="끝내기", command=root.quit)
menu.add_cascade(label="파일", menu=menu_file)
menu.add_cascade(label="편집")
menu.add_cascade(label="서식")
menu.add_cascade(label="보기")
menu.add_cascade(label="도움말")

# 스크롤 바
scrollbar = Scrollbar(root)
scrollbar.pack(side="right", fill="y")

# 본문 영역
txt = Text(root, yscrollcommand=scrollbar.set)
txt.pack(side="left", fill="both", expand=True)
scrollbar.config(command=txt.yview)

root.config(menu=menu)
root.mainloop()
```



# GUI를 활용한 간단한 프로젝트

* 따로 따로 단계별로 보는것이다.

## 1. create_layout

```
import tkinter.ttk as ttk
from tkinter import *

root = Tk()
root.title("Nado GUI")

# 파일 프레임
file_frame = Frame(root)
file_frame.pack(fill="x", padx=5, pady=5) # , padx=5, pady=5 각 간격 띄우기

btn_add_file = Button(file_frame, padx=5, pady=5, width=12, text="파일추가")
btn_add_file.pack(side="left")

btn_del_file = Button(file_frame, padx=5, pady=5, width=12, text="선택삭제")
btn_del_file.pack(side="right")

# 리스트 프레임
list_frame = Frame(root)
list_frame.pack(fill="both", padx=5, pady=5)

scrollbar = Scrollbar(list_frame)
scrollbar.pack(side="right", fill="y")

list_file = Listbox(list_frame, selectmode="extended", height=15,yscrollcommand=scrollbar.set)
list_file.pack(side="left", fill="both", expand=True)
scrollbar.config(command=list_file.yview)

# 저장 경로 프레임
path_frame = LabelFrame(root, text="저장경로")
path_frame.pack(fill="x", padx=5, pady=5,ipady=5)

txt_dest_path = Entry(path_frame)
txt_dest_path.pack(side="left", fill="x", expand=True, padx=5, pady=5, ipady=4) # 높이 변경 (i=이너)

btn_dest_path = Button(path_frame, text="찾아보기", width=10)
btn_dest_path.pack(side="right", padx=5, pady=5)

# 옵션 프레임
option_frame = LabelFrame(root, text="옵션")
option_frame.pack(padx=5, pady=5,ipady=5)

# 1. 가로 넓이 옵션
# 가로 넓이 레이블
lbl_width = Label(option_frame, text="가로넓이", width=8)
lbl_width.pack(side="left", padx=5, pady=5)

# 가로 넓이 콤보
opt_width = ["원본유지", "1024", "800", "640"]
cmb_width = ttk.Combobox(option_frame, state="readonly", values=opt_width, width=10)
cmb_width.current(0)
cmb_width.pack(side="left", padx=5, pady=5)

# 2. 간격 옵션
# 간격 옵션 레이블
lbl_space = Label(option_frame, text="간격", width=8)
lbl_space.pack(side="left", padx=5, pady=5)
# 간격 옵션 콤보
opt_space = ["없음", "좁게", "보통", "넓게"]
cmb_space = ttk.Combobox(option_frame, state="readonly", values=opt_space, width=10)
cmb_space.current(0)
cmb_space.pack(side="left", padx=5, pady=5)

# 3. 파일 포맷 옵션
# 파일 포맷 레이블
lbl_format = Label(option_frame, text="포맷", width=8)
lbl_format.pack(side="left", padx=5, pady=5)
# 파일 포맷 옵션 콤보
opt_format = ["PNG", "JPG", "BMP"]
cmb_format = ttk.Combobox(option_frame, state="readonly", values=opt_format, width=10)
cmb_format.current(0)
cmb_format.pack(side="left", padx=5, pady=5)

# 진행 상황 Progress Bar
progress_frame = LabelFrame(root, text="진행상황")
progress_frame.pack(fill="x", padx=5, pady=5,ipady=5)

p_var = DoubleVar()
progress_bar = ttk.Progressbar(progress_frame, maximum=100, variable=p_var)
progress_bar.pack(fill="x", padx=5, pady=5)

# 실행 프레임
run_frame = Frame(root)
run_frame.pack(fill="x", padx=5, pady=5)

btn_close = Button(run_frame, padx=5, pady=5, text="닫기", width=12, command=root.quit)
btn_close.pack(side="right", padx=5, pady=5)

btn_start = Button(run_frame, padx=5, pady=5, text="시작", width=12)
btn_start.pack(side="right", padx=5, pady=5)

root.resizable(False, False) # 창 크기 고정
root.mainloop()
```



## 2. basic_function

```
import tkinter.ttk as ttk
import tkinter.messagebox as msgbox
from tkinter import * # __all__
from tkinter import filedialog # 서브모델이라 별도로 import해줘야 함

root = Tk()
root.title("Nado GUI")

# 파일 추가
def add_file():
    files = filedialog.askopenfilenames(title="이미지 파일을 선택하세요", \
        filetypes=(("PNG 파일", "*.png"), ("모든 파일", "*.*")), \
# initialdir="C:/") # 튜플이용 및 최초의 C:/ 경로를 보여줌
# 파일경로 앞에 r을 붙이면 경로에 \\두번씩 입력할필요 없음
        initialdir=r"C:\Users\Ko\Desktop\PythonWorkspace\gui_basic")

    # 사용자가 선택한 파일 목록
    for file in files:
        list_file.insert(END, file)

# 선택 삭제
def del_file():
    #print(list_file.curselection())
    #list삭제시 reversed의 중요성은 기억!
    for index in reversed(list_file.curselection()):
        list_file.delete(index)

# 저장 경로
def browse_dest_path():
    folder_selected = filedialog.askdirectory()
    if folder_selected == "": # 사용자가 취소를 누를 때
        return
    # print(folder_selected)
    txt_dest_path.delete(0, END)
    txt_dest_path.insert(0, folder_selected)

# 시작
def start():
    # 각 옵션들 값을 확인
    print("가로넓이 : ", cmb_width.get())
    print("간격 : ", cmb_space.get())
    print("포맷 : ", cmb_format.get())
    
    # 파일 목록 확인
    if list_file.size() == 0:
        msgbox.showwarning("경고", "이미지 파일을 추가하세요")
        return
    
    # 저장 경로 확인
    if len(txt_dest_path.get()) == 0:
        msgbox.showwarning("경고", "저장 경로를 선택하세요")
        return
```



## 3. merge_images

```
# 이미지 통합
def merge_image():
    # print(list_file.get(0, END))
    images = [Image.open(x) for x in list_file.get(0, END)]
    # size -> size[0] : width, size[1] : height
    # widths = [x.size[0] for x in images]
    # heights = [x.size[1] for x in images]

    # [(10, 10), (20, 20), (30, 30)]
    widths, heights = zip(*(x.size for x in images))

    # 최대 넓이, 전체 높이 구해옴
    max_width, total_height = max(widths), sum(heights)
    
    # 스케치북 준비
    result_img = Image.new("RGB", (max_width, total_height), (255, 255, 255)) # 배경 흰색
    y_offset = 0 # y 위치
    # for img in images:
    #     result_img.paste(img, (0, y_offset)) # 붙여넣기
    #     y_offset += img.size[1] # height 값 만큼 더해줌

    for idx, img in enumerate(images):
        result_img.paste(img, (0, y_offset))
        y_offset += img.size[1]

        progress = (idx + 1) / len(images) * 100 # 실제 percent 정보를 계산, index가 0부터 시작하니까 +1
        p_var.set(progress)
        progress_bar.update()

    dest_path = os.path.join(txt_dest_path.get(), "nado_photo.jpg")
    result_img.save(dest_path)
    msgbox.showinfo("알림", "작업이 완료되었습니다.")
```



## 4. apply_options

```
# 이미지 통합
def merge_image():
    # print("간격 : ", cmb_space.get())
    # print("포맷 : ", cmb_format.get())

    try:
        # 가로넓이
        img_width = cmb_width.get()
        if img_width == "원본유지":
            img_width = -1 # -1 일때는 원본 기준으로
        else:
            img_width = int(img_width)

        # 간격
        img_space = cmb_space.get()
        if img_space == "좁게":
            img_space = 30
        elif img_space == "보통":
            img_space = 60
        elif img_space == "넓게":
            img_space = 90
        else: # 없음
            img_space = 0

        # 포맷
        img_format = cmb_format.get().lower() # lower은 소문자

        #####################################

        images = [Image.open(x) for x in list_file.get(0, END)]

        # 이미지 사이즈 리스트에 넣어서 하나씩 처리
        image_sizes = [] # [(width1, height1), (width2, height2), ...]
        if img_width > -1:
            # wiidth 값 변경
            image_sizes = [(int(img_width), int(img_width * x.size[1] / x.size[0])) for x in images]
        else:
            # 원본 사이즈 사용
            image_sizes = [(x.size[0], x.size[1]) for x in images]

        # 계산식
        # 100 * 60 이미지가 있음. -> width 를 80으로 줄이면 height?
        # (원본 width) : (원본 height) = (변경 width) : (변경 height)
        #      100    :       60      =       80     :      ?
        #       x     :        y      =        x'    :      y'
        #       xy' = x'y
        #       y'  = x'y / x -> 이 식을 적용
        
        # x = width = size[0]
        # y = height = size[1]
        # x' = img_width
        # y' = img_width * size[1] / size[0]

        widths, heights = zip(*(image_sizes))

        # 최대 넓이, 전체 높이 구해옴
        max_width, total_height = max(widths), sum(heights)
        
        # 스케치북 준비
        if img_space > 0: # 이미기 간격 옵션 적용
            total_height += (img_space * (len(images) - 1))
        result_img = Image.new("RGB", (max_width, total_height), (255, 255, 255)) # 배경 흰색
        y_offset = 0 # y 위치

        for idx, img in enumerate(images):
            # width 가 원본유지가 아닐 때에는 이미지 크기 조정
            if img_width:
                img = img.resize(image_sizes[idx])

            result_img.paste(img, (0, y_offset))
            y_offset += (img.size[1] + img_space) # height 값 + 사용자가 지정한 간격

            progress = (idx + 1) / len(images) * 100 # 실제 percent 정보를 계산, index가 0부터 시작하니까 +1
            p_var.set(progress)
            progress_bar.update()

        # 포맷 옵션 처리
        file_name = "nado_photo." + img_format
        dest_path = os.path.join(txt_dest_path.get(), file_name)
        result_img.save(dest_path)
        msgbox.showinfo("알림", "작업이 완료되었습니다.")
    except Exception as err: # 예외처리
        msgbox.showerror("에러", err)
```



**풀 소스 보기 :**

