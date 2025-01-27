---
title:  "[java,백준17140]이차원 배열과 연산 - HashMap, PriorityQueue를 활용한 정렬"
categories : [BaekjoonTest]
tag: [백준, 알고리즘, 구현, HashMap, PriorityQueue, 정렬]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



**HashMap과 PriorityQueue를 활용하여 배열의 행과 열을 정렬하는 시뮬레이션 문제입니다.**

**[이차원 배열과 연산(백준17140)](https://www.acmicpc.net/problem/17140)**

<br>

**예시를 시각화 해보자**

```text
예시 시각화: 초기 3x3 -> R연산(무조건) -> 3x6 -> C연산 -> ...
초기 3x3 배열: time=0
1 2 1
2 1 3
3 3 3

가장 처음에는 행의 개수 ≥ 열의 개수 이기 때문에, R 연산이 적용
(2, 1)은 (수, 횟수)를 의미, 또한 {수, 횟수} 순으로 오름차순
1 2 1 → (2, 1), (1, 2)         → 2 1 1 2
2 1 3 → (1, 1), (2, 1), (3, 1) → 1 1 2 1 3 1
3 3 3 → (3, 3)                 → 3 3

R 연산 후 3x6: time=1
2 1 1 2 0 0
1 1 2 1 3 1
3 3 0 0 0 0

다음에 적용되는 연산은 행의 개수 < 열의 개수이기 때문에 C 연산
C연산 자세한 과정은 생략

C 연산 후 6x6: time=2
1 3 1 1 3 1
1 1 1 1 1 1
2 1 2 2 0 0
1 2 1 1 0 0
3 0 0 0 0 0
1 0 0 0 0 0
```

<br>

<br>

## 풀이

**💡 시뮬레이션 주의사항**  

- 기저 조건:
  - 시간이 100초가 넘어가면 "탈출 후 -1" 출력
  - A\[r][c]==k 가 성립하면 "탈출 후 시간" 출력
- 필수 재할당(이전값 남아있으면 꼬임):
  - 복사 배열(copyA)과 HashMap은 매번 초기화
- 배열 크기가 100을 넘으면 나머지 버림

<br>

**<span style="color:#ff9300">HashMap으로 숫자 등장 횟수를 세고, PriorityQueue로 정렬</span>하는 방식으로 구현**

**핵심 구현 사항**

1. R,C 연산을 문제에 주어진 조건에 맞게 "분기"

   - R연산: 행 >= 열 길이 일 때 수행

   - C연산: 행 < 열 길이 일 때 수행

2. R,C 연산 구현: HashMap, 우선순위 큐 활용
   1. 횟수 구할 때 HashMap 자료구조를 사용 (배열로 하려다가 이게 더 간편+효율적이라 이걸로 함)
   2. 정렬은 우선순위 큐 자료구조를 사용 (배열로 하려다가 이게 훨~씬 간편해서 이걸로 함)
      - 정렬 기준: 등장 횟수 오름차순, 같으면 숫자 오름차순
   3. 행, 열 최대길이 구하는건 maxLen 전역 배열에 업데이트

<br>

**R 연산**

```java
static void R() {
    int maxRow = maxLen[0];
    int maxCol = maxLen[1];
    for (int i = 1; i <= maxRow; i++) {
        HashMap<Integer, Integer> map = new HashMap<>();
        // 숫자 등장 횟수 세기
        for (int j = 1; j <= maxCol; j++) {
            if(A[i][j] == 0) continue; //0 무시
            map.put(A[i][j], map.getOrDefault(A[i][j], 0) + 1);
        }
        // 정렬 및 배열 갱신
        Iterator<Integer> itor = map.keySet().iterator();
        while (itor.hasNext()) {
            int key = itor.next();
            qu.offer(new Pair(key, map.get(key)));
        }
      	//R연산(copyA 기록)
      	int col=1;
      	while (!qu.isEmpty()) {
        if (col > 100) {
            //배열 크기 100 넘으면 나머지는 버리기
            col=100;
            qu.clear();
            break;
        }
        Pair cur = qu.poll();
        copyA[i][col++]=cur.num;
        copyA[i][col++]=cur.cnt;
      }
      //maxLen 열 길이 업데이트
      maxLen[1]=Math.max(maxLen[1],col);
    }
}
```

<br>

**정렬 기준 구현**

```java
private static class Pair implements Comparable<Pair> {
    int num, cnt;
    public int compareTo(Pair o) {
        return this.cnt == o.cnt ? this.num - o.num : this.cnt - o.cnt;
    }
}
```

<br>

<br>

## 전체 코드

```java
public class Main {
  static int r, c, k;
  static int[][] A = new int[101][101];
  static int[][] copyA = new int[101][101]; //복제용 (재할당)
  static int[] maxLen = {3+1, 3+1}; //행 길이, 열 길이
  static PriorityQueue<Pair> qu = new PriorityQueue<Pair>();

  static void R() {
    int maxRow = maxLen[0];
    int maxCol = maxLen[1];
    for (int i = 1; i <= maxRow; i++) {
      //횟수 기록!
      HashMap<Integer, Integer> map = new HashMap<>(); //매번 init
      for (int j = 1; j <= maxCol; j++) {
        if(A[i][j] == 0) continue; //0 무시
        int findNum = map.getOrDefault(A[i][j], 0); //값 없으면 0
        map.put(A[i][j], findNum + 1);
      }
      //다 기록된 행 마다 R연산(정렬)!
      Iterator<Integer> itor = map.keySet().iterator();
      while (itor.hasNext()) {
        int key = itor.next();
        int value = map.get(key);
        qu.offer(new Pair(key, value)); //큐 삽입 (정렬)
      }
      //R연산(A배열 업데이트 위해 copyA 기록)
      int col=1;
      while (!qu.isEmpty()) {
        if (col > 100) {
          //배열 크기 100 넘으면 나머지는 버리기
          col=100;
          qu.clear();
          break;
        }
        Pair cur = qu.poll();
        copyA[i][col++]=cur.num;
        copyA[i][col++]=cur.cnt;
      }
      //maxLen 열 길이 업데이트
      maxLen[1]=Math.max(maxLen[1],col);
    }
  }

  static void C() {
    int maxRow = maxLen[0];
    int maxCol = maxLen[1];
    for (int j = 1; j <= maxCol; j++) {
      //횟수 기록!
      HashMap<Integer, Integer> map = new HashMap<>(); //매번 init
      for (int i = 1; i <= maxRow; i++) {
        if(A[i][j] == 0) continue; //0 무시
        int findNum = map.getOrDefault(A[i][j], 0); //값 없으면 0
        map.put(A[i][j], findNum + 1);
      }
      //다 기록된 열 마다 C연산(정렬)!
      Iterator<Integer> itor = map.keySet().iterator();
      while (itor.hasNext()) {
        int key = itor.next();
        int value = map.get(key);
        qu.offer(new Pair(key, value)); //큐 삽입 (정렬)
      }
      //C연산(A배열 업데이트 위해 copyA 기록)
      int row=1;
      while (!qu.isEmpty()) {
        if (row > 100) {
          //배열 크기 100 넘으면 나머지는 버리기
          row=100;
          qu.clear();
          break;
        }
        Pair cur = qu.poll();
        copyA[row++][j]=cur.num;
        copyA[row++][j]=cur.cnt;
      }
      //maxLen 행 길이 업데이트
      maxLen[0]=Math.max(maxLen[0],row);
    }
  }

  public static void main(String[] args) throws IOException {
    //input
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer stk = new StringTokenizer(br.readLine(), " ");
    r = Integer.parseInt(stk.nextToken());
    c = Integer.parseInt(stk.nextToken());
    k = Integer.parseInt(stk.nextToken());
    for (int i = 1; i <= 3; i++) {
      stk = new StringTokenizer(br.readLine(), " ");
      for (int j = 1; j <= 3; j++) {
        A[i][j] = Integer.parseInt(stk.nextToken());
      }
    }
    //run
    int time=0;
    while (true) {
      //base condition
      if(A[r][c]==k) break;
      if (time > 100) {
        System.out.println(-1);
        return;
      }
      //start
      time++;
      copyA = new int[101][101]; //매번 init
      //R,C 연산 선택
      if (maxLen[0] >= maxLen[1]) {
        R();
      } else {
        C();
      }
      //새로 구한 copyA 값을 A 로 복제!
      for (int ci = 1; ci <= 100; ci++) {
        for (int cj = 1; cj <= 100; cj++) {
          A[ci][cj]=copyA[ci][cj];
        }
      }
      //debug
//      System.out.println();
//      for (int i = 0; i < 8; i++) {
//        for (int j = 0; j < 8; j++) {
//          System.out.print(A[i][j]+" ");
//        }
//        System.out.println();
//      }
//      if(time==2) break;
    }

    //output
    System.out.println(time);
  }
  private static class Pair implements Comparable<Pair> {
    int num; int cnt;

    public Pair(int num, int cnt) {
      this.num = num; this.cnt = cnt;
    }

    @Override
    public int compareTo(Pair o) {
      if (this.cnt == o.cnt) {
        return this.num - o.num;
      } else {
        return this.cnt - o.cnt;
      }
    }
  }
}
```

*<span style="color:#777777">주관적인 생각의 풀이이므로 틀린 부분 지적은 언제나 환영합니다⚡</span>*
