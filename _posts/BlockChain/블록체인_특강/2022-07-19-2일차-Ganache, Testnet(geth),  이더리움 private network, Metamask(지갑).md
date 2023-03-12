---
title: "2일차-Ganache, Testnet(geth), 이더리움 private network, Metamask(지갑)"
categories : BlockChain
tag : [블록체인]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



## 서론

`Ganache 가상 머신` 과 `Testnet(geth) 네트워크` 와 `이더리움 private network 구축` 을 실습하겠다.

추가로 어디서든 다 사용하는 `Metamask`  또한 실습하겠다. (지갑 같은것)

<br>

**Testnet, private network 때 사용한 명령어 모음**

* 네트워크 구축할 때 필요한 정보들이 많아서 메모장에 따로 저장해두는걸 추천

```bash
===========================================
<Testnet>
geth --datadir ./datadir/ account new
password는 그냥 1234로 하겠다.

"ciphertext" : "99e2a51a9d8fcccd759fd7254a8b881b6c65c94cedb9d1e2ad8eb46e75b57c70"
===========================================
geth ==> mainnet 실제로 사용되는 ethereum network

geth --ropsten --datadir C:\Users\KoBongHun\Desktop\eth_testnet\datadir\ --http --http.addr 0.0.0.0 --http.corsdomain * --allow-insecure-unlock --http.api eth,net,web3,personal
===========================================
<private Network>
C:\Users\KoBongHun\Desktop\eth_private\keystore
geth --datadir C:/Users/KoBongHun/Desktop/eth_private/keystore account new
=> 4번반복 생성 ! account 생성임여긴
[node1]
fb713e8c7e49986ea4192af7f988ef3aa22c79ff
0e9546db9f3190afd57a8c2cd6c7cfe11e25c2e5accade752b3640a205a600f1

[node2]
40132c811cbab185a9bcc42dccbdaa4bf20e151f
325193143ae69ff753351d560d313a1b7dac43e410ad807e1a91d2c671fd354b

[node3]
df033f904ae119c725b5220eb573b19fe22b9897
e0c1b0b60b94f7e8a8de4fbad376edb4a5afe6e3e164db00bad30c27388b7cca

[node4]
ce943b341fbf8781961e8c2dc1f48f469da9871f
9bfe611f1be6a22983745e43b6a763a3e6a43e3c036463b0551fcc3da704328f
=======================================================
node1
geth --datadir C:/Users/KoBongHun/Desktop/eth_private/node1 init genesis.json

node2
geth --datadir C:/Users/KoBongHun/Desktop/eth_private/node2 init genesis.json

node3
geth --datadir C:/Users/KoBongHun/Desktop/eth_private/node3 init genesis.json

node4
geth --datadir C:/Users/KoBongHun/Desktop/eth_private/node4 init genesis.json
=======================================================
<생성 끝났으니 네트워크 실행>
node1
geth --networkid 719 --datadir C:/Users/KoBongHun/Desktop/eth_private/node1 --ipcdisable --http.port 8545 --ws.port 8546 --http.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --ws.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --mine --port 30304 --allow-insecure-unlock --nat=extip:127.0.0.1 console

node2
geth --networkid 719 --datadir C:/Users/KoBongHun/Desktop/eth_private/node2 --ipcdisable --http.port 8555 --ws.port 8556 --http.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --ws.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --mine --port 30305 --allow-insecure-unlock --nat=extip:127.0.0.1 console

node3
geth --networkid 719 --datadir C:/Users/KoBongHun/Desktop/eth_private/node3 --ipcdisable --http.port 8565 --ws.port 8566 --http.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --ws.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --mine --port 30306 --allow-insecure-unlock --nat=extip:127.0.0.1 console

node4
geth --networkid 719 --datadir C:/Users/KoBongHun/Desktop/eth_private/node4 --ipcdisable --http.port 8575 --ws.port 8576 --http.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --ws.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --mine --port 30307 --allow-insecure-unlock --nat=extip:127.0.0.1 console
===========================================================
enode는 해당 노드의 주소(admin검색해서 따온것이다.)
node1
"enode://699595a0b915e25494856e17d73eb53720e0e74158ce20c46806b206ccfc5c2dbe1b59f2aa2d34d9d1261536a80b0aff6468a233c981caade8a4923317eaa8b6@127.0.0.1:30304"

node2
"enode://c2e320e4c1f003dd6f8b4479727ae63c69a30236df4d12642f70bdda30b35c9e3faaef9f008ede978826eb14baf70f02bf016766784a1884d91705bf51023ea0@127.0.0.1:30305"

node3
"enode://5f72d076112f1ca24c3540449e11e324e64c85233565db0fd3b5b89a7c44ae810fcaf6d5b91e4d53a614e8bd93a626cd8ca10592cfb0a262ebbca245efd3cbc5@127.0.0.1:30306"

node4
"enode://9eaf7407537e55b5ea586ad19f8d220a97c5665bcff35afa3bb476efe4b3ff75c1a027ef3a9ba119953659dc7c9cf7ce7948aff7f4a36182f7bec84cd6fa513e@127.0.0.1:30307"
========================================================
노드 2,3,4 terminal (노드 1 enode 정보)
admin.addPeer("enode://699595a0b915e25494856e17d73eb53720e0e74158ce20c46806b206ccfc5c2dbe1b59f2aa2d34d9d1261536a80b0aff6468a233c981caade8a4923317eaa8b6@127.0.0.1:30304")

노드 3,4 terminal (노드 2 enode 정보)
admin.addPeer("enode://c2e320e4c1f003dd6f8b4479727ae63c69a30236df4d12642f70bdda30b35c9e3faaef9f008ede978826eb14baf70f02bf016766784a1884d91705bf51023ea0@127.0.0.1:30305")

노드 4 terminal (노드 3 enode 정보)
admin.addPeer("enode://5f72d076112f1ca24c3540449e11e324e64c85233565db0fd3b5b89a7c44ae810fcaf6d5b91e4d53a614e8bd93a626cd8ca10592cfb0a262ebbca245efd3cbc5@127.0.0.1:30306")

다하고 채굴시작은? miner.start()이고, 멈추는건 miner.stop()이다.
다시 네트워크 구성하고싶다? geth폴더만 지우고 다시 시작하면 된다.


// 수정
노드 start 시킬때 
geth --networkid 719 --datadir /Users/a10403/Desktop/eth_private/node1/ --port 30304 --ipcdisable --syncmode full --http --http.addr 0.0.0.0 --http.api admin,eth,miner,net,txpool,personal,web3 --allow-insecure-unlock --http.corsdomain "*" --http.vhosts "*" --http.port 8545 --mine --nat=extip:127.0.0.1 console
명령어로 수행하면 metamask에서 rpc url (http://127.0.0.1), 체인 id (719), 통화기호 (ETH)로 생성 잘 됩니다.
```

