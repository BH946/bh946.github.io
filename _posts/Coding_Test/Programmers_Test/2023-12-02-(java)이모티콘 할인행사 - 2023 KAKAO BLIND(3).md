---
title:  "[java]이모티콘 할인행사 - 2023 KAKAO BLIND(3)"
categories : [ProgrammersTest]
tag : [백준, 프로그래머스, 알고리즘 문제풀이, 다시풀기]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



## 문제

* **[이모티콘 할인행사](https://school.programmers.co.kr/learn/courses/30/lessons/150368)**

<br><br>

## 풀이

**문제 해석을 하자면,**

* `이모티콘 플러스 서비스 가입자수 -> 이모티콘 판매액 ` 순서로 늘리기를 원한다.
* 주어진 조건에 맞게 계산하자.

<br>

**풀이**

* 최대 4^7 복잡도 이므로 크지않음 -> 브루트포스로 진행
  * 유저도 100명이 최대니까 `4^7*100=약 2백만 정도`

* **따라서 문제에 주어진대로 "구현"만 잘 하면 된다.**
  * 할인가 계산
    * 처음에 할인이 40%라면, 정말 원가격에 40% 곱한 가격을 생각했다.
    * 이는 할인이 얼마나 되었는지 가격이지, 할인 후의 제품가격이 아니다. (착각 조심..!)
    * 따라서 원가격에 60% 곱한 가격이 "할인 후의 제품가격"이다.

  * 할인율 구분
    * 이모티콘 할인율은 10,20,30,40 으로 총 4개이고, 사용자 할인율은 입력으로 주어진다.


<br>

**예상 트리**

* 예상 상태트리 -> 깊이 7, 가지수 4

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/abd18b2a-8e2f-4608-9f18-0754061fbd72" alt="image" style="zoom: 33%;" /> 

<br><br>

## 코드

```java
class Solution {
    public int n, m;
    public int[][] result = new int[100][2]; // {비용, 가입여부}
    public int[] outRate = new int[7];
    public static final int[] EMOTICON_RATE = {10,20,30,40};
    
    public void dfs(int depth, int[][] users, int[] emoticons, int[] answer) {
        //base condition
        if(depth == m) {
            int sumJoin = 0;
            int sumMoney = 0;
            // 1번 목표 우선, 이후 2번 목표 -> update result
            for(int i=0; i<n; i++) { // user수 만큼 반복
                int userRate = users[i][0];
                int userMoney = users[i][1];
                int curMoney = 0;
                for(int j=0; j<m; j++) { // emoticon수 만큼 반복
                    if(userRate <= outRate[j]) { // 사용자 할인율 이상일때
                        int price = (int)(((100-outRate[j])*0.01)*emoticons[j]); // 할인가
                        curMoney+=price;
                    }
                }
                // update result
                if(curMoney >= userMoney) {
                    // 서비스 가입!
                    sumJoin++;
                }else {
                    // 서비스 미가입!
                    sumMoney+=curMoney;
                }
            }
            // update answer
            if(sumJoin > answer[0]) {
                answer[0] = sumJoin;
                answer[1] = sumMoney;
            }
            else if(sumJoin == answer[0]) {
                answer[1] = Math.max(answer[1], sumMoney);
            }
            return;
        }
        //recursion
        for(int i=0; i<4; i++) {
            outRate[depth] = EMOTICON_RATE[i];
            dfs(depth+1, users, emoticons, answer);
        }
    }
    
    public int[] solution(int[][] users, int[] emoticons) {
        int[] answer = {0,0};
        n = users.length;
        m = emoticons.length;
        
        dfs(0, users, emoticons, answer);

        return answer;
    }
}
```

<br>**<br>**

## **느낀점**

풀이까지는 잘 생각했는데, 이상하게 구현에서 실패한 문제

다른 사람 풀이도 나와 같은사람을 봤는데 본인은 구현을 제대로 못했던것 같다.

다시 풀어보자.
