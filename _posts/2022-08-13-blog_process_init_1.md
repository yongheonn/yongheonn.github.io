---
title: github 블로그 개발환경 구축하기 1
date: 2022-08-13 16:17:00 +09:00
categories: [웹, 블로그]
tags: [blog, github, jekyll]
---

# 0. 개요

github 블로그는 github에서 지원해주는 블로그로 가장 자유도가 높기로 유명하다.

웹 관련 지식(ex. html, css, javascript 등)이 많을수록 자유롭게 커스터마이징해서 꾸밀 수 있으므로, 앞으로 웹 관련 업계에 종사할 예정이 있는 나로서는 github 블로그를 고르는 것이 옳다고 생각했다.

github 블로그를 시작하려면 당연히 github 계정이 있어야하며, 백지에서부터 시작하기에는 많은 노력과 시간이 필요로하므로, 미리 어느정도 만들어진 샘플인 테마를 통해서 시작하는 것이 좋다.

# 1. 테마 고르기

내가 고른 테마 사이트는 [http://jekyllthemes.org/](http://jekyllthemes.org/)이며, 여기서 chirpy라는 테마를 골랐다.

![Alt text](/assets/img/select%20theme.png?raw=true)

테마를 고르면 위 사진처럼 나오게 되는데, 이때 이 테마를 적용시키는 방법은 2가지가 있다.

- 위 사진의 Homepage, 즉 제작자의 github 프로젝트에서 fork하는 방법
- Download 항목을 통해서 다운 받거나 Homepage, 즉 제작자의 github 페이지로 이동해 code를 zip을 통해 다운받거나 release 항목의 소스코드를 다운 받는 방법

기본적으로 첫번째 방법과 두번째 방법의 차이는 먼저 자신이 저장소를 만들어두었는가에 따라서 방법이 나뉜다고 봐도 된다.  
따로 github 블로그 저장소를 만들어두지 않았다면 첫번째, 아니라면 두번째가 편리하다.

먼저 제일 간단한 fork 방식을 알아보자면 제작자의 github 프로젝트 우상단의 fork를 눌러 저장소를 복제한 다음에 복제한 저장소의 이름을 `깃허브아이디.github.io` 의 형태로 바꿔준다.

그 다음 과정부터는 거의 동일하므로, 다음 방법을 알아보자면 먼저 Repository(저장소)를 새로 생성해주는 것부터 시작이다.  
만약 이미 만들어진 블로그 저장소가 있다면 다음 과정은 패스해도 된다.

### Repository(저장소) 생성

- Repository name을 `Owner(깃허브 아이디).github.io` 로 하기
- 공개 상태를 public으로 하기

나머지는 특별히 건들 것 없이 저장소를 생성한면 된다.

# 2. 저장소 Clone

이제 첫번째 방법과 두번째 방법 모두 저장소를 clone해야 하는데, 컴퓨터의 적절한 폴더에 git bash를 열거나 cmd에서 cd 명령어로 폴더 위치로 이동한 다음에 `git clone [깃허브 주소].git` 을 입력하면 된다.  
깃허브 주소는 자신의 저장소에서 code를 클릭한 다음에 clone의 https 항목에서 복사하기를 누르면 `[깃허브 주소].git` 이 복사된다.  
또는 그냥 자신의 저장소 주소를 복사하고 `.git` 을 붙여서 사용해도 된다.  
저장소 clone이 완료되면 두번째 방법의 경우 다운받았던 소스코드를 압축해제해서 로컬 git 저장소에 덮어씌운다.

# 3. 설치해야할 것들

그 다음에 첫번째 방법, 두번째 방법 모두 `Ruby`, `RubyGems`, `Jekyll`, `Builder` 을 기본적으로 설치해야 한다.  
`Ruby`는 [https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/)에서 운영체제에 알맞게 설치하면 된다.  
다운받은 설치파일로 `Ruby`를 설치하면 cmd에서 git clone 저장소로 이동해야 한다.