<br><br>

## Ganache 가상 network 구축

* Ganache는 이더리움 도구이며, 실행시 가상 머신으로서 자동으로 네트워크 구축을 해준다.
* Metamask로 거래까지 테스트

<br>

### Ganache 설치

* [설치(이더리움 도구)](https://trufflesuite.com/ganache/)
* 개발 테스트를 위한 **가상 머신 프로그램** - **네트워크 구축** 하는것이다.

<br>

**실행시 이렇게 이더리움 서버를 구동하는 것이다.**

![image-20220723195142323](/images/2022-07-19-2일차-이더리움 private network 구축 + 테스트넷/image-20220723195142323.png)

<br>

**이렇게 구동하기 위해선 프로젝트 생성을 해야한다.**  

* **NEW WORKSPACE인 EHEREUM**으로 생성

![image-20220723200044384](/images/2022-07-19-2일차-이더리움 private network 구축 + 테스트넷/image-20220723200044384-16585740597561.png)

<br>

**먼저, 프로젝트 명 설정**

![image-20220719101215601](/images/2022-07-19-2일차/image-20220719101215601.png)

<br>

**서버 설정**

* **HOSTNAME**에 0.0.0.0이나 현재 사진처럼 선택하면 localhost로 접근할 수 있다.
* **PORT**는 하고싶은것 하면된다.  
  만약 7545라면 URL을 http://localhost:7545 로 접근해야 하는 것이다.
* **NETWORK ID**도 원하는것으로 하면되고 **체인 ID**라고도 부른다.

![image-20220723195659219](/images/2022-07-19-2일차-이더리움 private network 구축 + 테스트넷/image-20220723195659219.png)

<br>

**ACCOUNT 와 KEYS 설정**

* **ACCOUNT DEFAULT BALANCE** 에는 각 블록이 기본값으로 보유하는 ETH의 개수(잔고) 설정

* **TOTAL ACCOUNTS TO GENERATE** 는 총 생성할 **노드(컴퓨터) = ADDRESS(계정)** 개수



![image-20220719101145501](/images/2022-07-19-2일차/image-20220719101145501.png)

<br>

**가스(=수수료) 설정**

* 이더리움에 `GAS 단위`는 **gwei**이다. 이것은 **0^9 wei**이다.  
  예로 **0.000000001 이더 = 1gwei** 라고 말할 수 있다.
* **GAS USED(사용한 가스, 가스 한도)**는 데이터 크기나 등등에 따라 다르며 보낼때 결정된다.

<br>

`첫번째 사진`은 가스(=수수료) 계산이 어떻게 되는지 이해가 안갈것 같아서 예시로 보여준 사진이다.

* **GAS USED = 21000 gwei = 0.000021 ETH**
* **GAS PRICE = 20000000000 gwei = 20 ETH**
  * **Total GAS = 0.000021 ETH x 20 ETH = 0.00042 ETH**
  * **총 합계 수수료 = GAS USED x GAS PRICE** 이다.

<br>

`두번째 사진`은 Ganache에 가스 설정하는 모습이다. (발신자가 정한 가격들이다)

* **GAS LIMIT(최대 가스 제한)**이 **6721975 gwei = 0.006721975 ETH**
* **GAS PRICE(가스 가격)**은 **20000000000 gwei = 20 ETH**



<img src="/images/2022-07-19-2일차-이더리움 private network 구축 + 테스트넷/image-20220723223054338.png" alt="image-20220723223054338" style="zoom:80%;" />



![image-20220723200353453](/images/2022-07-19-2일차-이더리움 private network 구축 + 테스트넷/image-20220723200353453.png)

<br>

**저장후 실행화면**

* **노드(컴퓨터) = ADDRESS(계정)** 들이 지정한 20개가 나타난다.
* Metamask 처럼 거래는 여기서 불가하고, 네트워크 및 지갑 정도의 역할을 한다고 보면 된다.
* ADDRESS 아래가 Public Key => 거래할때 등등..  
  오른쪽 Key 모양 클릭시 Private Key => 지갑에 계정 가져올때 등등..

![image-20220719101240923](/images/2022-07-19-2일차/image-20220719101240923.png)

<br>

**트랜잭션이 해당 서버에 들어오면 아래화면에 블록이 생성이 될것이다.  **

![image-20220719101529412](/images/2022-07-19-2일차/image-20220719101529412.png)

<br>

### MetaMask 설치

* [설치 Metamask](https://metamask.io/download/) 크롬 확장프로그램이다.

**지갑 생성을 하면 된다.**

**지갑 복구 구문은 꼭 따로 기억해둬야 비밀번호를 잊어버려도 찾을 수 있다.**

**회원가입 하면 받을 것이니까 따로 메모장에 기억해 둘것**

<br>

#### Ganache와 MetaMask를 네트워크를 서로 연동

![image-20220719102931358](/images/2022-07-19-2일차/image-20220719102931358.png)

![image-20220719102958932](/images/2022-07-19-2일차/image-20220719102958932.png)

![image-20220719103028497](/images/2022-07-19-2일차/image-20220719103028497.png)

<br>

**아래 네트워크 구성은 Ganache의 RPC SERVER, NETWORK ID 를 참고해 기입한다.**

![image-20220719103445485](/images/2022-07-19-2일차/image-20220719103445485.png)

<br>

#### 계정 가져오기 테스트

* **계정 가져오기 클릭 후 비공개 키에 Ganache의 private key를 기입**

![image-20220719104018972](/images/2022-07-19-2일차/image-20220719104018972.png)

<br>

**Ganache private key 예시**

![image-20220719103926870](/images/2022-07-19-2일차/image-20220719103926870.png)

<br>

#### 보내기 테스트

* **Account 1**에게 우리가 가져온 이더리움을 **100 ETH**정도 보내는 테스트 진행
* 이때 Account 1의 주소는 Ganache에서 Public key이며  
  Metamask에 Account1 계정 메인에서 보이는 key도 동일한 Public key이며 이를 사용

![image-20220719104349683](/images/2022-07-19-2일차/image-20220719104349683.png)

<br><br>

## Testnet 접속

* [geth 설치](https://geth.ethereum.org/downloads/)
* 경로, 설정은 웬만하면 **default**로
* **백신** 등에 의해 다운이 안될 수 있으니 꼭 잠시 **꺼두기**

설치가 끝나고 나면 관지라 권한으로 **powershell**이든 **터미널**이든 실행시켜서  
**geth 명령어 입력** 후 실행이 잘되면 반드시 꺼주기. Ctrl+C로 종료할 수 있다.  
**메인넷**을 사용할건 아니기 때문에 종료하는 것이다.  
**geth ==> mainnet(메인넷) 이며 실제로 사용되는 ethereum network 이다.**

<br>

### ERROR

경로가 없다고 에러 뜬다면 **C:\Program Files\Geth** 에 **Geth**가 잘 다운되었는지 먼저 확인후 **환경변수에 해당경로를 추가**해야한다.  
이때, 꼭 터미널은 다시 재접속을 권장한다.

![image-20220719105428029](/images/2022-07-19-2일차/image-20220719105428029.png)

<br>

### 이더스캔을 통해 이더리움 확인

* 터미널에` geth --datadir ./datadir/ account new` 입력  
  경로는 아무곳이나 상관없으며 git bash 사용을 추천
* `account`를 만든것이고, 해당 폴더에 파일하나 있는데 메모장으로 들어가서  
  `ciphertext부분 값`을 가져와 사용(`private key이다`)
* `MetaMask`에서 계정 가져오기를 이용해 위에서 구한 키를 사용해 `account`를 가져오면 된다.
  * 참고로 이더리움 테스트용으로 받아오는건 Metamask에서 구매에도 테스트가 있어서 구매를 하면 된다.
* 네트워크는 테스트를 위해 `Ropsten 테스트 네트워크로 변경`한다.
* [이더스캔의 ropsten](https://ropsten.etherscan.io/address/0x91a11E6283c361e54D6b0d22199166131748BB03) 접속을 해서 이더리움 볼 수 있으며, 이것은 **테스트 넷**이며 메인넷은 당연히 아니다.

<br>

**아래 사진은 구매를 통해 2 RopstenETH를 얻은 모습이다.**

![image-20220719113304647](/images/2022-07-19-2일차/image-20220719113304647.png)

![image-20220719113316863](/images/2022-07-19-2일차/image-20220719113316863.png)

<br>

### 테스트넷 동기화

* `이더 스캔`을 통해 UI로 보았다면, 개발자 모드처럼 개발자들이 보는 형태인 `터미널을 통해` 볼 수 있다.

* 터미널에 다음 명령어를 실행한다. 경로문제는 항상 조심!!

  ```bash
  geth --ropsten --datadir C:\Users\KoBongHun\Desktop\eth_testnet\datadir\ --http --http.addr 0.0.0.0 --http.corsdomain * --allow-insecure-unlock --http.api eth,net,web3,personal
  ```

* 에러가 발생한다면, 명령어 문제 확인.  
  명령어는 바뀔수 있기 때문에 geth help입력해서 명령어 바뀐게 있나 확인 추천.  
  또는 [공식문서](https://geth.ethereum.org) 에서 명령어 확인 추천.

`Looking for peers`를 보면 어디 위치에서 찾고있는지 알 수 있고,  
`Imported new block ...` 이런식으로 나오기 시작하면 동기화가 성공한것이다.   
테스트넷 구동중인 노드(컴퓨터)와 연결하는 것이니 상당히 오래 걸린다.

<br>

**새로운 콘솔창 띄우고**

* `geth attach http://localhost:8888` 입력시 블록 현황 볼 수 있다.
* 안되는 경우도 많은데 본인 컴퓨터의 라우터 문제일수도 있고, 명령어를 잘 못 입력했을수도 있기때문에 다양한문제가 있다.
* 우선 나는 undefined라고 떳지만, 예전에 라우터 등을 건드린 문제라고 생각하기 때문에 pass하겠다.

![image-20220719115250262](/images/2022-07-19-2일차/image-20220719115250262.png)

<br><br>

## 이더리움 Private Network 구축

* 아래처럼 폴더구성  
  node1, 2, ... 는 각 컴퓨터마다 가진 노드라고 생각하면되고, 우리는 임의로   
  **한개의 컴퓨터에 4개를 다 생성한것.**
* 따라서 **4번** `geth account new`를 해서 해당 키를 정리해서 따로 기록해둘것.
* 각각 node폴더마다 keystore 폴더도 만들고 만들어진 UTC들도 각각에 한개씩 넣어주기

![image-20220719133327805](/images/2022-07-19-2일차/image-20220719133327805.png)

<br>

**정리화면**

* 첫줄은 `address`값, 2번째줄은 `ciphertext` 값이다. - 각각 Public key, private key

![image-20220719133507886](/images/2022-07-19-2일차/image-20220719133507886.png)

<br>

### Atom 설치 및 JSON

* [Atom](https://atom.io/) 에서 설치 및 실행(단순히 JSON파일 작성하기 편하려고 사용하는것 뿐임)
* `genesis.json` 하나 생성 하고 코드 작성
  * `alloc`는 `address`값 - public key
  * `chainId` 도 자주 사용하니까 기억해두기
  * 생성된 이 **json**파일도 **node의 각각 폴더마다 넣어주기**(keystore폴더와 동일한 경로)

![image-20220719141716908](/images/2022-07-19-2일차/image-20220719141716908.png)

<br>

**각 노드 폴더마다 터미널 접근(한개 터미널이 한개의 컴퓨터)**

* `geth --datadir 경로../node1 init genesis.json` 이런식으로 node4 까지 터미널에 입력

![image-20220719141348379](/images/2022-07-19-2일차/image-20220719141348379.png)

<br>

### 네트워크 실행(채굴)

* **그리고 각 노드마다 다음 명령어 사용(네트워크 실행)**

```
geth --networkid 719 --datadir C:/Users/경로../node1 --ipcdisable --http.port 8545 --ws.port 8546 --http.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --ws.api admin,db,eth,debug,miner,net,shh,txpool,personal,web3 --mine --port 30304 --allow-insecure-unlock --nat=extip:127.0.0.1 console
```

<br>

* enter입력하면 계속 다른것 입력 가능, admin 입력후 **enode정보** 가져올것.  
  **enode는 노드의 주소**라고 생각
* 그후 아래 그림처럼 구성하기 위해 명령어를 아래처럼 구성
  * 명령어를 해석하면 그림과 동일

![image-20220719153459628](/images/2022-07-19-2일차/image-20220719153459628.png)

![image-20220719154916597](/images/2022-07-19-2일차/image-20220719154916597.png)

=> 명령어 입력후 **true**가 나오면 **정상**

이작업이 끝나면 그림처럼 노드끼리 서로 연결된것이다.

<br>

* **miner.start()** 입력시 **채굴 실행** - 하나에만 하면 된다.
  * null 이후 Successfuly.... 등등 빠르게 동기화시작
  * 그러고 나면 블록 생성이되고, 이것을 다른 컴퓨터들에게도 전파를 시작한다.
  * 따라서 다른 터미널들도 블록을 받아 추가한다.
* **miner.stop()** 입력시 멈춤

![image-20220719163922867](/images/2022-07-19-2일차/image-20220719163922867.png)

**참고 : 첫번째 터미널이 채굴했던 노드이고, number이 600대이다. 저만큼 채굴한것이고, 나머지를 보면 200대인데 동기화때문에 조금 천천히 채굴한 블록들을 받아오고 있는 중인 것이다.**

<br>

### METAMASK에서 확인

* 앞에서 **Ganche**랑 했던것처럼 여기서도 **네트워크 추가** 및 **계정 가져온 후** 확인해볼 수 있다.
* **chainId**는 json에서 입력한 값
* **RPC URL**은 http://Localhost:8545 즉, 노드 4개중에 확인하고싶은 노드 한개 url 찾아 적으면 됨
* Ganache와 했던것과 동일하기 때문에 이부분은 생략하겠음.  
  대신 besu 구축할때는 다시 정리하겠다.



