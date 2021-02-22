---
layout: post
title:  "[스프링] REST API (1) - REST API란?"
subtitle: ""
date:   2021-02-03 22:11:21 +0900
categories: dev
tags : Spring
---


["그런 REST API로 괜찮은가"]({{"https://brunch.co.kr/@springboot/491"}})를 보고 평소에 알고있던 REST API와는 많이 다르다는 생각을 했다.

#### REpresentational State Transfer


#### 먼저 우리가 흔히 알고있는 REST API란?
##### Microsoft REST API Guidelines (2016)
- URI는 https://{serviceRoot}/{colelction}/{id} 형식이어야 한다
- GET, PUT, DELETE, POST, HEAD, PATCH, OPTIONS를 지원해야 한다
- API 버저닝은 Major.minor로 하고 URI에 버전 정보를 포함시킨다
- 등등..

이를 본 Roy T.Fielding(REST 개념을 처음 제시한 사람)은 **'이건 REST API가 아니고 HTTP API이다' **


**"REST API를 위한 최고의 버저닝 전략은 버저닝을 안 하는 것"**



##### REST API?
- REST 아키텍처를 따르는 API

##### REST?
- 분산 하이퍼미디어 시스템(e.g. 웹)을 위한 아키텍처 스타일(=제약조건의 집합)
- 즉, 이 제약조건들을 모두 지켜야 REST를 따른다라고 할 수 있음
- REST를 구성하는 다른 아키텍처 스타일들
  - client-server
  - stateless
  - cache
  - `uniform interface`
  - layered system
  - code-on-demand (optional)
    - 서버에서 클라이언트에 코드를 보내 실행이 가능해야 함(e.g. javascript)

- 여기서 HTTP API만 사용하더라도 uniform interface를 제외한 대부분의 제약조건을 만족할 수 있음

##### Uniform Interface
- Identication of resources : 자원은 URI 식별되어야 함
- Manipulation of resources through representations : 메시지에 자원에 대한 행위가 표현되어야 함
- `Self-descriptive messages`
- `Hypermedia as the engine of application state (HATEOAS)`


##### Self-descriptive messages
- 메시지는 스스로를 설명해야 한다
- 에를들어 아래 메시지는 메시지 자체만 두고 봤을 때 어떻게 해석을 해야 하는지, 값들이 무엇을 의미하는지를 담지 않기 때문에 해당 조건을 만족하지 못함 
- ![1]({{"assets/img/dev/spring/11/1.png" | absolute_url}})

- 반대로 아래와 같이 추가를 해주면?
- ![2]({{"assets/img/dev/spring/11/2.png" | absolute_url}})
- Content-type을 보고 메시지가 JSON 형식임을 알 수 있고, json-patch라는 명세를 찾아가 메시지(json)을 해석하도록 해줌

##### HATEOAS
- 애플리케이션의 상태는 하이퍼링크를 통해 전이되어야 함
- html은 href를 통해서 링크로 상태 전이를 하므로 HATEOAS를 만족함
- JSON에서는?
  - LINK 헤더를 사용하면 메시지에 링크를 포함할 수 있음
  - LINK 헤더는 이미 표준이 나와있는 거라서 누구나 해석이 가능
- ***HATEOAS를 왜 해야하냐?***
  - 서버와 클라이언트가 각각 독립적으로 진화한다.
  - **서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다**
    - 즉, 신규 API 버전이 나와도 변경된 링크로 클라이언트에게 전달하고, 클라이언트가 서버 메시지를 해석한 링크로 상태 전이를 한다면 클라이언트는 수정될 필요가 없음
