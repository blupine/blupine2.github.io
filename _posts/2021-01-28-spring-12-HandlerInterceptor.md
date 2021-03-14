---
layout: post
title:  "[스프링] 핸들러 인터셉터"
subtitle: ""
date:   2021-01-28 21:09:41 +0900
categories: dev
tags : Spring
---



#### HandlerInterceptor란?
- 특정한 URI 호출을 가로채서 클라이언트 요청이 컨트롤러에 가기 전, 응답이 클라이언트에게 가기 전에 가로챔
- `DispatcherServlet`이 컨트롤러를 요청하기 전, 후에 요청과 응답을 가로챔

1. **preHandle(request, response, handler)**
   - 컨트롤러 이전에 수행할 동작을 정의
2. **postHandle(request, response, handler, modelAndView**
   - 컨트롤러 동작 이후에 수행할 동작 정의
   - Spring MVC의 DispatcherServlet이 화면을 처리하기 전에 동작함 
3. **afterCompletion(request, response, handler, exception)**
   - Spring MVC의 DispatcherServlet이 화면을 처리한 이후에 동작


#### Filter와 HandlerInterceptor의 차이?
- ![1]({{"assets/img/dev/spring/9/1.png" | absolute_url}})

1. Filter는 DispatcherServlet 앞단에서 처리를 하고 Interceptor는 DispatcherServlet에서 Handler(Controller)에 가기 전에 처리를 함
2. Filter는 J2EE 표준 스펙에 정의, 인터셉터의 경우 스프링 프레임워크에서 제공하는 기능

인코딩, 보안 관련 처리와 같은 애플리케이션 전역적으로 처리해야 하는 것은 Filter에서 처리,

인증, 인가와 같은 디테일한 처리는 인터셉터에서 주로 처리함



#### Interceptor 등록


- [구현 코드 커밋]({{"https://github.com/blupine/studyolleh/commit/f078fb54529c1702a85fcab168d19a1c4c60a4e6"}})

##### HandlerInterceptor 인터페이스 구현체 작성
```java
/* 모든 요청에 대해서 notification이 있는지 검사 후 model 속성에 추가해줌 */
@Component
@RequiredArgsConstructor
public class NotificationInterceptor implements HandlerInterceptor {

    private final NotificationRepository notificationRepository;
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        if (modelAndView != null && authentication != null && authentication.getPrincipal() instanceof UserAccount) {
            Account account = ((UserAccount) authentication.getPrincipal()).getAccount();
            Long count = notificationRepository.countByAccountAndChecked(account, false);
            modelAndView.addObject("hasNotification", count > 0);
        }
    }


}

```
------------------------------------------

##### HandlerInterceptor 등록

```java
@Configuration
@RequiredArgsConstructor
public class WebConfig implements WebMvcConfigurer {

    private final NotificationInterceptor notificationInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(notificationInterceptor);
    }
}
```




