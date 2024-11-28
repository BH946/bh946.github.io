---
title:  "Github Actions의 외부 환경 변수 Actions Secrets"
categories : Action_Git
tag : [깃헙, 깃허브, git, github, CI/CD, cron, 깃허브 액션, Github action, env]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



로컬 개발 환경에서 환경 변수로 `.env` 를 주로 사용하는데 보통은 `.gitignore` 을 이용해서 Git 저장소에 올리지 않음으로써 보안을 유지한다. 따라서, Github Actions 에서는 `.env` 를 볼 수 없어서 **Actions Secrets** 를 활용한다!!

따라서 여기서는 `.env` 파일을 `secret`을 통해서 **Github Actions** 실행 때 생성하는 과정을 정리하였다.

<br>

<br>

## 깃허브 액션에서 secret 사용법

`.env` 파일에 존재하는 환경 변수 중에서 **필요한 변수를** **Actions Secrets**에 추가해둔다.

* 레퍼지토리의 Settings에 있다!

<br>

그리고 실행이 되었을 시 **Actions Secrets**를 통해 개발 환경을 위한 환경 변수 파일(.env)을 생성해주도록 한다.

리눅스의 명령어인 `echo` 명령어를 활용!!

1. `CREATE_ENV`로 생성해둔 `secrets` 내용이 `src/` 경로에 `.env` 파일로 생성

2. 보안성 덕분에 `secrets` 값을 print나 echo로 출력을 해보면 ***으로만 출력이 됨
   그러나 실제로는 값을 잘 가져오므로 문제 없음

```yaml
# $은 변수에 사용
# secrets는 ${{ secrets.CREATE_ENV }} 이런 형태로 사용
- name: create env
	run: |
		echo "${{ secrets.CREATE_ENV }}" >> src/.env
		# echo "Hello, World!" >> test.txt
		# echo "TEST_API_KEY=$TEST_API_KEY" >> .env.production
```

