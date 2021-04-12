---
layout: post
title:  "웹 서버, 웹 애플리케이션 서버"
subtitle: ""
date:   2021-01-18 22:09:41 +0900
categories: dev
tags : Spring
---


### 웹 서버(Web ServeR)?
- HTTP 기반으로 동작
- 정적 리소스 제공 (html, css, js, image 등)
- 기타 부가기능
  - 클라이언트의 요청을 WAS에 전달, WAS의 처리 결과를 클라이언트에게 전달
- Apache, Nginx, 

### 웹 애플리케이션 서버(WAS - Web Application Server)
- HTTP 기반으로 동작
- 웹 서버 기능 포함 + 동적 리소스 제공 가능
- 프로그램의 코드를 실행해서 `애플리케이션 로직` 수행
  - 동적 HTML, HTTP API(JSON)
  - 서블릿, JSP, 스프링 MVC
- Tomcat, Jetty, Undertow


### 웹 서버, 웹 애플리케이션 서버(WAS)의 차이
- WAS는 `애플리케이션 로직`을 수행하는 것에 특화되어 있음


### 웹 시스템의 구성 - WEB, WAS, DB
- 정적 리소스는 웹 서버가 처리
- 웹 서버는 애플리케이션 로직같은 동적인 처리가 필요할 경우 WAS에 요청을 위임
- WAS는 애플리케이션 로직의 처리를 전담
- 웹 서버를 사용하지 않을 경우?
  - WAS가 웹 서버의 역할을 하기 때문에 상관은 없음
  - 그러나 WAS가 너무 많은 역할을 담당하기 때문에 서버 과부하 가능
  - 단순한 정적 리소스 요청 때문에 중요한 비즈니스로직(애플리케이션 로직)이 수행에 어려울 수 있음
  - 장애 발생 시 오류 화면을 띄워주는 것도 불가능..(서버가 죽었으니..)
![1]({{"assets/img/dev/spring/0/1.png" | absolute_url}})

