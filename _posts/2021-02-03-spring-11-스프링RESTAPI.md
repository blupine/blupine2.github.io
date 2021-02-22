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
  - **uniform interface**
  - layered system
  - code-on-demand (optional)
    - 서버에서 클라이언트에 코드를 보내 실행이 가능해야 함(e.g. javascript)

- 여기서 HTTP API만 사용하더라도 uniform interface를 제외한 대부분의 제약조건을 만족할 수 있음

##### Uniform Interface
- Identication of resources : 자원은 URI 식별되어야 함
- Manipulation of resources through representations : 메시지에 자원에 대한 행위가 표현되어야 함
- **Self-descriptive messages**
- **Hypermedia as the engine of application state (HATEOAS)**


##### Self-descriptive messages
- 메시지는 스스로를 설명해야 한다
- 에를들어 아래 메시지는 메시지 자체만 두고 봤을 때 어떻게 해석을 해야 하는지, 값들이 무엇을 의미하는지를 담지 않기 때문에 해당 조건을 만족하지 못함 
- ![1]({{"assets/img/dev/spring/11/1.png" | absolute_url}})


"웹을 독립적으로 발전시키 위한 






https://www.youtube.com/watch?v=RP_f5dMoHFc&t=767s


[블로그]({{"https://brunch.co.kr/@springboot/491"}})를 참고하면서 적용했고, 우선 적용을 해보는 것이 목표였기 때문에 JWT 토큰 생성 시 Role과 관련된 로직은 모두 제거해서 진행했다.

[구현 코드 커밋]({{"https://github.com/blupine/studyolleh/commit/f82e54d3ab2006164b43126d91d06f85b74cf7af"}})



먼저 의존성 추가를 위해 build.gradle에 아래 패키지를 추가해준다.
```xml
implementation 'io.jsonwebtoken:jjwt:0.9.1'
```
------------------------------------------------------


먼저 JWT 토큰을 생성, 처리, 검증의 역할을 수행하는 컴포넌트를 정의해준다.

##### JwtTokenProvider
```java
@Component
@RequiredArgsConstructor
public class JwtTokenProvider {
    //@Value("spring.jwt.secret")
    private String SECRET_KEY = "SECRET_KEY";

    private long tokenValidMilisecond = 1000L * 60 * 60; // 토큰 유효시간을 1시간으로 설정

    private final UserDetailsService userDetailsService;

    @PostConstruct
    protected void init() {
        SECRET_KEY = Base64.getEncoder().encodeToString(SECRET_KEY.getBytes());
    }

    // Jwt 토큰 생성
    public String createToken(String userPk) {
        Claims claims = Jwts.claims().setSubject(userPk);
        Date now = new Date();
        return Jwts.builder()
                .setClaims(claims)  // 데이터
                .setIssuedAt(now)   // 토큰 발행일자
                .setExpiration(new Date(now.getTime() + tokenValidMilisecond)) // 유효시간 설정
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY) // 암호화 알고리즘, secret값 세팅
                .compact();
    }

    // JWT 토큰 문자열에서 Authentication 구현체(UserDetails) 생성
    public Authentication getAuthentication(String token) {
        UserDetails userDetails = userDetailsService.loadUserByUsername(this.getUserPk(token));
        return new UsernamePasswordAuthenticationToken(userDetails, "", userDetails.getAuthorities());
    }

    // 토큰에서 회원 정보 추출
    public String getUserPk(String token) {
        return Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(token).getBody().getSubject();
    }

    // Request의 Header에서 token을 가져옮
    public String resolveToken(HttpServletRequest request) {
        return request.getHeader("X-AUTH-TOKEN");
    }

    // 토큰 유효성 + 만료일자 검증
    public boolean validateToken(String jwtToken) {
        try {
            Jws<Claims> claims = Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(jwtToken);
            return !claims.getBody().getExpiration().before(new Date());
        } catch (Exception e) {
            return false;
        }
    }
}
```

-----------------------------------------------------

이후 JWT 인증 필터를 직접 구현해야하는데, 여기서 위에서 구현한 JwtTokenProvider를 이용해서 요청으로부터 JWT 토큰을 가져오고 토큰을 검사해서 시큐리티 컨텍스트에 Authentication 구현체를 설정해주면 인증이 완료된다.

