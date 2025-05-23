---
title:  "Workflow 작성으로 레퍼지토리에 push 하는 법"
categories : Action_Git
tag : [깃헙, 깃허브, git, github, CI/CD, cron, 깃허브 액션, Github action]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**자신의 레퍼지토리에 저장하는 것은 따로 인증이 필요없지만, 다른 레퍼지토리에 push하는 것은 Git 인증이 필요하며 여기서는 SSH 키 인증 방식을 사용한다.**

<br>

<br>

## 자신의 레퍼지토리에 push

**만약 실행 중에 새로 파일을 생성한 경우 이 파일을 자신의 레퍼지토리에 저장하는 것도 가능하다.**

<br><br>

### workflow file

**yml 파일 작성**

```yaml
    # 생성한 파일들 push로 업데이트
    - name: push data update
      run: |
        git config --global user.name "github-actions[bot]"
        git config --global user.email "41898282+github-actions[bot]@users.noreply.github.com"
        git add -A
        git commit -m "data update"
        git push
```

* add, commit, push 명령어는 기본이기 때문에 자세한 설명은 생략하겠음.

<br>

<br>

## 다른 레퍼지토리에 push

**사용한 문서 : [push-another-repository](https://github.com/marketplace/actions/push-directory-to-another-repository)**

**정확히 이 문서대로만 진행하면 된다. - ssh 키 방식을 사용**

- **Generate the key files**
- **Set up the deployment key**
- **문서에서 이 2개만 따라가면 충분**

<br>

**시간 낭비했던 것들이 있어서 정리하겠다.**

1. **ssh 키 생성 때 : `ssh-keygen -t ed25519 -C "kobh2895@gmail.com"`
   자신의 깃허브 이메일로 꼭 기입할 것(깃 프로필에서 이메일 확인)**
   * `cat 파일명` **명령어로 키를 출력해서 볼 수 있다.**
   * **public key :** `cat 파일명.pub`
   * **private key :** `cat 파일명`
2. **settings 접근 법 :** `settings for the repository, not the account settings`
   반드시 레퍼지토리의 세팅을 클릭
3. **Add deploy key :** `paste the contents of the file with the public key`
   반드시 `public key`를 입력(자신이 만든 key파일.pub)
   * **출력된 모든 내용을 복사해서 붙여넣어야 한다(앞에 ssh-ed25519 포함하라는 뜻)**
4. **New repository secret :** `paste the contents of the file with the private key`
   반드시 `private key`를 입력(자신이 만든 key파일)
   * `--BEGIN...--`과` --END...--` 사이의 내용들을 복사해서 붙여넣어야 한다

<br><br>

### workflow file

**yml 파일 작성**

```yaml
      - name: Pushes to another repository
        uses: cpina/github-action-push-to-another-repository@main # 이게 반드시 필요
        env:
          SSH_DEPLOY_KEY: ${{ secrets.SSH_DEPLOY_KEY }} # SSH 키로 사용!
        with:
          source-directory: 'output' # 보내고 싶은 파일이 있는 폴더명 or 파일명으로!(경로 지정)
          destination-github-username: 'BH946' # 아래 설명 참고
          destination-repository-name: 'git-action-smartstore' # 아래 설명 참고
          user-email: kobh2895@gmail.com # 아래 설명 참고
          commit-message: 'test' # 커밋할 메시지 입력
          target-directory: 'log' # 아래 설명 참고
          target-branch: main # main 브랜치에 push
```

* `source-directory` : 자신이 보내는 출발점(source) 레퍼지토리에서의 경로를 의미

* push하고 싶은 레퍼지토리 URL : https://github.com/BH946/git-action-smartstore

  * `destination-github-username` : 'BH946'

  * `destination-repository-name` : 'git-action-smartstore'

* `user-email` : ssh 키 만들 때처럼 자신의 깃허브 이메일을 기입

* `target-directory` : 원하는 도착지의 경로를 작성
  * 참고로 항상 전체 데이터 초기화되고, push가 진행이 됨.
  * `target-directory`을 해줘야 해당 경로의 폴더만 초기화되고, push가 진행이 됨.
  * 따라서 `target-directory`를 꼭 사용하는 것을 추천
