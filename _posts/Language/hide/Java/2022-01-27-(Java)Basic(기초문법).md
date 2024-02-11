---
title:  "[Java]Basic(기초 문법)"
categories : [Java]
tag : [Java,Java 기초정리]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: false
---



## Intro

**전공 수업 자료 사용!**

<br><br>

## 1. 객관식

**< 1장 확인문제 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image002.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image004.gif" />

**JVM은 main을 먼저찾지만, 괄호가 비었으면 스킵**

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image006.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image008.gif" />

<br>

**< 2장 확인문제 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image010.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image012.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image014.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image016.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image018.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image020.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image022.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image024.gif" />

**큰 따음표 두개 붙는건 가능.(문자열때 알려줌)**

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image026.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image028.gif" />

 <br>

**< 3장 확인문제 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image030.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image032.gif" />

**참조타입 = 레퍼런스타입**

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image034.gif" />

double의 경우 0, float의 경우 끝에F 

**< 반복문 배열 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image036.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image038.gif" />

이건 따로 풀어보기 - 3번에 다

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image040.gif" />

길이는 오른쪽에 들어있어야 한다.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image042.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image044.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image046.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image048.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image050.gif" />

sum은 for문 안에서 정의 됬기 때문에 빼줘야 함! for위에 바로 선언

for문에 sum과 세미콜론 빼줘야 하고, int sum = 0;으로 초기화

세미콜론 빼고, 한줄로 나오기 위해 ln빼기

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image052.gif" />

16번에 다른 것들은 왼쪽 오른쪽에 뭐 있는데 라는 X

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image054.gif" />

20번에 라는 오른쪽은 double이므로 더 크므로 오류

**< 예외처리 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image056.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image058.gif" />

2번은 다 출력x, 나 예외처리 뜸

print에 print를 사용한다? 오류! printStackTrace()는 바로 출력 해준다.(print효과)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image060.gif" />

체크 예외는 예외처리 안하면 컴파일 통과 못함!

가,나,다는 사용자 편의를 위해 해줌

**try와 catch는 항상 붙어 다닌다. catch가 없으므로 => 컴파일 오류**

**< 4장 확인문제 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image062.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image064.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image066.gif" />

1번 필드는 어디서든 사용 가능

2번 인스턴스 필드 초기화란 non-static 필드 이다.

3번 순서상관 없지만 개발자들 편의를 위해!!!

6번 맞음! 암기!

7번은 컴파일러가 바이트코드 파일에 추가 해준다!!

8번은 class 필드 생성자 메소드에 4가지 붙을 수있는데 class빼곤 전부 4가지 다 가능~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image068.gif" />

답찾기

=> static 블록이란게 있구나 ,, 이것을 코딩하라고 시험은 안냄!!

 

++++ 7번 빠짐!!(확인 바람)

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image070.gif" />

=> 8-2는 왜?? 다시 공부,, -> 아마도 final 필드가 안될것같음

/+ 7번 문제도 풀기

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image072.gif" />

=> 7번은 해당 패키지 내부! 클래스 내부는 private

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image074.gif" />

 <br>

**< 8장 확인문제 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image076.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image078.gif" />

/2) 중요!! 여기서는 리얼 값을 리턴 한거니까!! /하나!!!!!!! 4) 랑 비교해보면 됨

 

**< 중간고사 대체 과제 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image080.gif" />

< 5장 확인문제는 주관식에서 같이보기 >

< 자바 클래스/객체 문제풀기 는 주관식에서 같이보기 >

 <br><br>

## 2. 주관식

**< 파일입출력 >**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image002-16464640727422.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image004-16464640727421.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image006-16464640727423.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image008-16464640727424.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image010.jpg"/> 

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image012-16464640727425.gif" />

여기서 또한 우리가 직접 프로그램에 입력(리얼!!)하는거니까 문자열 리터럴 아니기 때문에 / 하나 !!!!!!

숨겨진 파일들도 있으니까 프로그램과 눈으로 본 결과 다를 수 있음

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image014.jpg"/> 

 <br>

**< 상속 > - 5장**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image016-16464640727426.gif" />

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image018-16464640727427.gif" />

=> 내용 업그레이드 시키기위한 오버라이딩 예입니다. Math.PI는 좀더 정확한 수가 있습니다.!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image020-16464640727438.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image022-16464640727439.gif" />

=> ccc, ddd는 자기가 직접 코딩한거라서 안됨~!

=> animal은 추상클래스 이기 때문에 new할수 없다! 자식을 통해서

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image024-164646407274310.gif" />

=> 상속은 업그레이드하는 역할과, 중복을 없애는 일에 많이 사용!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image026-164646407274311.gif" />

=> 위 클래스의 개념적인 부분에 추상클래스!! new는 자식을 통해!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image028-164646407274312.gif" />

