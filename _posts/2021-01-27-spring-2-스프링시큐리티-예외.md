---
layout: post
title:  "[스프링] Form validation"
subtitle: "form validation"
date:   2021-01-28 21:09:41 +0900
categories: dev
tags : Spring
---


Controller에 전달되는 파라미터(form)를 검증할 수 있는 방법



#### Validator interface
- 전달받은 객체를 검증하고, 실패 시 errors를 설정해줌
  - `supports` : 파라미터로 전달된 클래스가 검증이 가능한지 여부를 반환
  - `validate` : 실제 검증 로직, 실패 시 errors 설정

### [예제 코드]({{"https://github.com/blupine/studyolleh/commit/be70a9ce1df259a1358c2d2e3162e852f0aa3dfb"}})


```java
@Component
@RequiredArgsConstructor
public class SignUpFormValidator implements Validator {

    private final AccountRepository accountRepository;
    
    @Override
    public boolean supports(Class<?> clazz) {
        return clazz.isAssignableFrom(SignUpForm.class);
    }

    @Override
    public void validate(Object target, Errors errors) {
        SignUpForm signUpForm = (SignUpForm) target;
        if (accountRepository.existsByEmail(signUpForm.getEmail())) {
            errors.rejectValue("email", "invalid.email", new Object[]{signUpForm.getEmail()}, "이미 사용중인 이메일입니다.");
        }
    }
}
```  

```java
@Controller
@RequiredArgsConstructor
public class AccountController {

    private final SignUpFormValidator signUpFormValidator;

    @InitBinder("signUpForm")  // signUpForm에만 Validator 적용
    public void initBinder(WebDataBinder webDataBinder) {
        webDataBinder.addValidators(signUpFormValidator);   
    }
}

```