---
layout: post
title:  "[스프링 시큐리티] remeberMe 사용"
subtitle: "세션이 만료된 유저 기억하기"
date:   2021-01-27 21:09:41 +0900
categories: dev
tags : Spring
---

### remeberMe?
- 매번 로그인을 하지 않고 로그인 상태를 유지하고 싶을 때?(로그인 상태 유지)
- 인증 정보(username, password 등)를 해시해서 쿠키로 보관
- 쿠키가 탈취될 경우 그대로 계정 로그인이 가능
  - 그래서 매번 바뀌는 토큰, 시리즈(랜덤, 고정) 등의 정보를 조합해서 쿠키를 새로 생성
  - db에 저장해서 요청으로 들어온 쿠키와 비교해야 함
  
```java
http.rememberMe()
        .userDetailsService(accountService)
        .tokenRepository(tokenRepository());
```

- userDetailService :
  - UserDetailService의 구현체가 전달되어야 함
- tokenRepository :
  - 토큰 저장에 사용할 레포지토리를 전달해줘야 한다
  - ```java
      @Bean
      public PersistentTokenRepository tokenRepository() {
          JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
          jdbcTokenRepository.setDataSource(dataSource);
          return jdbcTokenRepository;
      }
    ```
  - JdbcTokenRepositoryImpl :
    - PersistentTokenRepository 인터페이스를 Jdbc 기반으로 구현한 토큰 레포지토리
    - dataSource의 경우 `autowired`로 받아와서 넘기면 됨
    - ![1]({{"assets/img/dev/spring/1/1.png" | absolute_url}})
    - 내부적으로 `persistent_logins`라는 테이블을 정의해서 사용함
    - 따라서 매핑이 되는 테이블을 엔티티로 만들어줘야 함
    - ```java
        @Table(name = "persistent_logins")
        @Entity
        public class PersistentLogins {
            @Id
            @Column(length = 64)
            private String series;

            @Column(nullable = false, length = 64)
            private String username;

            @Column(nullable = false, length = 64)
            private String token;

            @Column(nullable = false, name="last_used", length = 64)
            private LocalDateTime lastUsed;
        }

      ```
