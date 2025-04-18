---
title:  "[구현] 컴퓨터그래픽스(기하연산, 다각형 면적과 포함, Convex Hull, Plane Sweeping)"
categories : AlgorithmTest
tag : [알고리즘]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**컴퓨터 그래픽스에 관련한 고급 알고리즘들을 구현해보겠다.**

**이전에 개념 정리한 부분은 [컴퓨터그래픽스 개념](https://bh946.github.io/algorithm/(%EA%B3%A0%EA%B8%89%EA%B0%9C%EB%85%90)-%EC%BB%B4%ED%93%A8%ED%84%B0%EA%B7%B8%EB%9E%98%ED%94%BD%EC%8A%A4(%EA%B8%B0%ED%95%98%EC%97%B0%EC%82%B0,-%EB%8B%A4%EA%B0%81%ED%98%95-%EB%A9%B4%EC%A0%81%EA%B3%BC-%ED%8F%AC%ED%95%A8,-Convex-Hull,-Plane-Sweeping)/) 에서 참고!!**

**다음과 같은 순서로 진행된다.**

* 기본 기하 연산
* 다각형 면적과 포함 계산
* 볼록 외피(Convex Hull)
* 평면 소거법(Plane Sweeping) => 직사각형 합집합의 면적

<br><br>

# 기본 기하 연산

**구현 함수**

* ccw 함수(cw : counter clockwise = 시계방향) : `행렬식 계산` 함수
* leftTurn, rightTurn, collinear 함수 : 좌,우,일직선 검사 (`ccw 양수,음수,0` 에 따라 나뉨)
* direction 함수 : 세점의 `회전방향 검사`에 따라서 1,-1,0 반환 (좌 : 1, 우 : -1, 일 : 0)
* **between 함수 : 점 c가 선분 (a,b)에 위치하는가 검사**
  * 먼저, collinear 확인 이후 수직선 확인 및 직선 범위 제한
* **intersect 함수 : 교차여부를 구하는데, 선분의 끝점이 교차점 허용하는 경우**
  * intersectProp 함수 : 교차여부를 구하는데, 선분의 끝점이 교차점 허용하지 않는 경우
  * 교차 여부는 같은 방향의 회전이 나오지 않으면 성립. 즉, `회전1*회전2=-1` 이면 교차점 성립
  * 물론, (a,b) 와 (c,d) 직선 교차를 구하는것이므로 (a,b) 관점 뿐만 아니라 (c,d) 관점에서도 확인필요
    * **즉 `(a,b,c)회전*(a,b,d) 회전==-1 && (c,d,a)회전*(c,d,b)회전 ==-1` 이 성립해야 교차점 성립한다.**

<br>

```c++
/*
ccw 함수(cw : counter clockwise = 시계방향)
leftTurn, rightTurn, collinear 함수 : 좌,우,일직선 검사
direction 함수 : 세점의 회전방향 검사에 따라서 1,-1,0 반환
between 함수 : 점 c가 선분 (a,b)에 위치하는가 검사
intersectProp 함수 : 교차여부를 구하는데, 선분의 끝점이 교차점 허용하지 않는 경우
intersect 함수 : 교차여부를 구하는데, 선분의 끝점이 교차점 허용하는 경우

이 중에서 between, intersect 가 중요

자료형 왠만하면 long long 쓰자
*/

#include<iostream>
using namespace std;

struct point {
	long long x;
	long long y;
};
typedef point Point;

// OP벡터, OQ벡터 (O는 원점)
// det(행렬식) => q가 p에 어느 방향인지 출력
long long ccw2(Point p, Point q) {
	return p.x * q.y - p.y * q.x;
}

// RP벡터, RQ벡터 => 시점R을 원점으로 만들어서 계산
// 시점을 원점으로 이동 : p-r, q-r
int ccw(Point r, Point p, Point q) {
	p.x -= r.x;
	p.y -= r.y;
	q.x -= r.x;
	q.y -= r.y;
	return ccw2(p, q);
}

// 세점 a,b,c의 회전방향이 좌,우,일직선인가 검사
bool leftTurn(Point a, Point b, Point c) {
	return ccw(a, b, c) > 0; // 좌회전 검사
}
bool rightTurn(Point a, Point b, Point c) {
	return ccw(a, b, c) < 0; // 우회전 검사
}
bool collinear(Point a, Point b, Point c) {
	return ccw(a, b, c) == 0; // 일직선 검사
}

// 선분 교차 검사를 위해 회전 방향에 따른 값 출력
// 좌 : 1, 우 : -1, 일 : 0
int direction(Point a, Point b, Point c) {
	if (leftTurn(a,b,c)) return 1;
	if (rightTurn(a,b,c)) return -1;
	if (collinear(a,b,c)) return 0;
}

// 점 c가 선분 (a,b)에 위치하는가
bool between(Point a, Point b, Point c) {
	if (!collinear(a, b, c)) return false; // 일직선 상에 없으면 false
	// 아래부턴 일직선 상에 있으니까 범위제한만 주면 된다.
	if (a.x != b.x) { // 수직선 아니면
		
		return (a.x <= c.x && c.x <= b.x) || (b.x <= c.x && c.x <= a.x);
	}
	else { // 수직선 이면
		return (a.y <= c.y && c.y <= b.y) || (b.y <= c.y && c.y <= a.y);
	}
}

// 선분 교차 검사 (교차점 허용) => 선분 ab, cd
int intersect(Point a, Point b, Point c, Point d) {
	// (a,b,c)*(a,b,d) ==-1 && (c,d,a)*(c,d,b)==-1 성립하면 교차
	// 추가조건 때문에 <=0로 판별
	if (direction(a, b, c) * direction(a, b, d) <= 0 && direction(c, d, a) * direction(c, d, b) <= 0) {
		// direction함수는 방향만을 나타내기 때문에 만약 0인 경우 일직선상인 건데,
		// 두 직선이 서로 접하는지 범위제한을 줘서 판단할 필요가 있음(between 함수 활용)
		if (direction(a, b, c) * direction(a, b, d) == 0 && direction(c, d, a) * direction(c, d, b) == 0) {
			// 점 c또는 d가 a,b직선 상에 존재하나 판단
			// 점 a또는 b가 c,d직선 상에 존재하나 판단
			if (between(a, b, c) || between(a, b, d)|| between(c, d, a) || between(c, d, b)) {
				return 1;
			}
			else
				return 0;
		}
		return 1;
	}
	else
		return 0;
}


// 선분 교차 검사 (교차점 제외) => 선분 ab, cd
bool intersectProp(Point a, Point b, Point c, Point d) {
	return direction(a, b, c) * direction(a, b, d) < 0 && direction(c, d, a) * direction(c, d, b) < 0;
}

int main() {
	Point a = { 3,5 };
	Point b = { 4,6 };
	Point c = { 2,7 };
	cout << ccw(a, b, c) << '\n';
	
	a = { 1,3 };
	b = { 3,1 };
	c = { 2,2 };
	if (between(a, b, c)) cout << "선분위에 점 존재" << '\n';
	
	a = { 3,5 };
	b = { 4,6 };
	c = { 2,7 };
	Point d = {4,5};
	if (intersect(a, b, c,d)) cout << "교차 존재" << '\n';

	return 0;
}
```

<br><br>

# 다각형 면적과 포함 계산

**구현 함수**

* **area 함수 : 다각형 면적 구하기**
  * 다각형 P의 정점 전부 외적해서 더하면 된다.
* **insidePolygon 함수 : 다각형 포함 구하기**
  * 점 q가 다각형 에지(표면)에 있나 먼저 확인한다.(있으면 바로 false 반환)
  * 이후에 교차점을 판별한다(교차점 존재하면 crossings에 +1)
    * 다각형 점 P[i], P[iPlus1] 의 y 범위 내에 점 q의 y가 존재하는지 확인하고, 존재하면 우선 교차한다는걸 이해하자.
    * 그리고 교차점 인정안하는 부분이 존재하므로, 그것을 방지하기 위해 leftTurn까지 적용한다.
      * 이것과 관련해서는 개념부분 게시물을 참고하면 알 수 있다.
  * 최종 적으로 교차점의 개수가 홀수이면 true를 반환한다.

<br>

```c++
/*
area 함수 : 다각형 면적 구하기
insidePolygon 함수 : 다각형 포함 구하기
*/

#include<iostream>
#include<vector>
using namespace std;

struct point {
	int x;
	int y;
};
typedef point Point;


// OP벡터, OQ벡터 (O는 원점)
// det(행렬식) => q가 p에 어느 방향인지 출력
int ccw2(Point p, Point q) {
	return p.x * q.y - p.y * q.x;
}

// RP벡터, RQ벡터 => 시점R을 원점으로 만들어서 계산
// 시점을 원점으로 이동 : p-r, q-r
int ccw(Point r, Point p, Point q) {
	p.x -= r.x;
	p.y -= r.y;
	q.x -= r.x;
	q.y -= r.y;
	return ccw2(p, q);
}

// 세점 a,b,c의 회전방향이 좌,우,일직선인가 검사
bool leftTurn(Point a, Point b, Point c) {
	return ccw(a, b, c) > 0; // 좌회전 검사
}
bool rightTurn(Point a, Point b, Point c) {
	return ccw(a, b, c) < 0; // 우회전 검사
}
bool collinear(Point a, Point b, Point c) {
	return ccw(a, b, c) == 0; // 일직선 검사
}

// 점 c가 선분 (a,b)에 위치하는가
bool between(Point a, Point b, Point c) {
	if (!collinear(a, b, c)) return false; // 일직선 상에 없으면 false
	// 아래부턴 일직선 상에 있으니까 범위제한만 주면 된다.
	if (a.x != b.x) { // 수직선 아니면
		return (a.x <= c.x && c.x <= b.x) || (b.x <= c.x && c.x <= a.x);
	}
	else { // 수직선 이면
		return (a.y <= c.y && c.y <= b.y) || (b.y <= c.y && c.y <= a.y);
	}
}

// 다각형 면적 구하기 (외전 전부 더하면 됨)
double area(vector<Point> p) {
	int ret = 0;
	int n = p.size(); // 다각형 p의 정점 개수
	for (int i = 0; i < n; i++) {
		int j = (i + 1) % n; // 마지막에 index n->0으로 바뀜
		ret += (p[i].x * p[j].y - p[i].y * p[j].x);
	}
	return abs(ret) / 2.0;
}

// 다각형 포함 문제 (교차점 홀수면 내부 포함 인정)
bool insidePolygon(Point q, vector<Point> P) {
	int n = P.size();
	int crossings = 0;
	for (int i = 0; i < n; i++) {
		int iPlus1 = (i + 1) % n; // 마지막에 index n->0으로 바뀜
		// 1. 점 q가 다각형 에지(표면)에 있나 먼저 확인
		if (between(P[i], P[iPlus1], q)) return false;
		// 2. 교차점 판별 => crossings에 +1
		if((P[i].y<q.y&&P[iPlus1].y>=q.y&&leftTurn(P[i],P[iPlus1],q)) ||
			(P[iPlus1].y<q.y&&P[i].y>=q.y&&leftTurn(P[iPlus1],P[i],q)))
			crossings++;
	}
	if (crossings % 2 != 0) return true; // 홀수라면 true !!
	return false;
}


int main() {
	vector<Point> p;
	p.push_back({ 2,2 });
	p.push_back({ 7,3 });
	p.push_back({ 4,6 });
	p.push_back({ 1,5 });
	cout << area(p)<<'\n'; // 다각형 면적 테스트 답 : 14

	Point q = { 2,3 };
	if(insidePolygon(q,p)) cout<<"다각형 포함" <<'\n'; // 다각형 포함 테스트

	return 0;
}
```

<br><br>

# 볼록 외피(Convex Hull)

**구현 함수**

* convexHull_Javis() : Jarvis's march 알고리즘 - 복잡도 N^2

* convexHull() : 위 함수 개선한 알고리즘 - 복잡도 NlogN

<br>

**예시로 입출력에 사용될 데이터 그림**

![image-20230423152417210](/images/2023-04-02-(구현) 컴퓨터그래픽스(기하연산, 다각형 면적과 포함, Convex Hull, Plane Sweeping)/image-20230423152417210.png) 

<br>

## convexHull_Javis() => N^2

**구현 과정**

1. 제일 크거나 작은 x값 또는 y값을 초기 극단점으로 사용(여기선 x값으로 했음)
2. 핵심 방법은 prev, next, i 에 leftTurn 으로 구현해나간다는 점(시계방향으로 극단점 구해져나감)

<br>

```c++
/*
볼록 외피(=Convex Hull)

convexHull_Javis() : Jarvis's march 알고리즘 - 복잡도 N^2
핵심 방법은 prev, next, i 에 leftTurn 으로 구현해나간다는 점
*/

#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

struct point {
	int x;
	int y;
};
typedef point Point;

// OP벡터, OQ벡터 (O는 원점)
// det(행렬식) => q가 p에 어느 방향인지 출력
int ccw2(Point p, Point q) {
	return p.x * q.y - p.y * q.x;
}

// RP벡터, RQ벡터 => 시점R을 원점으로 만들어서 계산
// 시점을 원점으로 이동 : p-r, q-r
int ccw(Point r, Point p, Point q) {
	p.x -= r.x;
	p.y -= r.y;
	q.x -= r.x;
	q.y -= r.y;
	return ccw2(p, q);
}

// 세점 a,b,c의 회전방향이 좌,우,일직선인가 검사
bool leftTurn(Point a, Point b, Point c) {
	return ccw(a, b, c) > 0; // 좌회전 검사
}
bool rightTurn(Point a, Point b, Point c) {
	return ccw(a, b, c) < 0; // 우회전 검사
}
bool collinear(Point a, Point b, Point c) {
	return ccw(a, b, c) == 0; // 일직선 검사
}

int getExtream(Point P[], int n) {
	// x,y중 본인은 x가 제일 큰 값으로 하겠음
	int temp = -1;
	int maxIndex = 0;
	for (int i = 0; i < n; i++) {
		if (P[i].x > temp) {
			temp = P[i].x;
			maxIndex = i;
		}
	}
	return maxIndex;
}

vector<Point> C; // C가 극단점 모음

// P[]가 전체 값
void convexHull_Javis(Point P[], int n) {
	// init
	int start = getExtream(P, n); // 초기 하나의 극단점 탐색(index 반환)
	int prev = start;
	int next = -1; 

	while (next != start) { // next==start면 종료(=> 1바퀴 돌면 종료)
		C.push_back(P[prev]); // 극단점 기록
		// 다음 극단점 탐색
		next = 0;
		if (next == prev) next = start; // 이걸 넣어야 무한루프에 빠지지 않음
		// why? next, prev가 같으면 leftTurn을 하는 의미가 없음. 같은값만 나옴.
        // 자세히 설명 : 처음부터 점 P가 정렬된 상태면 상관없는데 그게 아니기 때문에,
        // next와 prev가 같아지는 구간이 존재하게 되는것. 따라서 next==prev인 상황에서는
        // next에 prev(=0) 제외한 아무값이나 적용해도됨.(여기선 그냥 start값 적용)
        // next에 실제로 start대신 1 넣어도 잘 동작함

		for (int i = 0; i < n; i++) {
			if (i != prev && i != next && leftTurn(P[prev], P[next], P[i]))
				next = i;
		}
		prev = next; // prev update
	}
}

int main() {
	// 예시 그림 : https://stackoverflow.com/questions/62376042/calculating-and-displaying-a-convexhull
	Point P[10] = { {1,6},{2,1},{8,0},{9,5},{3,9},{2,3},{3,5},{4,4},{5,3},{8,3} };
	convexHull_Javis(P, 10);
	cout << C.size() << '\n'; // 5개 나와야 함(극단점 개수)

	return 0;
}
```

<br><br>

## convexHull() => NlogN

**참고 사이트 : https://www.crocus.co.kr/1288**

1. 우선 기준점을 잡는다. (보통 기준점은 y좌표가 가장 작은 것을 기준으로 한다.)
2. 그리고 이 기준점으로 하여 다른 점들을 반시계 방향으로 정렬한다.  
    (각에 따라 정렬하면 된다.) => 각에 따라 정렬 핵심
  1. y좌표, x좌표 기준 오름차순 정렬 (절대 위치 사용)
  2. 각도 비교로 오름차순 정렬 (상대 위치 사용(기준점 기준))
3. 컨벡스 헐 알고리즘(그라함 스캔(Graham's Scan) 알고리즘)을 이용한다.  
    => 사이트 그림보면 이해하기 쉬움
  1. 스택을 이용하며, 스택에 극단점들이 최종 기록된다.
  2. 핵심 방법  

    first, second, next가 좌회전 ( > 0 )이라면 second push 하고 next 이동   
    우회전( < 0 )이라면 위의 while문 계속 반복 (즉, 이전 first,second상태로 되돌아감)

<br>

```c++
/*
https://www.crocus.co.kr/1288
사이트 참고해서 이 소스 작성함.

1. 우선 기준점을 잡는다. (보통 기준점은 y좌표가 가장 작은 것을 기준으로 한다.)

2. 그리고 이 기준점으로 하여 다른 점들을 반시계 방향으로 정렬한다.
(각에 따라 정렬하면 된다.) => 각에 따라 정렬 핵심

3. 컨벡스 헐 알고리즘(그라함 스캔(Graham's Scan) 알고리즘)을 이용한다.
=> 사이트 그림보면 이해하기 쉬움
*/

#include<iostream>
#include<algorithm>
#include<cmath> // atan2 (아크탄젠트 - 두점사이 절대각도 구함)
#include<stack>
using namespace std;

struct point {
	int x, y;
	int p, q;
};
typedef point Point;

// OP벡터, OQ벡터 (O는 원점)
// det(행렬식) => q가 p에 어느 방향인지 출력
int ccw2(Point p, Point q) {
    return p.x * q.y - p.y * q.x;
}

// RP벡터, RQ벡터 => 시점R을 원점으로 만들어서 계산
// 시점을 원점으로 이동 : p-r, q-r
int ccw(Point r, Point p, Point q) {
    p.x -= r.x; p.y -= r.y;
    q.x -= r.x; q.y -= r.y;
    return ccw2(p, q);
}

double getAngle(Point p) {
    return atan2(p.q, p.p);
}

// 1. 각도 비교 사용시 각도 비교를 우선으로 정렬
// 2. 각도 비교 사용 안할시 y기준 정렬, x기준 정렬
bool comp(Point a, Point b) {
    // p, q 사용시 아래 조건문 만족하게 됨
    // p, q 사용안하면 0으로 초기화 되어있어서 아래 조건문 불만족
    if (getAngle(a) != getAngle(b)) return getAngle(a) < getAngle(b);
    
    if (a.y != b.y) return a.y < b.y;
    return a.x < b.x;
}

Point P[100];


int main() {
    // 그림 : https://stackoverflow.com/questions/62376042/calculating-and-displaying-a-convexhull
    // input
    // 10
    // 1 6 2 1 8 0 9 5 3 9 2 3 3 5 4 6 5 3 8 3

    // {1,6},{2,1},{8,0},{9,5},{3,9},{2,3},{3,5},{4,6},{5,3},{8,3}
    // x좌표로 정렬후
    // {1,6},{2,1},{2,3},{3,9},{3,5},{4,6},{5,3},{8,0},{8,3},{9,5}

    // 출력 : 0 1 3 7 9

    int n; // 점 개수
    cin >> n;

    for (int i = 0; i < n; i++) {
        int x, y;
        cin >> x >> y;
        P[i] = { x,y };
    }

    // y좌표, x좌표 기준 오름차순 정렬
    sort(P, P + n, comp);

    // 기준점(=P[0])기준 상대위치 기록 (따라서 i=1부터)
    for (int i = 1; i < n; i++) {
        P[i].p = P[i].x - P[0].x; // 기준점 빼서 원점으로 이동
        P[i].q = P[i].y - P[0].y;
    }

    // 구했던 상대위치 값으로 반시계 방향 정렬(기준점 제외. 따라서 p+1부터)
    // => 기준점에서 각도 비교로 반시계 정렬하면 됨
    sort(P + 1, P + n, comp);

    // 1st, 2st로 바로 초기값 사용 가능
    stack<int> st;
    st.push(0); st.push(1); // 0, 1 index 푸시


    // 전처리 과정 끝
    // 알고리즘 시작
    int next = 2; // next는 index 2부터 판단 시작
    while (next < n) { // 0부터 시작했으므로 next가 최종 n까지 가면 1바퀴 다 돈것이다
        while (st.size() >= 2) {
            int first, second;
            second = st.top();
            st.pop();
            first = st.top();

            // first, second, next가 좌회전 ( > 0 )이라면 second push 하고 next 이동
            // 우회전( < 0 )이라면 위의 while문 계속 반복 (즉, 이전 first,second상태로 되돌아감)
            if (ccw(P[first], P[second], P[next]) > 0) { // leftTurn써도 됨
                st.push(second);
                break;
            }
        }

        // next push
        st.push(next++);
    }

    cout << st.size();

    return 0;
}
```

<br><br>

# 평면 소거법(=Plane Sweeping)

**구현 함수**

* **UnionArea() : 합집합의 면적 => 그냥 전체 사각형 내부 면적이다. 중복 면적을 제외했을뿐**
  * 사용할 사각형 선택(x값 활용)
  * counts배열 구하기(y값 활용)
  * 면적 계산 시작(counts 이용해서 사각형 있는 경우만 세로*가로 면적 계산)

<br>

```c++
#include<iostream>
#include<vector>
using namespace std;

struct Rectangle {
	int x1, y1, x2, y2;
};

vector<Rectangle> inArr;
int Q[10]; // 직사각형 x좌표 정렬해서 기록
int S[10]; // 직사각형 y좌표 정렬해서 기록
int counts[10]; // 겹쳐진 사각형 수
int m1 = 8; // events - Q size
int m2 = 8; // S size

// 합집합의 면적 계산 (합집합이므로 중복 면접 제외)
int UnionArea() {
	int sweepLine = 0; int delta = 0; int area = 0; // 면적
	for (int i = 0; i < m1; i++) {
		sweepLine = Q[i];
		Rectangle R = { 0,0,0,0 };
        // 0. 사용할 사각형 선택(x값 겹칠때 마다 선택)
		for (int j = 0; j < inArr.size(); j++) {
			if (inArr[j].x1 == Q[i] || inArr[j].x2 == Q[i]) {
				R = inArr[j];
				break;
			}
		}

        // 1. counts배열 구하기(y값 활용)
		// sweepLine이 만나는 사각형의 왼쪽 가로선만 +1, 오른쪽 가로선은 -1
		if (sweepLine == R.x1) delta = 1;
		else delta = -1;

		int y1 = R.y1; int y2 = R.y2;
		for (int j = 0; j < m2; j++) {
			if (y1 <= S[j] && S[j] < y2) {
				counts[j] += delta; // S[j]~S[j+1] 구간에 겹쳐진 사각형의 수
			}
		}
        
        // 2. 면적 계산 시작 
        // 현재 내부면적 세로줄(S[j + 1] - S[j])
		int curlength = 0; 
		for (int j = 0; j < m2; j++) {
			if (counts[j] > 0) { // counts배열 있는 값만(사각형 있는 경우에만)
				curlength += S[j + 1] - S[j];
			}
		}
        
		// 현재 내부면적 가로줄(Q[i+1]-Q[i])
		if ((i + 1) < m1) {
			area += curlength * (Q[i + 1] - Q[i]); // 현재 구한 면적 더함
		}
	}
	return area;
}


int main() {
	cin >> N;
	for (int i = 0; i < N; i++) {
		cin >> inArr[i].x1 >> inArr[i].y1 >> inArr[i].x2 >> inArr[i].y2;
		Q[i * 2] = inArr[i].x1; Q[i * 2 + 1] = inArr[i].x2;
		S[i * 2] = inArr[i].y1; S[i * 2 + 1] = inArr[i].y2;
	}
	m1 = N * 2; // 총 x축 개수
	m2 = N * 2; // 총 y축 개수

	sort(&Q[0], &Q[m1]);
	sort(&S[0], &S[m2]);

	cout << unionArea();

	return 0;
}
```
