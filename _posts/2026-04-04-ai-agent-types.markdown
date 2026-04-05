---
layout: docs
title: "AI 코딩 에이전트 툴 입문 : gemini cli 무료, 윈도우 사용자"
date: 2026-04-04 10:30:00 +0900
categories: AI 에이전트 활용
badges:
- type: primary
  tag: AI Agent
---

\- spring boot 프로젝트 구성하고 서비스 개발중  
\- 테스트 코드 작성 필요  
\- 이참에 ai를 활용해보자고 생각이 들었다.  
\- 원래라면 클로드 코드를 사용해 볼 생각이었다.  
\- 무료로 쓸 수 있는 것들은 어떠할까 생각이 들었다.

\*\* 나는 **윈도우**, **인텔리제이** 유저 이다. \*\*

![1.png](/assets/img/posts/2026-04-04-ai-agent-types/1.png)

이렇게 사용하게된 무료, 윈도우, 인텔리제이의 gemini cli 사용기

## 1\. 설치

1-1. Node.js 설치 필요  
\- 최소 필수 버전: Node.js v20 이상 권장 (v20.x LTS 버전 이상)

> 1\. nodejs.org에 접속  
> [https://nodejs.org/ko/download](https://nodejs.org/ko/download "https://nodejs.org/ko/download")  
> 2\. LTS로 표시된 버튼 클릭, 최신 LTS 버전 다운로드 (msi 설치)  
> 3\. 설치 파일 실행 후 기본값(Next → Next → Finish)으로 진행  
> 4\. 터미널에서 설치 확인  
> node -v / npm -v 

1-2. cmd창 띄워서 다음을 입력하면 gemini-cli를 설치할 수 있다.

> npm install -g @google/gemini-cli

1-3. 인텔리제이 켜서 터미널에서 실행해도 되고, 그냥 인텔리제이 내장되어있는거 써도 된다.  
나는 그냥 터미널에서 실행해봤다.  
\- 윈도우 유저인 나, 파워쉘로 실행해보려고 했는데 한글명령 입력이 매우 힘들었다.

## 2\. 계정 인증

\- 계정 인증하지 않아도 사용할 수 있다 (제한된 토큰 약 100 ~ 250회)  
\- 계정 인증하면 더 많이 사용할 수 있다 (Login with Google 사용하여 로그인, 약 1,000 ~ 1,500회)  
**\*\* 무료 계정 사용 시 : 입력데이터는 Google의 모델 개선에 사용될 수 있음 !!**

1\. 처음에는 /auth 먼저 입력해서 1 - 구글 로그인을 해주도록 했다  
2\. 로그인하면 [https://aistudio.google.com/app/u/2/usage](https://aistudio.google.com/app/u/2/usage) 사이트에서 사용량을 확인해볼 수 있다.  
3\.  /stats 입력하면 사용량을 쉘에서 바로 확인할 수 있다.

토큰 사용량 기준은 아직 모르겠다.

## 3\. 프로젝트 파악하게 하기

3-1. aiignore파일 생성  
\- .aiignore 파일을 생성하여 개인정보 보호되도록 해준다.  
\- 나의 경우 이정도로 설정해주었는데, gitignore과 동일하다.

> \# 설정 파일 및 비밀 정보 제외  
> global.properties  
> \*.properties  
> \*.xml  
> .env  
> \# 인텔리제이 설정 파일 제외  
> .idea  
> \*.iml  
> out  
> gen  
> target

3-1. GEMINI.md 파일 생성  
\- /init 을 해서 필수 환경을 파악하게 한다.  
\- 생성된 GEMINI.md 파일에서 쓸모없어 보이거나 중요한 내용은 삭제하고 쓰면 됐다.

참고 : [https://goddaehee.tistory.com/371](https://goddaehee.tistory.com/371) \[갓대희의 작은공간:티스토리\]