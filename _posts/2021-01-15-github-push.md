---
layout: post
title: Gitbhub Blog를 위한 remote, push
date: "2021-01-15"
categories: Github Git
---
지난 포스트에서 로컬 저장소에 테마가 적용된 블로그를 개설해봤다.<br>
이 포스트는 로컬 저장소에 있는 파일을 원격 저장소에 올리고, 도메인을 이용하여 블로그를 구축해보는 방법에 대해 알아보겠다.<br>

<br><br>

### 1. Github 계정 생성 및 저장소 생성

[Github Link][1]<br>
당연하겠지만 우선 Github에 계정을 생성해야 한다.<br>
계정을 성공적으로 생성하고 난 후, Github Blog를 위해서는 저장소를 생성해야 한다.<br><br>
![Github_New][2]<br>
![Github_New2][3]<br>
_* 개인 도메인을 위해서 저장소의 이름은 **(유저명).github.io**로 생성한다._<br><br><br>

### 2. Git Remote

우선 프로젝트 폴더에 Git을 설치해야 한다.<br>

```bash
$ git init
```
<br><br>

git remote는 원격 저장소를 관리할 수 있는 명령어이다.<br>
로컬 저장소와 원격 저장소 연결을 위해 blog 라는 이름으로 원격 저장소 주소를 등록한다.<br>

```bash
$ git remote add origin https://github.com/uxmin/uxmin.github.io.git
```
_* 보통 origin으로 소개하는데, 본인이 원하는대로 선택하면 된다. 복사된 URL은 **Shift**+**insert**로 입력한다._<br><br>

![Github_Repositories][4]<br>
_* 원격 저장소의 URL은 여길 참조._<br><br>

#### 2_1. Remote 확인
```bash
$ git remote
```
#### 2_2. Remote 제거 
```bash
$ git remote rm origin
```
<br><br><br>

### 3. Git Push

초기화 된 Git은 **master**라는 이름의 Branch를 생성한다.<br>
저장소에 새로운 파일을 추가하거나, 파일을 수정해서 Commit을 진행하는 것은 모두 master branch를 통해 처리된다.<br><br>

![Git_Branch][5]<br><br>

필요에 따라 새로운 branch를 생성해서 작업을 한 후 master branch로 Merge하는데, 여기서는 기본적으로 master branch를 이용할 예정이다.<br><br>

#### 3_1. Add

Commit을 하기 전, 어떤 파일들을 Stage에 올릴지 선택을 하는 과정이 필요하다.<br>
첫 과정이기 때문에 모든 파일을 Stage에 올리는 과정을 진행하겠지만, 다양한 과정이 존재한다.<br>
```bash
$ git add -A #현재 모든 경로의 파일을 Stage에 올림.
$ git add . #현재 경로의 모든 변경사항을 Stage에 올림.
$ git add ./exam/examFolder #examFolder 폴더의 모든 변경사항을 Stage에 올림.
$ git add ./exam/examFolder/examFile.png #examFile.png 파일을 Stage에 올림.
$ git add -p #현재 경로의 변경사항을 확인 후 Stage에 올림.
```
_* Add 명령어 참고: [Git-Add Documentation][6]_<br><br>

성공적으로 Stage에 적재됐는지 확인하고 싶다면 아래 명령어로 확인할 수 있다.<br>
```bash
$ git status
```

![Git_Status][7]<br>
_* master branch의 Stage에 올라간 것을 확인할 수 있다._<br><br>


#### 3_2. Commit

Push를 하기 전, Stage에 올라간 파일들을 Commit하는 과정이 필요하다.<br>
Commit은 Remote서버에 파일을 전송(Push)하는 것이 아니고, 사용자의 클라이언트 내에서 수행되는 것임을 알고 있어야 한다.<br><br>
아래 명령어를 입력하여 Stage에 올라간 파일들을 Commit한다.<br>

```bash
$ git commit -m "Blog First Commit"
```
_* Commit 명령어 참고: [Git-Commit Documentation][8]_<br><br>

![Git_Commit][9]<br>
_* Commit이 성공적으로 완료된 모습._<br><br>

Commit을 진행할 때는 로그인이 수반되어야 한다.<br>
혹시 로그인을 하지 않았다면 아래 명령어로 입력해서 로그인을 먼저 진행한다.<br>
```bash
$ git config --global user.email "Github@gmail.com"
$ git config --global user.name "Github"
```
<br>

Commit이 성공적으로 완료되었는지는 아래 명령어를 입력해서 확인할 수 있다.<br>
```bash
$ git log
```
<br><br>

#### 3_3. Push

모든 과정이 완료되었다면 원격 저장소로 Push하는 과정을 진행한다.<br>
등록했던 Remote 서버의 origin을 이용해서 master branch에 push한다.<br>

```bash
$ git push origin master
```


[1]: https://github.com/
[2]: /assets/img/Github/github_new_repositories.png
[3]: /assets/img/Github/github_new_repositories2.png
[4]: /assets/img/Github/github_repositories_url.png
[5]: /assets/img/Github/git_branch.png
[6]: https://git-scm.com/docs/git-add
[7]: /assets/img/Github/git_status.png
[8]: https://git-scm.com/docs/git-commit
[9]: /assets/img/Github/git_commit.png