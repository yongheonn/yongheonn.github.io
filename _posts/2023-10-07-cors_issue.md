---
title: 갑작스러운 504 및 502 cors 에러
date: 2023-10-07 18:00:00 +09:00
categories: [웹, 개발 고민]
tags: [cors, "502", "504", origin, aws, cloudflare]
---

# 어느날 갑자기 api 통신이 안되는 오류 발생

주기적으로 yongheonn.com 사이트를 점검하던 어는 날, 아무것도 건드린 적 없는데, 잘되던 사이트에 문제가 발생했다.  
프론트 단의 사이트 자체는 문제가 없었으나 백엔드와의 통신이 먹통이 되었는지, 게시판 목록을 받지 못하고 있던 것이다.  
그래서, 일단 개발자 도구를 통해 네트워크 탭을 확인했고, 504 에러를 확인했다.  
그리고, 콘솔을 확인한 결과

`yongheonn.com/:1 Access to fetch at 'https://api.yongheonn.com/ajax/subject/all' from origin 'https://yongheonn.com' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`

라는 에러 결과를 확인할 수 있었고, 당연스럽게 cors 설정부터 살펴봤으나, 이상이 없었고, 잘 생각해보면 코드를 건드린 적이 없는데, 코드 관련 이슈일 리는 없다는 판단을 내렸다.  
그렇기에 aws ec2부터 확인했고, 잘 실행중이었으나, 혹시 몰라 재부팅 후, 프로세스도 다시 실행해주었다.
그러더니 504 에러 대신 502 에러가 뜨기 시작했고, 프로세스가 어쩌다보니 죽어있었다는 판단을 내렸다.  
하지만 여전히 에러가 뜨는 것은 여전했고, ssh 연결을 통해 내부에서 curl 명령어로 확인해본 결과, 내부 8080 포트는 연결이 잘 되고 있었고, 밖에서도 http://ec2-43-200-79-234.ap-northeast-2.compute.amazonaws.com:8080/ajax/subject/all 로 curl을 던져보니 결과를 잘 받아왔다.  
그렇기에 loadbalancing 부분이나 cloudflare 부분에서 뭔가 잘못되었다는 결론을 내렸다.

그리고, 실제로 로드밸런싱의 http://yongheonn-loadbalancer-262576260.ap-northeast-2.elb.amazonaws.com:8080 로 curl을 실행해보니 긴 시간후 connection failed를 응답했다.  
curl로 http://yongheonn-loadbalancer-262576260.ap-northeast-2.elb.amazonaws.com:8080가 아닌 https://yongheonn-loadbalancer-262576260.ap-northeast-2.elb.amazonaws.com 로 줬을때, 반응이 생겼었다.  
하지만 올바른 인증서가 아니라 하여 인증서 문제인줄 알고, acm 인증서를 확인하니 만료되어 있어, acm 인증서 발급을 요청하여 새로 발급하고, 이를 로드밸런싱에 연결한 다음, cloudflare에 dns로 등록해주었다.  
하지만, 이래도 이상한 반응을 보였는데, curl을 통한 ssl 인증서 관련 오류는 무시해도 좋다는 글들을 읽을 수 있었고, 다시 -k 옵션을 주니 502 에러가 떴다.  
즉, 로드밸런싱 문제는 확실하다는 것으로 인증서 문제는 아니었던 것이다.

aws loadbalancing 탭에서 대상 그룹으로 전달 부분을 따라가보니 등록된 대상의 상태 확인에 unhealthy라는 문제를 발견할 수 있었다.

인터넷에서 관련 정보를 찾아본 결과, loadbalancer는 기본 주소, 즉 "/"로 health check를 실행한다고 한다.  
즉, 서버가 건강한지 주기적으로 기본 주소로 체크를 하는데, 내 백엔드 프로젝트에서는 api 역할만 신경쓰고, 기본 주소로 들어오는 요청에 대한 로직을 설정해두지 않았기 때문에, 건강하지 못한 서버로 판단되고, 요청을 거절하고 있었던 것이다.

그렇기에 사용 몇달간은 문제가 없었지만, 어느 시점, health check가 실행된 이후 api 통신에 문제가 생겼던 것이다.  
사실, 30초 주기로 체크하는데, 이 옵션이 이제 와서 어쩌다 켜진건지, 혹은 원래 켜져있었는데, 어떻게 문제가 발생하지 않았는지는 의문이다.

어찌되었건, /ajax/health/ 로 health check를 받도록 loadbalancing 옵션을 적절히 설정해주었고, 스프링 시큐리티에서도 해당 주소는 인증없이 접근 가능하도록 설정을 바꾸어주었다.

문제는 이런데도 계속 unhealthy가 떴고, 여러 시도 끝에 health check 포트를 8080으로 지정하면 healthy로 뜨는 것을 확인했다.  
해당 대상은 알고보니 80포트를 대상으로 했을 때, 문제였던 것이고, 따로 8080포트를 대상으로 그룹을 열어줘야 되었던 것이다.  
이에 따라, 8080포트로 대상 그룹에 새로 인스턴스를 연결해주니 해당 포트는 healty로 뜨고, 사이트도 정상 작동하였다.
