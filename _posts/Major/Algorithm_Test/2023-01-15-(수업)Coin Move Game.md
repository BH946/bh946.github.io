---
title:  "[수업]Coin Move Game"
categories : [AlgorithmTest]
tag : [백준, 프로그래머스, 알고리즘 문제풀이]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



## Intro..

이 문제는 `Nim게임 + DP` 관련 문제이다.

`Nim게임 + DP` 관련해서 정리해둔 게시글이 있기 때문에 참고할 것.

<br>

## 문제

![screen captures](/images/2023-01-15-(수업)Coin Move Game/df29e17f-dac7-46e0-b1a5-91d0e98f370e.png)



![screen captures](/images/2023-01-15-(수업)Coin Move Game/550eb9dd-5219-413b-bde1-4444df1c722e.png)

<br>

## 풀이

이 또한 DP로 풀어주면 된다.   

알고리즘 과정 : 초기값 0, 1정도만 채워주고 뒤부터 규칙에 맞게 채워나가면 됨.   
=> 참고로 뒤에 길게 적다보면 P 주기마다 비슷한 형태로 반복되는것 같음

<br>

**아래는 DP배열 예시 `P=6, K=3, S=15`**

![image-20230611020208524](/images/2023-01-15-(수업)Coin Move Game/image-20230611020208524.png)

<br>

**위 구조를 나타낸 조건들 3가지**

* L, L은 무조건 W => 당연히 이전단계에서 무조건 진 경우면 다음단계는 이기니까   
* W, W는 무조건 L => 당연히 이전단계에서 무조건 이긴 경우면 다음단계는 지는 차례니까   
* L, W는 상대를 무조건 W로 보내는 선택을 할 수 있으니까 L로 설정해야함.   => 상대가 W가 되야 다음턴에 영희가 W될 차례니까

이방식대로 DP 그려나가면 된다.

<br><br>

## 추가정리

**최근 다시 이 문제를 풀어보았다.**

<br>

* **시작점(=S)이기 때문에 다음과 같은 특징이 있다.**

  * 아래 그림속에 마지막 15번째는 당연하게도 이전에 이동한 경로를 고려할 필요가 없다. 
    * K 경로 의미

  * 또한 X인지도 고려할 필요가 없다. 
    * P 배수 의미
  * 따라서 시작점(S)에서 K(1,2,3...K)만큼 이동한 요소들의 값으로 기록해준다.
    * 예로 아래 **12, 13, 14**열에 동그라미 되어있는것을 의미한다.

* **아래 배열속 요소들의 의미는??**
  * 각 요소들은 "영희(자신)"이 도착한 순간을 의미한다고 보면 된다.
    * 예시로 처음 0일때는 "영희"가 0에 도착한 순간이므로 "철수(상대)" 차례지만 무엇을해도 진다.
    * 따라서 "영희"가 무조건 승리하기 때문에 W(win)로 0열에 전부 채워진 것이다.  
      (참고로 L은 졌다는 의미)
  * `LL => W / WW => L / LW(LLL..W) => L` 형태로 예측이 된다.
    * 예시로 7열의 경우 K=1,2,3 때 요소의 정답 값은 L,L,W 이다.
      * DP\[1][7] 로 예를들면 "영희"가 1만큼 이동해서 7로 왔다는 의미(8->7)이고, 이때 다음 가능한 경로는 경로 중복 불가를 고려해야 하기 때문에  
      * DP\[1][?] 값들은 볼 필요가 없이 나머지 DP\[2][?], DP\[3][?] 들을 확인하면 된다.
      * 뒤에 ?는 DP\[2][7-2], DP\[3][7-3] 를 의미하며 이는 현재 7에서 2만큼 이동했을때 승패 값과 7에서 3만큼 이동했을때 승패 값을 의미한다.
      * 이때는 "영희"가 아닌 "철수"의 차례이므로 DP\[2][5], DP\[3][4]의 값인 W, L 중에서 당연히 "철수"는 자신이 이기기 위해서 W(=DP\[2][5]) 를 선택하는게 자명하다.
      * 따라서 "영희"는 질수밖에 없기 때문에 DP\[1][7] 의 값은 L이 되는것이다.
      * 이것은 "LW" => "L" 형태로 예측한 하나의 예시였다.

![image-20230611020208524](/images/2023-01-15-(수업)Coin Move Game/image-20230611020208524.png)

<br>

```java
/**
 * LL => W
 * WW => L
 * LW(WL) => L
 * 즉, LLLLW => L
 */
static int[][] dp = new int[8][1000005];

public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer stk;
    StringBuilder sb = new StringBuilder();
    int T = Integer.parseInt(br.readLine());
    for(int t = 0 ; t<T; t++) {
        // init
        for(int i =0; i<8; i++) Arrays.fill(dp[i], 0);
        // input
        stk = new StringTokenizer(br.readLine(), " ");
        int p = Integer.parseInt(stk.nextToken());
        int k = Integer.parseInt(stk.nextToken());
        int s = Integer.parseInt(stk.nextToken());
        // run
        // 현재 dp init은 0이고, p배수도 0으로 할거라 미리 바꿀건 없음
        for(int i=1;i<=k;i++) dp[i][0] = 1; // 0으로 도착한 경우 당연히 승리(1)
        for(int j=1; j<s; j++) {
            for(int i=1;i<=k;i++) {
                // p배수 확인
                if(j%p==0) break;
                if(j>p) {
                    if(j == p*(j/p)-i) continue;
                }
                else{
                    if(j == p-i) continue;
                }
                // dp update => 0만 있으면 W(1), 1이 하나라도 있으면 L(0)로 갱신
                dp[i][j] = 1; // 따라서 먼저 1로 업데이트 후 진행
                for(int l=1; l<=k; l++) {
                    if(l==i) continue; // i만큼 이동해서 왔으므로 i는 제외
                    if(j-l<0) continue; // 0보다 작으므로 전부 x(=L=0)
                    if(dp[l][j-l] == 1) {
                        dp[i][j] = 0;
                        break;
                    }
                }
            }
        }
        // 마지막 s도 채우기
        for(int i = 1; i<=k; i++) {
            dp[i][s] = dp[i][s-i];
        }
        // output
        int result = -1;
        for(int i = 1; i<=k;i++) {
            if(dp[i][s] == 1) result = Math.max(result, s-i);
        }
        sb.append(result).append('\n');
    }
    System.out.println(sb);
}
```

