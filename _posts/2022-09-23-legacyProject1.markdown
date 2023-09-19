---
layout: legacy
title: 스프링부트 레거시 프로젝트 1 - 환경세팅하기
date: 2022-09-23 15:40:00 +0900
categories: 스프링부트_레거시프로젝트
badges:
- type: primary
  tag: 스프링부트
- type: primary
  tag: 레거시 프로젝트
---
스프링부트 레거시 프로젝트를 만들기 위해서도 https://start.spring.io/ 를 사용할것이다.
레거시 프로젝트이기 때문에 다음과 같은 설정을 적용할 것이다.
- 스프링 부트
- 메이븐
- 자바1.8
- lombok
- log4j
- mybatis
- mustache

![legacy1](/assets/img/legacy1.png)
1. 메이븐 프로젝트 선택한다.
2. java로 선택한다. 버전은 추후에 1.8로 세팅할 예정이다.
3. 부트 버전은 최신버전으로 선택하였다. 조금 이상하지 않은가?
4. group, artifact이름을 임의로 정한다.
5. ADD DEPENDENCIES를 클릭하여 하기 4가지 라이브러리를 선택한다. 
    
다운로드 받아진 파일을 압축을 풀어 인텔리제이로 연다.

![legacy1_2](/assets/img/legacy1_2.png)

프로젝트를 여는 순간 라이브러리들이 다운로드 받아진다.

인텔리제이에서는 스프링부트 프로젝트이면 자동으로 우측상단에
어플리케이션을 실행할 수 있도록 run버튼이 활성화된다.

![legacy1_3](/assets/img/legacy1_3.png)

run버튼을 누르면 다음과 같은 로그가 나오면서 정상적으로 8080포트로
로컬에서 띄워지게된다.

이로써 첫번째 프로젝트 빌딩은 성공이다. 