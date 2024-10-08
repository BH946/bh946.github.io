---
title: "3일차-하이퍼레저 베수 private network"
categories : BlockChain
tag : [블록체인, 하이퍼레저 besu, metamask, dapp]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**하이퍼레저 베수 private network 구축을 해보고 Metamask로 확인을 해보자. 그리고 블록체인 기반의 DAPP 시스템의 구조를 생각해본다.**

이전에 `이더리움`에서 구축한 private 네트워크는 메타마스크에서 확인까지는 안 했지만,  
이번 `베수` 로 구축한 private 네트워크는 메타마스크까지 전부 확인해 보겠다.

**참고 용어 : address, ciphertext => public key, private key**

- 메타마스크에선  
  `private key` 사용해서 계정 가져왔고(`지갑`을 가져온 것이다)  
  `public key` 사용해서 블록 주고 받았다(`거래`)

<br>

<br>

## 사용 명령어

**사용 명령어**

```bash
java -version

ls
./gradlew installDist
=> 빌드 시작(오래걸린다)

besu 환경변수 넣기(PATH)
C:\Users\KoBongHun\Desktop\besu\build\install\besu\bin


node1 
address: 0x2941F305FA0263758449Cd541e4aa1b5F54a309e
private_key: 13762785b5410dd75b540a313ad3f7d25c3fe99d1aed6e21ac1a09655375175e

node2
address: 0x3Fc35814481F7708F2a4c1aCe331Be0F2Eae550a
private_key: 81fddde495717b959f8a1a922a86bf66267896737fad71aea48d6f38826c6b70

node3
address: 0x9Eff2E4f87eaAC27b446F9FA6d22c8031C8076e1
private_key: e84e1b26c9bd988b1fd4dbf8ffb7cfda031c350351a4c74f288a264bf1cbd204

node4
address: 0xe0bC3a5e976AC8CEd3a4B208954b08F4Cf2Cbc51
private_key: d8c964cd27e37a7e1ecf34e874d9db56fa6d02a7e9fee30e86f0b646cad8ad5b

============================================

besu operator generate-blockchain-config --config-file=ibftConfigFile.json --to=networkFiles --private-key-file-name=key
./besu operator generate-blockchain-config --config-file=~~경로~~~~/ibftConfigFile.json --to=~~~경로~~~~/networkFiles --private-key-file-name=key

=============================================

besu --data-path=data --genesis-file=../genesis.json --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all"
./besu --data-path=C:/Users/...경로/node1/data --genesis-file=C:/Users/...경로/genesis.json --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all"

node1 enode
Enode URL enode://f9e20f036c9b8a1f5335e5a76f36194ff0acdefbaef30a43a1573f4e4e8b8b98dc1cca84a4317648ef1ed02d904cc922d51d5aa390af331ee8029c6d123cf9cd@127.0.0.1:30303

node1 execution command
besu --data-path=data --genesis-file=../genesis.json --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all"

node2 execution command
besu --data-path=data --genesis-file=../genesis.json --bootnodes=enode://f9e20f036c9b8a1f5335e5a76f36194ff0acdefbaef30a43a1573f4e4e8b8b98dc1cca84a4317648ef1ed02d904cc922d51d5aa390af331ee8029c6d123cf9cd@127.0.0.1:30303 --p2p-port=30304 --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8546

node3 execution command
besu --data-path=data --genesis-file=../genesis.json --bootnodes=enode://f9e20f036c9b8a1f5335e5a76f36194ff0acdefbaef30a43a1573f4e4e8b8b98dc1cca84a4317648ef1ed02d904cc922d51d5aa390af331ee8029c6d123cf9cd@127.0.0.1:30303 --p2p-port=30305 --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8547

node4 execution command
besu --data-path=data --genesis-file=../genesis.json --bootnodes=enode://f9e20f036c9b8a1f5335e5a76f36194ff0acdefbaef30a43a1573f4e4e8b8b98dc1cca84a4317648ef1ed02d904cc922d51d5aa390af331ee8029c6d123cf9cd@127.0.0.1:30303 --p2p-port=30306 --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8548
```

<br>

<br>

## 하이퍼레저 베수 네트워크 구축

이전에 한 `이더리움에서 private 네트워크 구축`과 `하이퍼레저 베수 private 네트워크 구축`의 흐름은 매우 유사하므로 전혀 어렵지 않다.

