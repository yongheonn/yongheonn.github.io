---
title: Git 비밀번호 재설정
date: 2022-11-01 17:30:00 +09:00
categories: [웹, 개발 고민]
tags: [pw, password, git, token, update]
---

# Git 비밀번호 재설정 방법

최근 github는 사용자 비밀번호가 아닌 토큰을 이용하여 push 접근 등을 허용한다.
그리고, window 자격 증명 관리자 등 여러 방법을 사용해봤으나 계속

`remote: Invalid username or password. fatal: Authentication failed for '<깃허브주소>'`

같은 메시지가 떴고, 잘 생각해보다 .git 디렉토리에 있는 config 파일을 수정해주면 된다는 것을 깨달았다.

깃 프로젝트 최상단에 있는 .git 폴더 내부에 config 파일이 있는데 그 내용은 다음과 같다.

![Alt text](/assets/img/git%20config.png?raw=true)

중간에 비어진 저부분에 `ghp_`로 시작하는 토큰값을 갱신해주면 비밀번호 재설정이 완료된다.
