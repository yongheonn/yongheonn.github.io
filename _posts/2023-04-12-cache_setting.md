---
title: aws s3 정적 사이트 캐시 문제
date: 2023-04-12 02:00:00 +09:00
categories: [웹, 개발 고민]
tags: [aws, s3, lambda, nodejs, cache, metadata]
---

# html, css, js 캐시 문제 해결

기본적으로 리액트 빌드시 index.html은 그대로지만, static 폴더의 css와 js는 매번 파일명이 변경되어 새로 업로드된다.

여기에 어차피 html에서 로드할 css와 js가 결정되기에 css와 js의 캐시를 최대한 길게 잡아도 업데이트할 때는 새로운 css와 js가 적용되며, 업데이트되지 않았을 때 계속 캐시를 사용하는 최적화가 일어난다.

반면 index.html은 파일이름이 그대로여야 하기에 캐시를 길게 적용하기에 문제가 있다.

그렇기에 html에는 cache-control: private, no-cache로 css와 js는 cache-control: public, max-age: 31536000로 설정해주면 캐시 최적화가 더 잘된다.

나는 aws s3와 cloudfront를 이용해서 정적 사이트 배포를 하고 있었기에 s3에서 각 파일의 metadata를 수정해줘야했다.

문제는 한번 metadata를 직접 수정해놔도 새로운 파일이 업로드되거나 교체되먼 기존 meatadata는 사라지기에 배포 때마다 자동으로 metadata를 수정하게 해야했다.

# aws lambda

위 문제는 aws lambda를 이용해서 해결했다. aws lambda에서 s3 대상으로 생성 이벤트와 접두사 static/과 index.html일 때의 두개의 트리거를 생성했다.

// 추후 favicon.ico logo???.png img 폴더 등의 이미지도 자주 변경되지 않을 것으로 예상되기에 트리거 대상에 포함하고, 1주일 정도의 캐시를 적용했다.

여기서 나는 nodejs 18을 이용해서 코드를 작성했고, 몇번의 시행착오 끝에 결국 자동화를 해냈다.

여기서 주의점은 nodejs 18은 이전 버젼과 달리 s3client를 import해야 했다.

그리고, getobject 후 다시 copyobject로 업데이트할 때, copyobject로 인한 생성 이벤트로 다시 무한히 반복되는 것을 방지하기 위해, 생성 이벤트로 들어왔을 때, cache-control이 undefined인지 확인하고, undefined일 때만 copyobject를 실행하고, 아니면 실행하지 않도록 했다.
