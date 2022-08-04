---
title: "Rest API 만들기 & MERN"
categories : [React_JS, Node_JS]
tag : [Javascript, 리액트, Node, 노드, Nodejs, Express, Router, MongoDB(mongoose,NoSQL), JWT(토큰), CRUD with JWT]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



## 들어가기전..

**Mern이란?**  `MongoDB, Express, React, Node.js` 를 뜻하며  
이 네가지 요소를 사용해 웹사이트 개발을 의미

**Rest API이란?** [Rest API 게시글](/knowledge/GraphQL,-RestAPI/)  

* `Express 라이브러리` 활용해서 **서버 구동 및 Router** 함수 활용
  * `react-router-dom 라이브러리`를 활용한 것이 아닌 `Express 라이브러리`의 Router() 메소드 사용  
    [react-router-dom 게시글](/react_js/React-Router(라우터)) 참고
  * 즉, CRA처럼 리액트로 개발하는것이 아닌 Node js로 개발
* `MongoDB` 는 MySQL처럼 Database역할을 한다(NoSQL)  
  [MongoDB 사이트](https://cloud.mongodb.com) 참고 및 Node.js 와 connect
* 로그인 인증으로 `JWT` 방식의 토큰 활용
  * 로그인 정보 암호화 위한 해시 방식의 `CryptoJS` 활용
* `CRUD with JWT` 로 Rest API 구성 마무리

**API 구성을 위한 설치할 것들**

* `npm init`
* `yarn add express mongoose dotenv nodemon crypto-js jsonwebtoken`
  * `dotenv` 이란 env를 통해 보안키 같은것을 숨기기위한 라이브러리  
    React의 CRA에선 바로 env, gitignore활용 했는데, 이 방식도 있다는걸 참고
  * `nodemon` 이란 핫라인 같은 느낌으로 터미널 입력(node index.js)이 아닌, 코드 수정시 바로 반영.  
    `Code Runner 플러그인` 도 있긴 하지만, `nodemon` 도 충분히 편리해 보임.  
    **사용법**은 package.json에 `"start": "nodemon index.js"` 하고 터미널에 yarn start
  * `crypto-js` 란 암호화 하는 라이브러리라고 생각
  * `jsonwebtoken` 란 JWT를 활용하게 해주는 라이브러리이다.
* 추가로 `Postman` 활용해서 api 구동 확인하는것 추천

<br>

## Express(서버 구동)

**간단하며, 개념 및 사용법 알고 싶다면 추후에 JavaScript 카테고리에서 확인할 것**



### Router(라우터)

**클라이언트의 요청 경로(path)를 보고 이 요청을 처리할 수 있는 곳으로 기능을 전달해주는 역할**

**Express의 메소드인 Router()를 사용**  

* index.js : `app.use()`는 **start point**
* 라우터들.js : `router.get(), router.post() 등등..`은 **end point**
* 아래 코드 예시 : `http://localhost:5000/api/auth/register` 로 URI 형성된것

```js
// index.js
const userRoute = require("./routes/user") // user.js의 route
const authRoute = require("./routes/auth")

// ...

app.use(express.json()); // json 허용
app.use("/api/auth", authRoute)
app.use("/api/users", userRoute);

// ...
```

```js
// auth.js
const router = require("express").Router()

router.post("/register", async (req, res)=> {
	// ...
})

module.exports = router
```

<br>

## MongoDB

**MongoDB는 NoSQL이며, [MongoDB 사이트](https://cloud.mongodb.com) 에서 회원가입 및 프로젝트 생성해서 사용한다.**

**스타벅스에서 api 실행시 몽고DB 제대로 연결 안될수 있음. 스타벅스 공용 와이파이에서 막은듯 하다.**





### 세팅 방법

**New Project에서 프로젝트 새로 생성한다**

![image-20220804171010947](C:\Users\KoBongHun\Desktop\Git\BH946.github.io\images\2022-08-03-Rest API 만들기 & MERN\image-20220804171010947.png)



**Database Deployments에서 Create 한다**

![image-20220804171025874](C:\Users\KoBongHun\Desktop\Git\BH946.github.io\images\2022-08-03-Rest API 만들기 & MERN\image-20220804171025874.png)



**Database Access에서 DB접근 할 때 사용할 아이디를 만든다.**

![image-20220804171108740](C:\Users\KoBongHun\Desktop\Git\BH946.github.io\images\2022-08-03-Rest API 만들기 & MERN\image-20220804171108740.png)



**Network Access에서 네트워크 접은 방식 만드는데 개발할때는 0.0.0.0(=localhost)으로 할것**
**=> 다만, 실제 배포할땐 그 서버 ip를 기준으로 해야 함**

![image-20220804171227054](C:\Users\KoBongHun\Desktop\Git\BH946.github.io\images\2022-08-03-Rest API 만들기 & MERN\image-20220804171227054.png)



**Connect 버튼 클릭후 애플리케이션 접근 url활용 한다.**

**참고로 그림에 Browse Collections 를 클릭하면, 우리가 생성한 Schema 데이터들을 볼 수 있다.**

![image-20220804171431949](C:\Users\KoBongHun\Desktop\Git\BH946.github.io\images\2022-08-03-Rest API 만들기 & MERN\image-20220804171431949.png)



안전하게 사용하기 위해 `.env`에 위에서 구한 url 저장후 `dotenv 라이브러리`를 활용한다.    
아래 코드는 무조건 필수로 소스 작성해서 `.env` 내용을 사용해야 한다.

```js
// index.js
const dotenv = require("dotenv"); 
dotenv.config(); // 반드시 사용해줘야 env내용 사용가능

process.env.MONGO_URL // 전역 process로 env접근
```



**DB와 node 연결 코드**

```js
// index.js
const dotenv = require("dotenv"); 
const mongoose = require("mongoose");
dotenv.config();

mongoose
    .connect(process.env.MONGO_URL)
    .then(() => console.log("DB Connection Successfull!"))
    .catch((err) => {
        console.log(err);
    });
```





### mongoose를 이용한 Schema(Table)작성

NoSQL이므로 MySQL이라고 생각하면서 보지말고 차이가 있다는걸 이해하고 볼 것.  
Schema 작성을 MySQL의 Table 작성이라고 이해하면 편하다.  

models폴더로 구성했고 models폴더가 DB자체의 느낌이며,  
내부 파일들이 MySQL의 Table들을 만든 느낌

**예시 코드**

* MySQL의 `create table 테이블명()` 과 유사
  * `new mongoose.Schema()` 로 테이블 create 하는것과 유사하며
  * `mongoose.model("User", UserSchema)` 로 테이블명까지 붙이는것 유사

```js
// models/User.js
const mongoose = require("mongoose")

const UserSchema = new mongoose.Schema(
    {
        username:{type:String, required:true, unique:true},
        email:{type:String, required:true, unique:true},
        password:{type:String, required:true},
        isAdmin:{
            type:Boolean,
            default: false,
        },
    },
    { timestamps: true }
)

module.exports = mongoose.model("User", UserSchema)
```





### mongoose의 다양한 find() 사용

* DB에 우리가 원하는 데이터가 있는지 찾을때 사용하는 함수이다.
* findByIdAndUpdate, ...delete, ...등등 다양한 메소드들 존재한다.
  * 크게 find() 와 findOne()으로 나뉘며, findOne()은 딱 하나 찾는것이다.
  * 더욱 다양한건 공식문서에서 찾아볼것





### mongoose를 이용한 aggregate() 사용

mongodb에서 활용한 aggregate메소드는 db에 join문, group문 구현가능  
집계작업은 여러 문서를 처리하고 계산된 결과를 반환

* $ 들어간건 이미 내장되어 있는 함수들 같다
* "$들어간" 이거는 DB의 컬럼(Column)을 의미하는것 같다
* createdAt, month 이런건 변수처럼 이름지정하는 역할인것 같다  
  이 변수명으로 DB에 저장되는것 같다
* $match - 데이터 선택 같음
* $project - 저장같음
* $group - 출력같음 
* [공식문서](https://www.mongodb.com/docs/manual/aggregation/ ) 참고

```js
// GET USER STATS - 관리자만 사용가능(매월 합계 구해줌)
router.get("/stats", verifyTokenAndAdmin, async (req, res) => {
    const date = new Date();
    const lastYear = new Date(date.setFullYear(date.getFullYear() -1))

    try{
        const data = await User.aggregate([
            { $match: { createdAt: { $gte: lastYear}}}, // lastYear 이후 데이터
            {
                $project: { // 프로젝션인가
                    month: { $month: "$createdAt"} // db에 createdAt의 월을 month에 할당
                }
            },
            {
                $group: { // 이부분이 출력됨
                    _id: "$month", // 위에서 구한 month로 project된 db의 월을 의미
                    total: { $sum:1 } // 1의 의미는 여기서 구한 값 전부 합계하란 의미
                }
            }
        ])
        res.status(200).json(data);
    } catch(err){
        res.status(500).json(err);
    }
})
```

<br>

## Authentication(인증)

**로그인 및 회원가입 관련해서 API를 구성할때는 따로 auth.js 파일로 빼서 생성하는것을 추천한다.**

* **회원만이 접근가능한 API를 위해서 auth.js에서 로그인시 토큰(예: JWT)를 발행하기 수월**
* **비밀 정보들을 해시 형식으로 암호화 시켜서 다루기 위해 CryptoJS 같은것을 활용 하기 수월**





### auth.js

mongoose 라이브러리의 `save 메소드`는 Promise이며 비동기이므로, 비동기 표현이 필요하다.  
또한 `save 메소드`는 db에 해당 id가 없다면 insert()로 동작하고, id가있다면 수정을 한다.

* `sql문과 python`으로 했을때 db연결후 `commit, execute함수`처럼 입력하고 저장했다.
  nosql문은 sql문과 당연히 차이가 있으므로 여기서 `save메소드`로 db에 저장한다고 해서 이상한게 아니다.
* `json함수`로 json형식으로 데이터 전송(JSON으로 응답 중)
* `error 코드` 의 경우 다양할텐데 500만 그냥 설정했다. 여러 에러코드를 설정하는것도 중요!!
  * 즉, 예로 `error 코드` 400으로 하고, if로 만약 id,pw가 비어있는 경우로 지정하고  
    id,pw없다고 전송하는식.

```js
const router = require("express").Router() // express의 Router()함수 사용
const User = require("../models/User")

// REGISTER(등록)
router.post("/register", async (req, res)=> {
    const newUser = new User({
        username: req.body.username,
        email: req.body.email,
        password: req.body.password,
    }) // 얻은 값을 DB로 전송해야한다. save()사용

    try {
        const savedUser = await newUser.save(); // Document
        res.status(201).json(savedUser); // json
    } catch( err){
        res.status(500).json(err);
        // err code 500만 설정하겠다.(다양한 나머지 에러들은 일단 무시)
    }
})
```





### CryptoJS - 보안 강화

**비밀번호 같은 암호화하기 유용(위의 코드경우 비밀번호 그대로 노출)**

**따라서 crypto.js의 AES 가 보안에 강해서 강추!**



#### 1) crypto.js 사용할 때 

* `첫번째 매개변수` : 암호화할 키 입력
* `두번째 매개변수` : 암호화한 키 찾을때 사용할 키 입력
* 사용하면 몽고DB에 crypto.js 통해서 해시키로 암호화한 비밀번호로 저장되게 된다.



**CryptoJS.AES.encrypt() : 암호화 하는 함수**

```js
// CryptoJS.AES.encrypt() 함수 사용
// REGISTER(등록)
password: CryptoJS.AES.encrypt(req.body.password, process.env.PASS_SEC).toString()
```



**CryptoJS.AES.decrypt() : 암호환 키 원래 값으로 복구하는 함수**

```js
// CryptoJS.AES.decrypt() 함수 사용
// Login(로그인)
const hashedPassword = CryptoJS.AES.decrypt(user.password, process.env.PASS_SEC);
const OriginalPassword = hashedPassword.toString(CryptoJS.enc.Utf8); // 비번 형식 utf8
```



#### 2) 웹에 암호 숨기기

**웹에 암호는 뜨는데 이것 또한 안뜨게 하는건 코드 살짝 수정하면 됨.**

* 웹에 비밀번호가 해시키로 암호화한 것도 뜨긴 뜬다. 이를 없애기 위해서
* 코드 내부에서 몽고DB 비밀번호 불러와서 사용 후 응답을 비밀번호를 제외하고 응답한다.
* 아래 코드에서 jwt는 무시하고, password 제외 주석부분 중점으로 확인!!



```js
// LOGIN(로그인)
const CryptoJS = require("crypto-js")
router.post("/login", async (req, res) => {
    try{
        // REGISTER된 USER은 어차피 NAME이 1개일테니 findOne() 사용
        const user = await User.findOne({username: req.body.username});
        !user && res.status(401).json("Wrong credentials!") // id 못찾으면 401 에러

        const hashedPassword = CryptoJS.AES.decrypt(user.password, process.env.PASS_SEC) // 해시된 비번을 원래 비번 값으로 변환
        const OriginalPassword = hashedPassword.toString(CryptoJS.enc.Utf8); // 비번 형식 utf8

        OriginalPassword !== req.body.password && res.status(401).json("Wrong credentials!") // pw 몾찾으면 401 에러
        
        // 다 통과시 로그인 성공이므로 토큰도 생성(JWT 토큰은 뒤에서 설명하겠음)
        const accessToken = jwt.sign(
            {
                id: user._id,
                isAdmin: user.isAdmin,
            },
            process.env.JWT_SEC,
            {expiresIn:"3d"} // 3일뒤 만료. 즉, 다시 로그인 필요
        );

        // user로 하면 others에 필요없는 다른 소스들도 너무 많이 담긴다. 
        // 객체 열어보면 user._doc에 필요한 데이터들이 모여있다.
        // 따라서 user_doc로 지정한것이다.
        const { password, ...others} = user._doc; // password뺀 내용만 json으로 보내려는것

        res.status(200).json({...others, accessToken}) // password 제외!!!
    }catch(err) {
        res.status(500).json(err);
    }
})
```





### JWT - 보안 강화

`JWT(JSON웹토큰)`를 통해서 `세션`으로 로그인한 정보를 이용해서 `로그인이 필요한 API`를 다루는 방식이 아니라,  

`토큰`을 발급받아서 `토큰`으로 회원임을 인증하여 `로그인이 필요한 API`를 다루는 방식이다.

=> 보통 headers에 담아서 토큰 사용중

* `생성` : **jwt.sign(매개1,매개2,매개3)** 
  * 매개1 : 구별되는 id정돈 있어야하고, 필요하다면 기억할 정보도 추가가능
  * 매개2 : 앞의 해시(cryptojs) 풀때 필요한 SEC(키)처럼 유사한 역할
  * 매개3 : 여러 다양한 속성가능한데 그 중 몇일 뒤 만료일지 지정하는 속성 주로 사용
  * 매개4 : 콜백함수(잘 사용안함)

* `인증` : **jsw.verify(매개1,매개2,매개3)**
  * 매개1은 인증할 토큰을 적는다
  * 매개2는 앞의 해시(cryptojs) 풀때 필요한 SEC(키)처럼 유사한 역할
  * 매개3은 콜백함수인데 여기는 사용. err와 성공한 user값 매개변수 가짐



**jwt.sign(매개1,매개2,매개3) : 토큰 생성하는 함수**

* 예로 로그인해서 토큰 생성받는 경우
* 만료 기간과 간단히 토큰에 같이 넣을 정보(isAdmin)를 넣어서 토큰 생성

```js
// 위에서 본 코드 중 토큰 부분..
const accessToken = jwt.sign(
    {
        id: user._id,
        isAdmin: user.isAdmin,
    },
    process.env.JWT_SEC,
    {expiresIn:"3d"} // 3일뒤 만료. 즉, 다시 로그인 필요
);
```



**jsw.verify(매개1,매개2,매개3) : 토큰 인증하는 함수**

* 예로 회원만 접근 가능한 마이 페이지 경우 회원 인증을 해야해서 하는 경우
* 클라이언트에서 처음에 로그인 했을때 얻은 토큰이 user 정보와 함께 넘어오기 때문에
* 해당 토큰을 클라이언트에서 계속 이용해서 인증이 필요할때 같이 전송해서 응답 받을수 있다.
  * 이때 보통 headers에 토큰을 담아서 보낸다.

```js
const jwt = require("jsonwebtoken")
// 회원 인증
const verifyToken = (req, res, next) => {
    const authHeader = req.headers.token; // 보통 헤더에 토큰
    if (authHeader) {
        const token = authHeader.split(" ")[1] // 토큰에 무기명 토큰.. 이렇게 보냈음
        jwt.verify(token, process.env.JWT_SEC, (err,user) =>{
            if(err) res.status(401).json("Token is not valid!") // 토큰 유효하지 않은경우 - 2번경우
            req.user = user; // req에 user를 새로 추가해서 데이터 넣은것
            next()
        })
    }else { // 인증 실패~~(헤더에 토큰 업는경우) - 1번경우
        return res.status(401).json("You are not authenticated!")
    }
}
```

<br>

## Postman 사용법

공식 홈페이지에서 다운받은 후 실행

아래 사진처럼 세팅후 send버튼 클릭시 실제로 DB에 동작이 된것을 MongoDB에 접속해보면 알 수 있다.

![image-20220804180653916](/images/2022-08-03-Rest API 만들기 & MERN/image-20220804180653916.png)



만약 토큰을 활용하게 되면, Headers에 넣어주면 된다. (Bearer 뒤에 실제 토큰 작성해줘야 함)

URI에 보면 파라미터를 같이 전송하는데 저런식으로 find/뒤에 적어서 전송할 수 있다.

![image-20220804180953015](/images/2022-08-03-Rest API 만들기 & MERN/image-20220804180953015.png)

<br>

## CRUD with JWT

**Create(post 생성), Read(get 읽기), Update(put 수정), Delete(delete 삭제)**

=> 괄호안에는 `http 메소드`들 보통 저걸로 사용한다는걸 의미





### Authentication 추가 설명 - Create

`verifyToken 함수`는 토큰 인증하는 함수이다.

`verifyTokenAndAuthorization 함수` 는 위 함수를 이용해서 토큰 인증하는데,  
여기서 next 함수는 갑자기 무엇인지 설명하겠다.

* 화살표 함수에 매개변수로 **next() 함수**가 있다.   
  즉, `verifyToken 함수` 의 **next() 함수**는 `verifyTokenAndAuthorization 함수` 에서 `verifyToken 함수` 호출한 부분에 적혀있는 화살표 함수이다.
* 따라서 각 함수에선 **next() 함수**를 호출한 것이고,  
  만약 아무것도 없더라도 **next() 함수** 호출로 마무리를 해줘야한다.
* 즉, 함수를 매개변수로 보내서 사용하려고 이렇게 한것이다.

```js
// 회원 인증
const verifyToken = (req, res, next) => {
	// ...
	next() // verifyTokenAndAuthorization에서 호출한 화살표 함수 실행
    // ...
}

const verifyTokenAndAuthorization = (req, res, next) => {
    verifyToken(req, res, () => { // next함수 부분!!
        // (url로 보낸)요청한id === 토큰에서 얻은id or 관리자면 통과!
        if (req.user.id === req.params.id || req.user.isAdmin) { // url로 보낸건 params로 접근가능
            next(); // 여기 next는 내용이 없지만 send()나 end()처럼 끝 맺음 해줄것.
        } else {
            res.status(403).json("You are not alowed to do that!"); // - 3번경우
        }
    })
}
```





### UPDATE

위에서 만든 `verifyToken 관련 함수들` 을 이용해서 상황에 따라 토큰 인증을 받으면 된다.

예를 들어 토큰 인증이 필요없으면?? 해당 함수 사용안하면 된다!

* 아래 예시는 관리자 or 자기자신이므로 `verifyTokenAndAuthorization 함수` 를 사용한다.
* 그리고 UPDATE이므로 `router의 put메소드`를 활용한다.
* `$set` 의 경우 User 스키마(테이블)의 모든 속성들이라 보면 된다.
* `mongoose 의 findByIdAndUpdate()` 사용

```js
// UPDATE - 관리자 or 자기자신
router.put("/:id", verifyTokenAndAuthorization, async (req,res) => {
    // 만약 비번 update 할수 있기 때문에 아래 if문 추가 및 암호화
    if (req.body.password) { 
        // 암호화
        req.body.password = CryptoJS.AES.encrypt(req.body.password, process.env.PASS_SEC).toString();
    }
    try{
        const updatedUser = await User.findByIdAndUpdate(
            req.params.id, // id 찾기
            {
                $set: req.body // body 업데이트(id, pw... 전부 update)
            },
            { new: true}
        );
        res.status(200).json(updatedUser)
    } catch(err) {
        res.status(500).json(err);
    }
})
```





### DELETE

* 아래 예시는 관리자 or 자기자신이므로 `verifyTokenAndAuthorization 함수` 를 사용한다.
* 그리고 DELETE이므로 `router의 delete메소드`를 활용한다.
* `mongoose 의 findByIdAndDelete()` 사용

```js
// DELETE - 관리자 or 자기자신
router.delete("/:id", verifyTokenAndAuthorization, async (req, res)=>{
    try{
        await User.findByIdAndDelete(req.params.id)
        res.status(200).json("User has been deleted...")
    }catch(err){
        res.status(500).json(err)
    }
})
```





### GET

* 아래 예시는 관리자만이므로 `verifyTokenAndAdmin 함수` 를 사용한다.
* 그리고 GET이므로 `router의 get메소드`를 활용한다.
* `mongoose 의 find()` 사용

```js
// GET ALL USER - 관리자만 사용가능
router.get("/", verifyTokenAndAdmin, async (req, res)=>{
    // 개수 제한 기능 추가(5)
    const query = req.query.new; // url에 ?new=true같이 보냈으면 new값 반환
    try{
        const users = query
        ? await User.find().sort({ _id : -1}).limit(5)
        : await User.find();
        res.status(200).json(users)
    }catch(err){
        res.status(500).json(err)
    }
})
```





### 나머지

나머지 다양한 경우들은 프로젝트의 코드들을 확인해보는것을 추천한다.

* [Git_Rest_API](https://github.com/BH946/fullstack-clone/tree/shop-cart-ecommerce-rest-api)
* promise 객체 반환하는지 확인 : [promise_mongoose](https://masteringjs.io/tutorials/mongoose/promise) 이고,   
  여기선 find, save다 가능이고, 콜백함수 없어야한다함.
  * async/await은 promise여야 사용할 수 있음
  * promise아니여도 new Promise로 promise객체로 생성해서 사용할 수도 있긴함

















