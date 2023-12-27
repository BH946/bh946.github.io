---
title:  "[java]표현 가능한 이진트리 - 2023 KAKAO BLIND(4)"
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

* **[표현 가능한 이진트리](https://school.programmers.co.kr/learn/courses/30/lessons/150367)**

<br><br>

## 풀이

**문제 해석을 하자면,**

* 먼저, 이진트리를 표현하는 방법(조건)을 제시합니다.
* 이후 숫자가 주어지면 이진트리로 표현할 수 있는지를 물어봅니다.

<br>

**풀이**

* **이진화**
* **이진트리 판별위해 이진화 앞에 '0'최대 붙이기 -> "포화 이진트리로 만들기 위해"**
  * 이진수를 생각해보면 0001 은 십진수로 1이다
  * 앞에 0을 빼도 1로 잘 표현된다
  * 즉, 오른쪽부터 숫자가 채워지므로 `이진수 맨앞에 0을 붙이자`

* **이진트리 판별 -> 더미노드(0)을 사용하기 때문에 문제없는지 확인이 필요**
  * 이진트리의 루트노드가 0이면 서브트리의 루트도 0이어야 한다. -> 이 문제에서 제시한 조건이다!
  * 이것은 문제 특성상 더미노드가 0이므로 더미노드 자식이 1일 수가 없기 때문이다


<br><br>

## 코드

```java
import java.util.*;
// substring은 비효율이긴 하지만 구현이 쉬워서 index사용 대신 이것으로 진행
class Solution {
    
    public Boolean isBinaryTree(String binary) {
        // base condition
        if(binary.length()==0) return true;
        
        int root = binary.length()/2;
        String left = binary.substring(0, root); // 비효율이긴 함
        String right = binary.substring(root+1);
        if(binary.charAt(root) == '0') { // 루트 노드가 0이라면 서브 트리도 모두 0
            return isZeroTree(left) && isZeroTree(right);
        }
        else { // 루트 노드가 1이라면 서브 트리 검사
            return isBinaryTree(left) && isBinaryTree(right);
        }
        
    }
    
    public Boolean isZeroTree(String binary) {
        // base condition
        if(binary.length()==0) return true;
        
        int root = binary.length()/2;
        String left = binary.substring(0, root); // 비효율이긴 함
        String right = binary.substring(root+1);
        if (binary.charAt(root) == '1') { // 루트 노드가 1이라면 실패
            return false;
        }
        else { // 루트 노드가 0이라면 서브트리 계속 확인
            return isZeroTree(left) && isZeroTree(right);    
        }
    }
    
    public String plusZero(String binary) {
        int size = binary.length();
        int level = 1;
        int count = 1;
        while(size > count) {
            count+=Math.pow(2,level);
            level+=1;
        }
        int dif = count-size;
        for(int i=0; i<dif; i++) binary = "0"+binary;
        return binary;
    }
    
    public String toBinary(long num) {
        String binary = "";
        Stack<Long> st = new Stack<>();
        long res = 0;
        while(num != 0) {
            res=num%2;
            num/=2;
            st.add(res);
        }        
        while(!st.isEmpty()) {
            binary+=st.peek(); // 비효율이긴 함
            st.pop();
        }
        // 2. 이진화 앞에 '0'최대 붙이기
        binary = plusZero(binary);

        return binary;
    }
    
    public int[] solution(long[] numbers) {
        int[] answer = new int[numbers.length];
        
        for(int i=0; i<numbers.length; i++) {
            // 1. 이진화 (+"포화 이진트리")
            String binary = toBinary(numbers[i]);
            //System.out.println(binary); // debug
            // 3. 이진트리 판별
            Boolean result = isBinaryTree(binary);
            if(result) answer[i] = 1;
            else answer[i] = 0;
        }
        
        return answer;
    }
}
```

<br>**<br>**

## **느낀점**

풀이는 좀 비슷하게 생각했었지만 구현을 전혀 못했고, 구글링의 힘을 빌렸다.

이진트리 순회와 분할정복은 잘 아는 내용이었는데도 구현이 어려웠다.

다시 풀어보자.