##### JwtAuthenticationFilter
```java
@RequiredArgsConstructor
public class JwtAuthenticationFilter extends GenericFilterBean {

    private JwtTokenProvider jwtTokenProvider; // 컴포넌트가 아니므로 생성자 주입 필요

    public JwtAuthenticationFilter(JwtTokenProvider jwtTokenProvider) {
        this.jwtTokenProvider = jwtTokenProvider;
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        String token = jwtTokenProvider.resolveToken((HttpServletRequest) request);
        if (token != null && jwtTokenProvider.validateToken(token)) {
            Authentication authentication = jwtTokenProvider.getAuthentication(token); // 인증에 실패하면 Exception 발생
            SecurityContextHolder.getContext().setAuthentication(authentication); // 인증에 성공하면 인증 객체를 SecurityContext에 설정해줌
        }
        chain.doFilter(request, response);
    }
}
```

-----------------------------------------------------

그리고 이렇게 구현한 필터는 SecurityConfigureAdapter를 상속받는 클래스를 작성하고 필터를 생성해서 필터를 추가해준다.

##### JwtConfigurer
```java
public class JwtConfigurer extends SecurityConfigurerAdapter<DefaultSecurityFilterChain, HttpSecurity> {

    private JwtTokenProvider jwtTokenProvider; // 컴포넌트가 아니므로 생성자 주입 필요

    public JwtConfigurer(JwtTokenProvider jwtTokenProvider) {
        this.jwtTokenProvider = jwtTokenProvider;
    }

    @Override
    public void configure(HttpSecurity httpSecurity) {
        JwtAuthenticationFilter jwtAuthenticationFilter = new JwtAuthenticationFilter(jwtTokenProvider);
        httpSecurity.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

-----------------------------------------------------

위에서 구현한 클래스는 WebSecurityConfigurerAdapter을 상속받아 스프링 시큐리티의 설정을 진행했던 SecurityConfig에서 생성 및 등록을 해줬다.

##### SecurityConfig
```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final JwtTokenProvider jwtTokenProvider;
    private final CustomUserDetailsService customUserDetailsService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);

        http
                .authorizeRequests()
                .antMatchers("/api/login/**").permitAll()   // 로그인 api에 대해서 비인가 접근 허용

                .and()
                .apply(new JwtConfigurer(jwtTokenProvider)); 

```


------------------------------------------------------

여기까지 진행하면 JWT 기반 인증을 위한 설정은 모두 완료됐고, 이제 로그인 서비스를 구현해주면 된다.

여기서 AuthenticationManager에 전달하는 Authentication 객체는 UsernamePasswordAuthenticationToken으로 만들어서 전달한다.

##### LoginService
```java
@Service
public class LoginService {
    /* 생략 */

    public Optional<AccountDto> login(String username, String password) {

            UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, password);

            Authentication authentication = authenticationManager.authenticate(authenticationToken);

            SecurityContextHolder.getContext().setAuthentication(authentication);

            UserAccount principal = (UserAccount) authentication.getPrincipal();
            Account account = principal.getAccount();

            AccountDto accountDto = modelMapper.map(account, AccountDto.class);

            return Optional.ofNullable(accountDto);
        }

```

<br>

여기서 고민을 했던 것은 사용자 Nickname과 Email로 모두 인증이 가능하게끔 하고 싶은데 UsernamePasswordAuthenticationToken의 생성자에 어떤 principal(첫 번째 인자)를 전달해야 할지였다. 

프로젝트에서 커스텀 로그인을 처리하기 위해 이전에 UsernamePasswordAuthenticationToken을 생성할 때 아래처럼 커스텀한 UserDetails 클래스를 작성했는데(UserDetails를 구현한 User를 상속받음) 이거를 전달하면 될지 고민을 했었다.

##### UserAccount
```java
@Getter
public class UserAccount extends User {
    private Account account;

    public UserAccount(Account account) {
        // super()를 통해 User의 username, password 필드를 채워줌
        super(account.getNickname(), account.getPassword(), List.of(new SimpleGrantedAuthority("ROLE_USER")));
        this.account = account;
    }
}

