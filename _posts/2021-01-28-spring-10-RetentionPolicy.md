---
layout: post
title:  "[스프링] RetentionPolicy"
subtitle: ""
date:   2021-01-28 21:10:41 +0900
categories: dev
tags : Spring
---

이전 포스팅에서 `@WithAccount`라는 커스텀 어노테이션을 만들면서 `@Retention`라는 어노테이션을 사용했었다.

해당 어노테이션이 왜 쓰이는지 궁금해져서 찾아봤다.

#### @Retention
`@Retention` 어노테이션은 해당 어노테이션이 언제까지 유지되는지를 정의하는 어노테이션이다.

다음 세 가지 정책이 있다.


- `RetentionPolicy.SOURCE`
    - 해당 어노테이션을 컴파일 전 소스코드 단계에서만 유지를 하는 정책
    - 따라서 컴파일한 `.class` 파일에서는 해당 어노테이션이 삭제된다. 
- `RetentionPolicy.CLASS`
    - Default 정책
    - 컴파일 이후 `.class` 파일까지도 어노테이션이 유지되지만 실행되는(RUNTIME) 시점에는 사라진다
- `RetentionPolicy.RUNTIME`
    - 해당 어노테이션을 실행 시점에도 유지한다


#### @WithAccount

따라서 이전에 작성했던 `@WithAccount` 어노테이션에서는 해당 어노테이션이 실행 시점에 사용하기 위함이므로 ReteiontionPolicy.RUNTIME을 하는 것이 맞다.

```java
@Retention(RetentionPolicy.RUNTIME)
@WithSecurityContext(factory = WithAccountSecurityContextFactory.class)
public @interface WithAccount {
    String value();
}
```
