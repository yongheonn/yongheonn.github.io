---
title: "Liquid Exception: undefined method gsub for 502:Integer in"
date: 2023-11-13 22:00:00 +09:00
categories: [웹, 개발 고민]
tags: [Integer, error, github, blog, action]
---

# 어느날 확인해본 결과 알게된 오류

오랜만에 커밋해두었던 블로그의 내용을 저장소에 푸쉬하였는데, 반영이 되지 않아서 캐시 문제라고 생각했었다.  
그런데, 충분한 시간을 기다리고 캐시 사용을 중지하거나 강제로 새로고침하여도 반영되지 않아서 깃허브 action에서 빌드등의 오류가 생겼다 싶어서 확인하러 갔다.  
아니나 다를까, 역시 깃허브 action에서 오류가 있었음을 확인할 수 있었는데, 저번 달에 푸쉬한 내역도 오류가 있었다.  
확인을 안하고 넘어간 탓에 모르고 있었던 것이다.  
그렇게 오류 내역을 찬찬히 살펴보는데,

`Liquid Exception: undefined method gsub' for 502:Integer in /home/runner/work/yongheonn.github.io/yongheonn.github.io/_layouts/post.html`

앞선 내용의 에러를 확인할 수 있었다.  
처음엔 그냥 생각없이 대충 말그대로 \_layout 폴더의 post.html부터 찾아가 에러를 고치려 했다.  
그런데, 생각해보면, 해당 파일은 블로그 시작부터 수정한 적이 없어서 왠만해선 원인이 아닐 것이라는 점을 알 수 있다.  
실제로 약간 의심이 가는 부분이 있어 수정해보아도 전혀 원인이 아니었다.  
그렇게 커밋 내역을 살펴보고, 다시 에러 메세지를 제대로 살펴보니 원인을 짐작할 수 있었다.  
\_layout 폴더의 post.html은 말그대로 블로그 포스트의 레이아웃을 설정해둔 것으로, 실제 포스트에서 뭔가 문법적으로 오류가 있었다는 뜻을 내포할 수도 있으며, 502:Integer에서 숫자와 관련된 무언가의 문제가 있다는 뜻을 추측할 수 있었다.  
실제로 커밋 당시 2023-10-07-cors-issue 포스팅 내부를 보면, 태그에서 502, 504를 그대로 썼는데, 이게 따옴표로 감싸주지 않으면 에러가 발생하는 것이었다.  
그렇게 해당 숫자를 따옴표로 감싸주니 바로 에러가 해결되어 포스팅 반영이 제대로 되었다.