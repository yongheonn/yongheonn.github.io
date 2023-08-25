---
title: github 민감한 정보 관리
date: 2023-05-04 07:00:00 +09:00
categories: [웹, 개발 고민]
tags: [git, private, submodule, yml, properties]
---

# git에서 민감한 정보 처리

spring에서 application.yml이나 application.properties등 민감한 정보를 관리할 때, .gitignore를 통해서 공개되지 않도록 한다.  
로컬 개발 환경에서는 문제되지 않지만, 문제는 실제 프로덕션에 전달할 때이다.  
이러한 때에는, submodule을 사용하면 좋다.  
secret key를 활용할 수도 있으나 너무 제한적이고, application.yml이나 application.properties와 같은 파일 형식에 적용하기는 힘들다.

submodule을 사용하면 권한있는 사용자만 볼 수 있으며, github action에서도 해당 파일을 활용할 수 있으니 편리했다.

대신, 해당 push 내역을 반영하려면 `git submodule update --remote`을 활용하면 좋다.
