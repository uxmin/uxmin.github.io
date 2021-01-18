---
layout: post
title: Github Blog를 위한 jekyll 설치 및 테마 적용
date: "2021-01-13"
categories: Github Ruby Jekyll
---
Github 블로그를 개설하기에 앞서, 블로그를 개설하기 위한 환경을 구축해주어야 한다.<br>
이 포스트는 Github 블로그를 개설하고, 테마 적용을 위한 jekyll 설치 및 테마를 적용하는 방법에 대해서 알아보겠다.<br>

<br><br>

### 1. Ruby 설치
  
[Ruby Download Link][1]<br>
Jekyll을 설치하기에 앞서 Ruby를 우선적으로 설치해주어야 한다.<br><br>
![Ruby_installer][2]<br>
_* 나의 경우에는 2.7.2-1 버전을 설치했지만 최신 버전을 다운받아도 된다._<br><br><br>

### 2. Jekyll 설치

Ruby를 성공적으로 설치했으면 Jekyll을 설치해야 한다.<br>
Jekyll를 설치할 경로를 설정한 후 다음과 같은 명령어를 입력한다.<br>

```bash
$ gem install jekyll
$ gem install minima
$ gem install bundler
$ gem install jekyll-feed
$ gem install tzinfo-data
```
<br><br>

Ruby와 Jekyll이 성공적으로 설치되었는지 확인하기 위해서 다음 명령어로 버전을 확인한다.<br>
```bash
$ ruby -v
$ jekyll -v
```
![Ruby_Jekyll_V][3]<br>
_* 성공적으로 설치된 모습을 확인할 수 있다._<br>

<br><br>

### 3. 기본적인 Jekyll 블로그 만들기

테마를 적용하기 앞서서 기본적인 블로그를 만들어보자.<br>

```bash
$ jekyll new blog
```
![Jekyll_New][4]<br><br>

새로 생성된 폴더 경로로 들어가서 서버를 열어준 후 생성이 되었는지 확인한다.<br>

```bash
$ jekyll server
```
![Jekyll_Server][5]<br><br>

```bash
$ bundle exec jekyll server
```
_* jekyll server가 작동되지 않을 때 이 명령어를 사용해보자._<br>
<br>

성공적으로 서버가 실행이 되면, **localhost:4000** 주소를 통해서 블로그가 생성된 것을 확인할 수 있다.<br><br>
![Jekyll_Server_Success][6]<br><br><br>

### 4. Jekyll 테마를 적용한 블로그 만들기

구글에 jekyll 테마를 검색하면 어렵지 않게 무료 테마를 제공하는 사이트를 찾을 수 있다.<br>
그 중 대표적인 사이트를 소개한다.<br>
[Jekyll Theme 다운로드][7]<br>

테마를 선택한 후, url을 이용해서 클론하는 방법을 적용하겠다.<br><br>
![Jekyll_Theme_Url][8]<br>
_* jekyll 테마 url은 여기서 확인할 수 있다._<br><br>

기존 경로에 clone하면 테마 이름으로 폴더가 생성된다.<br>

```bash
$ git clone https://github.com/samarsault/plainwhite-jekyll.git
$ cd plainwhite-jekyll
$ bundle
$ jekyll server
```
_* 추후에 Git으로 push하기 위해서 (사용자명).github.io 폴더명 변경해 줄 예정._<br><br>

테마가 적용되고 서버가 정상적으로 작동하게 되면 밑과 같이 적용된 모습을 확인할 수 있다.<br><br>
![Jekyll_Theme_Success][9]<br><br><br>


[1]: https://rubyinstaller.org/downloads/
[2]: /assets/img/Github/ruby_installer.png
[3]: /assets/img/Github/ruby_jekyll_v.png
[4]: /assets/img/Github/jekyll_new.png
[5]: /assets/img/Github/jekyll_server.png
[6]: /assets/img/Github/jekyll_server_success.png
[7]: http://jekyllthemes.org/
[8]: /assets/img/Github/jekyll_theme.png
[9]: /assets/img/Github/jekyll_theme_success.png