=> 이거 저거 상속 받으시오,,, 인터페이스로 구현

=> 다중 상속이 가능한것이 가장 큰 차이점. 

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image030-164646407274313.gif" />

=> 초기값이 없기때문에 에러가 났습니다.( 아무것도 안적으면 자동으로 public static final이(상수) 붙을거니까!!)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image032-164646407274314.gif" />

=> extends, implements

=> 클래스 밑에 인터페이스 이런건 아예 존재하지 않습니다.

인터페이스 자체가 남한테 일 시킬려고 구현되는 거기 때문에.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image034-164646407274315.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image036-164646407274316.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image038-164646407274317.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image040-164646407274318.gif" />

=> 그럼 cc가 가르키던 배열 객체는 GC가 들고가겠죠!? 

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image042-164646407274321.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image044-164646407274319.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image046-164646407274322.gif" />

=> 총 4개의 생성자가 있다. (main에서 자식 불렀다.)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image048-164646407274320.gif" />

**=> 이걸 이해하면 완벽히 이해한 것!**

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image050-164646407274323.gif" />

=> 뭔가가 넣어서 호출한다면??? 4번이 먼저 시작하겠죠?

=> 무조건 super();입니다! 코딩안하면 무조건! 햇갈리지 마요!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image052-164646407274324.gif" />

1번 단일 상속이다.

2번 오버라이딩!! 맞는말

3번 private는 자신의 클래스에서만 가능!!

4번 맞는말!! final 메소드 의 경우 상속은 하지만 리모델링 하지마라

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image054-164646407274325.gif" />

1번에서 추가설명 하자면 형변환 연산자는 적어도 되는데 굳이 적어주지 않아도 된다! (클래스 업 = 프로모션)

2번 클래스 다운이고 2가지 조건이 있기때문에 항상은 틀린말!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image056-164646407274326.gif" />

=> 4번은 맞는말~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image058-164646407274327.gif" />

=> 2번 자식이 더 좁게는 할 수 없다!

++++++++ 4번문제가 빠짐 확인 고고 (5장 확인문제에서 확인)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image060-164646407274328.gif" />

=> 단군할아버지 급 클래스

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image062-164646407274329.gif" />

=> Tire ti가 sn을 참조하니까 좀 더 수정된? 메소드인 자식꺼 출력인것 같음.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image064-164646407274330.gif" />

=> super.run();을 넣었을 경우의 출력 예시~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image066-164646407274331.gif" />

=> 7번의 경우는 형변환 될 수 없습니다.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image068-164646407274332.gif" />

=> 1번 상위, 하위 클래스 이야기는 오버 라이딩~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image070-164646407274333.gif" />

1번 자식 클래스가 new 될 때 추상클래스 또한 실행 되니까 코딩은 똑같아요~!

2번은 추상클래스도 포함~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image072-164646407274334.gif" />

=> 2번 필드는 똑같은 이름이 존재 할 수 있는데, 부모접근할 때 자식 접근할 때 다르죠 그러나 메소드 오버라이딩의 경우 둘다 똑같죠(원래꺼 접근 하고 싶으면 super. 붙여야 하는 거고~)

3번은 상속인데 하나의 클래스? 틀린말!

++++ 12번 문제 빠짐( 5장 확인문제 확인!)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image074-164646407274335.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image076-164646407274336.gif" />

=> 1번 추상 클래스는 단독으로 new 안됨~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image078-164646407274337.gif" />

=> 4번 추상 메소드를 가진다는 것은 추상 클래스 이거나, 인터페이스의 경우~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image078-164646407274337.gif" />

=> 4번 추상 메소드를 가진다는 것은 추상 클래스 이거나, 인터페이스의 경우~!

++++ 16번 문제 빠짐(5장 확인문제 확인!!)

<br>

**< 클래스와 객체 > - 4장**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image080-164646407274338.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image082.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image084.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image086.gif" />

=> 그림 꼭 잘 확인!! s에서 n은 new해주고 ~~. 을 사용해야 함!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image088.gif" />

=> static은 따로 있기때문에 나가 아니네!? 그러니 this는 X!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image090.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image092.gif" />

나 - 클래스가 아니라 객체를 생성!

다 - (public, private) getter은 외부에서 검열후 읽어주는것, 

setter은 검열 후 대신 write해주는 것

라 - ----Circle() { } 를 하나 이상 코딩했다면

마 - 맞다.

(라, 마 다시 복습 해보고 이해하기)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image094.gif" />

함수와 메소드의 차이?!

C언어는 oop가 아니죠 바로 printf();로 함수 사용 => 함수

자바에서는 ~~.이 붙고 print!! 이런식으로 사용! => 메소드

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image096.gif" />

