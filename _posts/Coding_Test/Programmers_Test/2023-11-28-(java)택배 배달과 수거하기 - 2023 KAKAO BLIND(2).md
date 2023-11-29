---
title:  "[java]택배 배달과 수거하기 - 2023 KAKAO BLIND(2)"
categories : [ProgrammersTest]
tag : [백준, 프로그래머스, 알고리즘 문제풀이]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



## 문제

* **[택배 배달과 수거하기](https://school.programmers.co.kr/learn/courses/30/lessons/150369)**

<br><br>

## 풀이

**문제 해석을 하자면,**

* 각 집에 배달 및 수거할 때, 원하는 개수만큼 택배를 배달 및 수거할 수 있는 특징을 이용하여
* 모든 배달과 수거를 완료하는 "최소거리"를 구해야 한다.

<br>

**풀이**

* 규칙 생각 : 마지막부터 배달이든 수거든 선택해야 하고 최대cap 만큼 다 사용해야 제일 "최소거리"
  * 따라서 마지막을 먼서 선택하기 위해 -> **Stack**

* 고려할 반례들
  * 반례1) dStk가 "빈" 경우거나 pStk가 "빈" 경우를 생각해서 구현
  * 반례2) 4, 5, [8, 0, 8, 0, 4], [0, 0, 0, 0, 20]), 50 마찬가지
  * 이 모든 반례를 만족하기 위해서 따로 **"모든case"를 조건문으로 해결**


<br><br>

## 코드

```java
import java.util.*;
class Solution {
    public long solution(int cap, int n, int[] deliveries, int[] pickups) {
        //init
        long answer = 0;
        Stack<Node> dStk = new Stack<>(); // 배달
        Stack<Node> pStk = new Stack<>(); // 수거
        for(int i=1; i<=n; i++) {
            // 0은 스택에 넣을필요 없음
            if(deliveries[i-1]!=0) { 
                dStk.add(new Node(i, deliveries[i-1]));    
            }
            if(pickups[i-1]!=0) {
                pStk.add(new Node(i, pickups[i-1]));    
            }
        }
        //run
        // 둘다 비었으면 "종료"
        while(!dStk.isEmpty() || !pStk.isEmpty()) { 
            int minRoad = 0;

            // minRoad 갱신 - 모든 case 고려
            if(!dStk.isEmpty()) {
                if(!pStk.isEmpty() && dStk.peek().road < pStk.peek().road) {
                    minRoad = pStk.peek().road;
                }else {
                    minRoad = dStk.peek().road;
                }
            }else if(!pStk.isEmpty()) {
                minRoad = pStk.peek().road;
            }
            
            // dStk - 안비었으면 최대 cap만큼 진행
            int capTemp = cap;
            while(!dStk.isEmpty()) {
                int weight = dStk.peek().weight;
                if(capTemp-weight>0) { 
                    // 얘만 다음 스택값으로 이동
                    dStk.pop();
                    capTemp-=weight;
                }else if(capTemp-weight<0) {
                    dStk.peek().weight -= capTemp;
                    break;
                }else { // ==
                    dStk.pop();
                    break;
                }
            }

            // pStk - 안비었으면 최대 cap만큼 진행
            capTemp = cap;
            while(!pStk.isEmpty()) {
                int weight = pStk.peek().weight;
                if(capTemp-weight>0) { // 양수
                    // 얘만 다음 스택값으로 이동
                    pStk.pop();
                    capTemp-=weight;
                }else if(capTemp-weight<0) { // 음수
                    pStk.peek().weight -= capTemp;
                    break;
                }else { // ==
                    pStk.pop();
                    break;
                }
            }
            
            // System.out.println(minRoad); // debug test
            answer += minRoad*2; // 왕복이라 *2
        }
    
        //output test
        // for(int i=1; i<=n; i++) {
        //     System.out.println("dStk:"+dStk.peek().weight+"pStk:"+pStk.peek().weight); 
        //     dStk.pop(); pStk.pop();
        // }
        return answer;
    }
    
    static class Node {
        int weight; int road;
        public Node (int road, int weight) {
            this.road = road;
            this.weight = weight;
        }
    }
}
```

<br>**<br>**

## **느낀점**

처음에 스택을 이용하게된 이유는 다른 사람들의 질문에서 보게 되었기 때문이다.

이번에 문제를 풀면서 스택을 사용하는 이유를 체감했으니 다음엔 스스로 생각하기를...ㅠ