/* UsernamePasswordAuthenticationToken 생성 */
UserAccount userAccount = userDetailsService.loadUserByUsername(username);
Authentication authentication = new UsernamePasswordAuthenticationToken(userAccount, password);
```


----------------------------------------------------------------------------------

사실 이 고민은 의미가 없었던 게 다음 사진을 보면 알 수 있다.

![1]({{"assets/img/dev/spring/10/1.png" | absolute_url}})

인증 필터를 거쳐서 UsernamePasswordAuthenticationToken을 AuthenticationManager 구현체(ProviderManager)에게 전달하면, 해당 매니저는 자신이 가지고 있는 AuthenticationProvider들을 순회하면서 인증을 시도해본다. 그리고 이 인증을 실제로 수행하는 AuthenticationProvider.autheticate() 메소드는 UserDetailsService의 loadUserByUsername을 호출해서 인증에 필요한 유저 정보 객체를 DB로부터 가져온다.

UserDetailsService는 이미 이전에 구현을 해둔 상태였고, 해당 구현체에서 이미 nickname과 email 어떤게 들어오더라도 사용자 정보를 DB로부터 불러와 UserDetails 구현체를 반환하도록 구현되어있었다.

##### CustomUserDetailService
```java
@Service
@RequiredArgsConstructor
public class CustomUserDetailsService implements UserDetailsService {

    private final AccountRepository accountRepository;

    @Transactional(readOnly = true)
    @Override
    public UserDetails loadUserByUsername(String eamilOrNickname) throws UsernameNotFoundException {
        Account account = accountRepository.findByEmail(eamilOrNickname);
        if(account == null){
            account = accountRepository.findByNickname(eamilOrNickname);
        }
        if(account == null){
            return null;
        }
        return new UserAccount(account);
    }
}
```

***결국 UsernamePasswordAuthenticationToken 생성자의 첫 번째 파라미터(principal)에 nickname 또는 email 아무거나 전달하더라도 커스텀해둔 CustomUserDetailsService를 통해서 UserDetails를 받아올 수 있다.***

<br>

--------------------------------------------------------------------

**UsernamePasswordAuthenticationToken 생성 이후에 전체적인 인증 진행 과정을 따라가보면서 그 원리를 더 자세하게 알 수 있다.**

#### 1. ProviderManager

![2]({{"assets/img/dev/spring/10/2.png" | absolute_url}})

AuthenticationManager의 구현체로 Authenticaion 구현체(UsernamePasswordAuthenticationToken)를 위의 authentcion 메소드에 전달한다. 이후 해당 매니저는 자신이 가지고 있는 Provider들을 순회하면서 authentication 메소드를 호출하며 인증을 위임한다.(182번줄)

<br>

#### 2. AbstractUserDetailsAuthenticationProvider

![3]({{"assets/img/dev/spring/10/3.png" | absolute_url}})

AuthenticationProvider의 구현체로 UsernamePasswordAuthenticationToken 객체를 인증할 때 해당 구현체의 authentication 메소드로 인증이 위임된다. 127번 라인의 determineUsername 메소드 호출을 통해서 토큰 객체로부터 Username이란 것을 가져오는데, 후에 UserDetailsService의 loadUserByUsername 호출을 위해서 가져온다.

그러면 토큰(Authentication)의 어떤걸 username으로 가져오는가??


![4]({{"assets/img/dev/spring/10/4.png" | absolute_url}})

determineUsername 메소드를 보면 Authentication의 getName()을 호출한다는 것을 알 수 있다. 그러면 UsernamePasswordAuthenticationToken의 getName()을 호출하는 것인데, 이는 다음과 같이 구현되어있다.


![5]({{"assets/img/dev/spring/10/5.png" | absolute_url}})

즉, UsernamePasswordAuthenticationToken의 첫 번째 인자로 전달한 principal(위에서는 login 서비스로 전달된 username 문자열로 전달함)은 Account의 nickname, email, UserAccount(UserDetails 구현체) 그 어떤게 와도 상관이 없다.

UserAccount를 전달했다면 72번 라인에서 UserAccount의 getUsername()으로 반환될것이고, 위에서 했던 것 처럼 단순히 username으로 전달을 하더라도 80번 라인을 통해서 결과적으로 인증을 요청한 username만 남게된다.

<br>

![3]({{"assets/img/dev/spring/10/3.png" | absolute_url}})

결국 determineUsername의 호출 결과는 인증을 요청한 username(nickname 또는 email)만 남게된다. 그렇게 남게된 username은 133번 라인에서 retrieveUser라는 메소드에 전달되는데, 이는 다음과 같이 구현되어 있다.


#### 3. DaoAuthenticationProivder

![6]({{"assets/img/dev/spring/10/6.png" | absolute_url}})

결국 마지막으로 인증을 위임받게 된 DaoAuthenticationProvider가 UserDetailsService의 loadUserByUsername 메소드에 username을 전달하여 UserDetails를 전달받고, 이를 가지고 실제 비교를 통한 인증이 진행된다.

