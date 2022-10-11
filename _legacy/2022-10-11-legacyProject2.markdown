---
layout: legacy
title: 스프링부트 레거시 프로젝트 2 - 첫번째 페이지 띄우기
date: 2022-10-11 14:03:00 +0900
categories: 스프링부트_레거시프로젝트
badges:
- type: primary
  tag: 스프링부트
- type: primary
  tag: 레거시 프로젝트
---
다음과 같이 폴더 트리를 만들었다.
처음부터 잘 정리해놓으면 편하게 기능을 개발할 수 있다. 

![legacy2](/assets/img/legacy2.png)

크게 2가지(클래스, 리소스)폴더로 나눠서 정리한다. 
> ### java(클래스) 폴더 / com.yeahn 패키지
> #### 1. common : 공통적으로 범용적으로 사용할 클래스를 모아놓는다
> #### 2. config : 설정에 관련된 클래스를 모아놓는다.
> #### 3. dao : mybatis 매퍼클래스들을 모아놓는다.
> #### 4. model : dto, vo의 기능을 하는 모델을 모아놓는다.
> #### 5. web : controller, service클래스를 모아놓는다.

> ### resources(리소스) 폴더
> #### 1. query : 쿼리xml을 모아놓는다.
> #### 2. static : css, js, imges 등을 모아놓는다.
> #### 3. templates : 마크업에 관련된 mustache 파일들을 모아놓는다.

이 상태에서 
1. templates폴더에 index.mustache파일을 추가
2. web패키지에 controller 패키지 추가하고 Main클래스를 추가한다.
3. 그리고 다음과 같이 컨트롤러 소스를 추가하여 index.mustache파일을 바라보도록 한다.
```java
package com.yeahn.web.controller;

import com.yeahn.model.YeahnTable;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class Main {

    @RequestMapping("/index")
    public ModelAndView index(Model model){
        // mustache 를 사용하게 되면 앞에 경로(src/main/resources/templates)와
        // 뒤에 확장자(.mustache)는 생략을 한 순수한 파일의 이름만 반환하면 된다.
        // 아래와 같이 index 를 반환하게 되면
        // src/main/resources/templates/index.mustache 로 변환되어 View Resolver 가 처리하게 된다.
        ModelAndView mv = new ModelAndView();

        mv.setViewName("index");
        return mv;
    }

}
```

뷰페이지는 아름다운 디자인을 위해 무료로 배포된 css, js스타일을 사용했다.
https://colorlib.com/wp/ 이 홈페이지에 배포된 파일을 사용하였다.
이는 static폴더에 css, js폴더에 각각 넣어준다.

퍼블리싱하고, 페이지를 띄워본다.
![legacy2_2](/assets/img/legacy2_2.png)
이러한 형태의 대시보드로 홈페이지를 만들것이다.