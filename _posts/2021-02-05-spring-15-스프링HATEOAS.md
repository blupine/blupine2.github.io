---
layout: post
title:  "[스프링] REST API (2) - HATEOAS 구현"
subtitle: ""
date:   2021-02-05 22:11:21 +0900
categories: dev
tags : Spring
---

[구현 커밋]({{"https://github.com/blupine/studyolleh/commit/aa986de0044264f908cfebf06dc03378bf88c798"}}) 

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
- 애플리케이션 상태 전이의 late binding
  - 어디서 어디로 전이가 가능한지 미리 결정되지 않는다. 어떤 상태로 전이가 완료되고 나서야 그 다음 전이될 수 있는 상태가 결정된다. 
  - **링크는 동적으로 변경될 수 있다.**



--------------------------------------------------------------

의존성 추가
```gradle
implementation 'org.springframework.boot:spring-boot-starter-hateoas'
```

구현해보면?
----------------------------------------------

### 회원가입 API 예시

```java
@PostMapping("/signup")
public ResponseEntity signup(@RequestBody SignUpRequestDto signUpRequestDto) {
    Account account = accountService.createNewAccount(signUpRequestDto)
    SignUpResultDto signUpResultDto = modelMapper.map(account, SignUpResultDto.class);
    EntityModel<SignUpResultDto> entityModel = EntityModel.of(signUpResultDto);
    entityModel.add(WebMvcLinkBuilder.linkTo(RestAccountController.class).slash("signup").withSelfRel());
    entityModel.add(WebMvcLinkBuilder.linkTo(RestAccountController.class).slash("login").withRel("login"));
    return ResponseEntity.created(uri).body(entityModel);
}

```

EntityModel 클래스를 이용해서 해당 요청으로부터 상태 전이가 가능한 링크를 담아 반환해준다. 

----------------------------------------------

### 회원가입 API 예시 테스트 코드

```java
@DisplayName("회원가입 - 정상 입력")
@Test
void signupTest() throws Exception {
    /* given */
    SignUpRequestDto signUpRequestDto = SignUpRequestDto.builder()
            .nickname("blupine")
            .email("test@email.com")
            .password("asdfasdf")
            .build();

    /* when & then */
    mockMvc.perform(post("/api/signup")
            .contentType(MediaType.APPLICATION_JSON)
            .accept(MediaTypes.HAL_JSON)
            .content(objectMapper.writeValueAsString(signUpRequestDto)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("nickname").hasJsonPath())
            .andExpect(jsonPath("email").hasJsonPath())
            .andExpect(jsonPath("joinedAt").hasJsonPath())
            .andExpect(jsonPath("url").hasJsonPath())
            .andExpect(jsonPath("_links").hasJsonPath())
            .andExpect(jsonPath("_links.self").hasJsonPath())
            .andExpect(jsonPath("_links.login").hasJsonPath());
            
    /* then */
    Account accountAfter = accountRepository.findByNickname("blupine");
    Assertions.assertNotNull(accountAfter);
}
```

-------------------------------------------------

### 요청 결과

```json
{
    "nickname": "testname1",
    "email": "Test@testemail.com",
    "joinedAt": null,
    "url": null,
    "_links": {
        "self": {
            "href": "http://localhost:8080/api/signup"
        },
        "login": {
            "href": "http://localhost:8080/api/login"
        }
    }
}
```