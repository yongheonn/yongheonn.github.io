---
title: 스프링 부트에서 daum 메일 전송 주의점
date: 2023-04-09 03:07:00 +09:00
categories: [웹, 개발 고민]
tags: [spring-boot, java, smtp, mail, daum]
---

# 주의점

다음 메일은 smtp에서 ssl 방식을 사용하기에 포트는 465로 하고, properties에서 ssl을 따로 enable: true로 해주어야 한다.

그리고, 계속 452 check your e-mail address라는 에러가 떠서 확인해 봤더니, application.yml 문제인줄 알았는데 SimpleMailMessage에서 setFrom을 안해서 생긴 문제였다.

이는 본격적으로 배포 전에 구글 메일을 다음 스마트워크 서비스로 바꾸어 인증 메일을 직접 도메인 메일을 통해서 구현하려고 했기 때문이었다.

구글과 달리 다음은 from을 따로 설정해주지 않으면 에러가 생기는 것이었다.