<br><br>

### Java설치

* [공식문서](https://www.oracle.com/java/technologies/downloads/#java18)
* JDK 18버전으로 설치하겠다.

<br><br>

### GIT

* [하이퍼레저 베수 GIT](https://github.com/hyperledger/besu)
* 22.1.2 버전으로 하겠다 => 브랜치에서 변경
* 다운받은 폴더에 `git bash`로 들어가서` ./gradlew installDist` 명령어 입력(설치시작)
  * 다운 시간이 좀 걸리며, 에러의 원인은 대부분 JDK문제

<br><br>

### 환경변수 설정

* `build` 폴더가 새로 생기며, `build/install/besu/bin` 경로로 들어가서 해당 경로 복사해서 환경변수에 사용한다.
  * 바로 실행해 보는 건 `./bseu 명령어`

* 설치가 끝나고 나면 `besu 명령어`로 **실행**할 수 있는데,  
  이를 항상 `cd`로 **접근**해서 하는 건 귀찮기 때문에  
  `환경변수`로 경로를 설정해서 `besu 명령어`를 **어디서든 실행** 가능하게 하는 것이다.

<br>

**아래는 윈도우10 기준이다.**

![환경변수](/images/2022-07-20-3일차/image-20220720113644585.png)

<br>

**보통 사용자 변수를 먼저 수정해 보는 걸 추천하고, 그래도 안 된다면 시스템 변수를 건드린다.**

![환경변수2](/images/2022-07-20-3일차/image-20220720113814879.png)

<br>

**이런 식으로 besu 경로를 설정하면 된다.**  
**예시는 현재 바탕화면(Desktop)에 있는 besu 경로를 설정한 것이다.**

![환경변수3](/images/2022-07-20-3일차/image-20220720113912847.png)

<br>

**마지막으로 터미널에 besu 명령어 입력 시 많은 문자들이 주르륵 나오면 성공이다.**

<br><br>

### 폴더 생성

![노드 폴더 세팅](/images/2022-07-20-3일차/image-20220720130736740.png)

<br><br>

### Account 생성

* 이전(이더리움)처럼 하는 게 기본이고, 아래 사이트 이용하는 건 연습용으로 사용하면 된다.  
  (또다른 new account 하는 방법이다)
* 본인은 이전 포스팅처럼 new account를 하겠다.

![계정 생성](/images/2022-07-20-3일차/image-20220720130911882.png)

![계정 체크](/images/2022-07-20-3일차/image-20220720131111627.png)

<br><br>

### genesis.json

명칭은` ibftConfigFile.json`로 하겠다. 왜냐하면 key파일 생성하면 `genesis.json`으로 생성될 거기 때문에 파일명 안 겹치기 위해서 파일명을` ibftConfigFile.json` 이것으로 하겠다는 것이다.

* [참고문서](https://besu.hyperledger.org/en/stable/Tutorials/Private-Network/Create-IBFT-Network/)
* Atom을 사용하겠음
* 각각의` address, private` 수정, `chainId`도 수정
  * `address` 수정 때 앞에 0x는 제외하고 붙여 넣어 줄 것
  * `balance` 값이 node1은 200, node2는 90000 ... 이런 식으로 값이 블록에 담겨서 전송된다.
  * 90000000000000000000000인 이유는 18자리 이기 때문이며 즉, 90000 ETH를 의미한다.

```json
// genesis.json 예시
{
 "genesis": {
   "config": {
      "chainId": 719,
     "londonBlock": 0,
      "ibft2": {
        "blockperiodseconds": 2,
        "epochlength": 30000,
        "requesttimeoutseconds": 4
      }
    },
    "nonce": "0x0",
    "timestamp": "0x58ee40ba",
    "gasLimit": "0x47b760",
    "difficulty": "0x1",
    "mixHash": "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {
       "2941F305FA0263758449Cd541e4aa1b5F54a309e": {
          "privateKey": "13762785b5410dd75b540a313ad3f7d25c3fe99d1aed6e21ac1a09655375175e",
          "balance": "0xad78ebc5ac6200000"
       },
       "3Fc35814481F7708F2a4c1aCe331Be0F2Eae550a": {
         "privateKey": "81fddde495717b959f8a1a922a86bf66267896737fad71aea48d6f38826c6b70",
         "balance": "90000000000000000000000"
       },
       "9Eff2E4f87eaAC27b446F9FA6d22c8031C8076e1": {
         "privateKey": "e84e1b26c9bd988b1fd4dbf8ffb7cfda031c350351a4c74f288a264bf1cbd204",
         "balance": "50000000000000000000000"
       },
       "e0bC3a5e976AC8CEd3a4B208954b08F4Cf2Cbc51": {
         "privateKey": "d8c964cd27e37a7e1ecf34e874d9db56fa6d02a7e9fee30e86f0b646cad8ad5b",
         "balance": "40000000000000000000000"
       }
      }
 },
 "blockchain": {
   "nodes": {
     "generate": true,
       "count": 4
   }
 }
}
```

<br><br>

### ibftConfigFile.json 기반 Key 파일 생성

* 해당 폴더에서 터미널로 아래 명령어 실행 후 `networkFiles 폴더가 생성`된다.

<br>

**networkFiles 폴더 생성 - 환경변수 한 경우**

```
besu operator generate-blockchain-config --config-file=ibftConfigFile.json --to=networkFiles --private-key-file-name=key
```

<br>

**networkFiles 폴더 생성 - 환경변수 안 한 경우**

```
./besu operator generate-blockchain-config --config-file=~~경로~~~~/ibftConfigFile.json --to=~~~경로~~~~/networkFiles --private-key-file-name=key
```

<br>

`networkFiles` 폴더 안에 생성된 `genesis.json` 파일을 아래 사진처럼 위치 옮길 것.

![json파일 세팅](/images/2022-07-20-3일차/image-20220720135246377.png)

<br>

`networkFiles` 폴더 안에 `keys`폴더 안에 있는 `key파일`들은 각각 `node`폴더 안에 `data` 폴더에 이동

![폴더트리 구조](/images/2022-07-20-3일차/image-20220720135355399.png)

<br>

**파일 구성된 모습**

![파일구조 모습](/images/2022-07-20-3일차/image-20220720135456266.png)

<br><br>

### enode

이전에 이더리움에서 한 것처럼 enode도 설정해야 하는데,  
**여기선 노드1의 enode만 설정하면 알아서 서로 연결한다!**

**실행 명령어**

```bash
besu --data-path=data --genesis-file=../genesis.json --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all"

만약 환경설정 아닌경우는? 아래코드
./besu --data-path=C:/Users/...경로/node1/data --genesis-file=C:/Users/...경로/genesis.json --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT --host-allowlist="*" --rpc-http-cors-origins="all"
```

<br>

**node1 enode**

* 기억해 두고 활용할 것이다.

![node1 enode](/images/2022-07-20-3일차/image-20220720143204929.png)

<br><br>

### 노드 실행 명령어 만들기1

* node1은 위의 실행명령어를 그대로 사용  
* node2부터는 중간에 --bootnodes에 node1의 enode를 넣어서 연결(나머지 노드 마찬가지)
  * 참고로 port는 다 다르게 작성할 것
  * bootnodes를 시작점이라고 해석해서 기억하면 된다.

![노드 실행 명령어](/images/2022-07-20-3일차/image-20220720143827198.png)

<br><br>

### 참고사진

* `왼쪽 그림`은 이전에 한 `이더리움(geth) 연결`같은 형태
  * 서로 다른 node들이 서로 요청 응답을 통해서 연결하는 것이다.
* `오른쪽 그림`은 오늘 한 `베수 방식 연결` 형태이다. 
  * 하나의 서버를 통해 탐색하는 것이다.
  * 잘 보면 서버 역할이 node1인 것이다.

![geth와 besu 방식](/images/2022-07-20-3일차/image-20220720144042127.png)

<br>

<br>

## POSTMAN으로 데이터 확인

* postman은 api 테스트할 때 많이 사용해 봤으니 무엇인지 설명은 생략

<br>

[postman fork](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/) 사이트에서 -> run postman -> fork

그리고 postman 실행해 보면

![postman 데이터 확인](/images/2022-07-20-3일차/image-20220720153922207.png)

<br>

이처럼 fork한 것을 알 수 있고, 이를 통해 테스트해 볼 수 있다.

어떻게 보면 node 실행한 것들이 블록체인으로 서버 DB가 된 거라 볼 수 있다.

<br>

실제 테스트해 보면(8545로)  
아래처럼 블록을 가져온 것을 볼 수 있다.

![postman 데이터 확인2](/images/2022-07-20-3일차/image-20220720154955320.png)

<br>

<br>

## MetaMask로 테스트

**우선 네트워크를 추가해 준다.**

* `8545`는 `node1`이다. 
  (노드는 컴퓨터 한 개라고 했음. 임의로 node4개를 돌리고 있는 건데 컴퓨터 4개를 돌리고 있는 것이다)
* 체인ID는` genesis.json`에 만든 `chainId`를 의미한다.

![Metamask 테스트](/images/2022-07-20-3일차/image-20220720160444143.png)

<br>

**그 후 계정을 가져온다(지갑 가져오기)**

* node1과, node2의 지갑을 가져오겠다(`Private Key`활용)

<br>

**그리고 node1의 블록을 node2로 `10 SEO`만큼 보내겠다(전송 테스트)**

* 이때는 `Public Key`를 활용한다

<br>

**처음 `200 SEO`와 `90000 SEO`가 아래처럼 잘 변경되었다.**

![Metamask 테스트2](/images/2022-07-20-3일차/image-20220720161229430.png)

![Metamask 테스트3](/images/2022-07-20-3일차/image-20220720161453616.png)

<br>

<br>

## 블록체인 기반의 DAPP 시스템

* SC = 스마트컨트랙트, BC = 블록체인을 의미

* 강사님은 블록체인 기반의 앱 시스템은 아래처럼 생각하신다고 하신다  
  무조건 옳다는 건 아니고 강사님의 경험상 아래 형태라고 하셨다

**큰틀 : BC <----> SDK <----> 화면**

**세부사항 : BC <----> SC <---> SDK <---> Node.js <---> UI <---> 사용자**

우리가 지금 실습한 건 BC부분인 블록체인 서버 부분이라 생각한다.

<br>

<br>

## 질의응답

`genesis.json`에 정의한 `balance` 외에 다른 값도 보낼 수 있냐는 질문에 대한 강사님이 답변

- `비트코인`은 `스마트컨트랙트`가 없어서 **1대1**로 주고받는데,  
  `이더리움`은 `스마트컨트랙트`가 있어서 코인 외의 **사용자 정의 데이터** 또한 보낼 수 있다고 한다.  
  파일 자체를 아예 줄수있는건 아니고, **스마트컨트랙트 형태**로 보내고 받는다고 한다.  
  즉, "hello world"를 그대로 보내진 않고 디코드, 인코드해서 **다른 형태**로 주고받는다.  
  요즘은 모르지만, 이전엔 32KB 용량 이상이 안 되는 거로 알고 있다. 글자는 10000자 정도까지였다.   
  따라서 사진을 보내고 싶어도 요즘 사진은 10M 이렇게 되니 사진 데이터를 넣어 보내기 어렵다.  
  다만, 32KB 이내인 간단한 데이터는 충분히 이더리움에 보낼 수 있다.   
  => ex) NFT의 유명한 [opensea](https://opensea.io/explore-collections) 생각해 보면, Img 파일 저장된 곳 URL을 이더리움에 데이터로 넣어서 활용했다.  
  그래서 큰 데이터는 나눠서 받는 기법을 사용해서 토렌트처럼 조각을 내서 하겠다는 얘기는 예전부터 있는데, 아직 해결되진 않았다고 한다. 즉, 이론적으론 가능한 일이 많은데 실제론 아직 안 되는 일들이 많다고 한다.

- 블록체인 기업 취업 준비를 원한다면??  
  포트폴리오를 지갑 정도 만들면 좋을 것 같다고 한다.

- 블록체인 유명한 회사는?? - 람다256.. 등  
  대기업의 경우는 한 부서로 보통 운영...

- 괜찮은 회사인지 판단 법은??  
  그 기업이 뭘 하는지 뭘 개발할 거고, 블록체인은 기본적으로 본인들의 소스를 공유하는 게 암묵적 규칙인데 스마트컨트랙트 소스 정돈 공유했는지, 깃이 잘 있는지, 깃이 잘 업데이트가 되는지 등을 확인해서 안 좋은 기업 판별하는 것을 추천한다.