나 - protected는 접근 지정자 4가지중 하나! 같은 패키지에서 접근 가능! 다른패키지는 안되는데 다만 자손애들은 가능!(나중에 자세히!!)

라 - thread와 관련 => 이번 학기에서 X

**thread란? 프로세스 하나에 thread가 여러개 올 수 있다.**

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image098.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image100.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image102.gif" />

가 - private는 외부에서 접근 안되며, 상속도 안됨!

그림을 보면 public이 보디가드역할! (getter, setter 메소드 사용)

다 - defalut는 안적는 겁니다! (?)

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image104.gif" />

가 - 추상화는 나중에 제대로 배웁니다~!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image106.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image108.gif" />

다 - 나중에 더 자세히 배우겠지만, 그림 확인

라 - Math.random~! 썻던것 처럼! 그림 확인

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image110.gif" />

< oop의 큰 특징인 다형성 >

실제로 이렇게 println메소드가 여러개가 있는 것입니다.

이런게 바로 메소드 오버로딩 입니다.

생성자도 가능!!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image112.gif" />

생성자도 여러개 가능합니다! 그런데 왜 여러개를 만드나요??

예를 들면 햄버거를 만든다고 해도 고객마다 원하는게 다르다

즉 양상추 없거나 피클 없는 햄버거나 등등 필요하다 생성자!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image114.gif" />

첫줄 : 필드, 둘째줄 : 생성자, 셋째줄 : 생성자

this.은 자신을 가리키는 뜻이다. (필드, 메소드, 생성자)

/1. 오류 원인 : 생성자에서 다른 생성자를 부를 때는 this()부르는데 위치상 첫줄에 와야합니다!!

/2. ~~ = new Rectangle(); 부분에서 괄호안에 문제발생!

매개변수가 생성자에 다 필요하기 때문에 매개변수를 넣어서 수정하지 않는다면 문제 발생합니다.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image116.gif" />

static 이 없는 필드2개와 메소드 2개이다. 따라서 new해야~!

3줄 코딩의 1번은 new~

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image118.gif" />

=> 클래스명.을 쓴다는건?! static이라는 얘기!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image120.gif" />

=> r1.objCnt를 한다는건 굳이 멀리서 돌아간다는 얘기!!

아까 얘기한것!! 바로가도 되는걸 돌아간다는 얘기!!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image122.gif" />

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image124.gif" />

 

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image126.gif" />

필드 value는 디폴트 타입이니까 같은 패키지에서 접근가능!

다른패키지에서는 보디가드를 통해 접근가능!

setter, geter은 보디가드!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image128.gif" />

=> Dog로 접근하는건 static 멤버죠

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image130.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image132.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image134.gif" />

=> 과제!!

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image136.gif" />

나 : java.util.Scaner sc = ~~

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image138.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image140.gif" />

=> 예전에 import~~*; 이걸 너무많이하면 문제 생길 수 있다 기억

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image142.gif" />

=> 참고 : StringTokenizer 문자열을 토큰으로 만들어 버림!

=> 몰랐다면? if~~반~~ 코딩 엄청 했겠죠!

=> 이렇게 오픈 라이브러리가 많은데 뭐뭐 있는지 잘 안다면???  그것이 곧 실력이라고 개발자들은 얘기한다!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image144.gif" />

=> 지역번수가 아니라 초기화 자동 0!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image146.gif" />

=> 별표시 GC의 타겟

<br>

**< 반복문, 배열, 예외처리 > - 3장**

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image148.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image150.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image152.gif" />

sc.next(); 는 버퍼에 계속 남아있기 때문에 누군가 가져가줘야 한다.

sc.nextLine(); 이라면 단어 한개 이상 (즉 한줄) 가져가 준다.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image154.gif" />

sc.next(); 이것을 안해주면 문자 입력했었던 경우라면 입력 스트림에 남아있는 정수아닌 문자때문에 계속 발생이 된다.

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image156.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image158.gif" />

아직 객체없다. 주소 가르킬수 없다 라는의미. 컴파일 오류는 안뜸?!

앞으로 이 예외가 발생한다면, 아 객체가 없는데 +알파를 사용하려고 한거구나 생각!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image160.gif" />

5장때 자세히!!

<br>

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image162.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image164.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image166.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image168.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image170.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image172.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image174.gif" />

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image176.gif" />

과제!

<img src="/images/2022-01-27-(Java)Basic(기초문법)/clip_image178.jpg"/> 

먼저 1차원 배열과 똑같이 a 배열의 아이템을 하나씩 꺼낸다. 

{5,5,5} , {10,10,10,10,10} ... 이 통째로 차례차례 arr에 담긴다.

거기에 for each문을 한번 더 사용하여 각각의 arr를 k로 하나씩 빼낸다.
