---
layout: post
title:  "[스프링] @Profile"
subtitle: "profioe"
date:   2021-01-28 21:09:41 +0900
categories: dev
tags : Spring
---

로컬 개발 환경, 테스팅 환경, 운영 환경에 따른 다른 프로필, 프로퍼티를 사용하는 방법

##### @Profile("name")

- **Component**
```java
@Profile("local") // spring profiles에서 active가 local일 때만 사용
@Slf4j
@Component
public class ConsoleMailSender implements JavaMailSender {
    @Override
    public MimeMessage createMimeMessage() {
        return null;
    }
```

- **application.properties**
```xml
spring.profiles.active=local
```