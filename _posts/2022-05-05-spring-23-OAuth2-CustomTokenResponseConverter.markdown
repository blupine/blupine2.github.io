---
layout: post
title:  "[스프링 시큐리티] OAuth2.0 - OAUth2 Token Response 커스텀하기"
subtitle: ""
date:   2022-05-05 17:37:55 +0900
categories: dev
tags : Spring
---

Spring 프로젝트에서 OAuth2.0 인스타그램 로그인을 구현하던 도중 생기는 문제


- ![1]({{"assets/img/dev/spring/25/1.png" | absolute_url}})

```
[invalid_token_response] An error occurred while attempting to retrieve the OAuth 2.0 Access Token Response: Error while extracting response for type [class org.springframework.security.oauth2.core.endpoint.OAuth2AccessTokenResponse] and content type [application/json;charset=utf-8]; nested exception is org.springframework.http.converter.HttpMessageNotReadableException: An error occurred reading the OAuth 2.0 Access Token Response: tokenType cannot be null; nested exception is java.lang.IllegalArgumentException: tokenType cannot be null
```

tokenType이 null이 될 수 없고 해석되는데, 인스타그램은 AccessToken의 Type을 response에 포함해서 주지 않아서 발생하는 문제로 보인다. (Spring Security가 OAuth2AccessToken 인스턴스를 생성할 때 null 체크 및 예외 발생)

- ![2]({{"assets/img/dev/spring/25/2.png" | absolute_url}})

*OAuth2AccessToken.java, 해당 인스턴스를 생성할 때 tokenType이 null이면 예외가 발생한다.*

RFC 문서에는 해당 필드가 Option으로 표기되어 있어서 Provider에 따라 해당 필드를 주지 않는 곳들이 있는 것 같다...

다행히 이런 상황을 고려해서인지 스프링 시큐리티가 Access Token Response를 custom할 수 있는 확장 포인트를 제공해서, 아래와 같이 TokenType이 null일 경우 강제로 채워줘서 해결이 가능했다.

```java
public class CustomTokenResponseConverter implements Converter<Map<String, Object>, OAuth2AccessTokenResponse> {
    private static final Set<String> TOKEN_RESPONSE_PARAMETER_NAMES = new HashSet<>(
            Arrays.asList(OAuth2ParameterNames.ACCESS_TOKEN, OAuth2ParameterNames.EXPIRES_IN,
                    OAuth2ParameterNames.REFRESH_TOKEN, OAuth2ParameterNames.SCOPE, OAuth2ParameterNames.TOKEN_TYPE));

    @Override
    public OAuth2AccessTokenResponse convert(Map<String, Object> source) {
        String accessToken = getParameterValue(source, OAuth2ParameterNames.ACCESS_TOKEN);
        OAuth2AccessToken.TokenType accessTokenType = getAccessTokenType(source);

        // for instagram not answering tokentype....
        if(accessTokenType == null)
            accessTokenType = OAuth2AccessToken.TokenType.BEARER;

        long expiresIn = getExpiresIn(source);
        Set<String> scopes = getScopes(source);
        String refreshToken = getParameterValue(source, OAuth2ParameterNames.REFRESH_TOKEN);
        Map<String, Object> additionalParameters = new LinkedHashMap<>();
        for (Map.Entry<String, Object> entry : source.entrySet()) {
            if (!TOKEN_RESPONSE_PARAMETER_NAMES.contains(entry.getKey())) {
                additionalParameters.put(entry.getKey(), entry.getValue());
            }
        }
         // @formatter:off
        return OAuth2AccessTokenResponse.withToken(accessToken)
                .tokenType(accessTokenType)
                .expiresIn(expiresIn)
                .scopes(scopes)
                .refreshToken(refreshToken)
                .additionalParameters(additionalParameters)
                .build();
        // @formatter:on
    }
    private static String getParameterValue(Map<String, Object> tokenResponseParameters, String parameterName) {
        Object obj = tokenResponseParameters.get(parameterName);
        return (obj != null) ? obj.toString() : null;
    }

    private static OAuth2AccessToken.TokenType getAccessTokenType(Map<String, Object> tokenResponseParameters) {
        if (OAuth2AccessToken.TokenType.BEARER.getValue()
                .equalsIgnoreCase(getParameterValue(tokenResponseParameters, OAuth2ParameterNames.TOKEN_TYPE))) {
            return OAuth2AccessToken.TokenType.BEARER;
        }
        return null;
    }
    private static long getExpiresIn(Map<String, Object> tokenResponseParameters) {
        return getParameterValue(tokenResponseParameters, OAuth2ParameterNames.EXPIRES_IN, 0L);
    }
    private static Set<String> getScopes(Map<String, Object> tokenResponseParameters) {
        if (tokenResponseParameters.containsKey(OAuth2ParameterNames.SCOPE)) {
            String scope = getParameterValue(tokenResponseParameters, OAuth2ParameterNames.SCOPE);
            return new HashSet<>(Arrays.asList(StringUtils.delimitedListToStringArray(scope, " ")));
        }
        return Collections.emptySet();
    }
    private static long getParameterValue(Map<String, Object> tokenResponseParameters, String parameterName,
                                          long defaultValue) {
        long parameterValue = defaultValue;

        Object obj = tokenResponseParameters.get(parameterName);
        if (obj != null) {
            // Final classes Long and Integer do not need to be coerced
            if (obj.getClass() == Long.class) {
                parameterValue = (Long) obj;
            }
            else if (obj.getClass() == Integer.class) {
                parameterValue = (Integer) obj;
            }
            else {
                // Attempt to coerce to a long (typically from a String)
                try {
                    parameterValue = Long.parseLong(obj.toString());
                }
                catch (NumberFormatException ignored) {
                }
            }
        }

        return parameterValue;
    }
}
```

이렇게 CustomTokenResponseConverter를 구현했고 (사실 기본 Converter인 `DefaultMapOAuth2AccessTokenResponseConverter`를 거의 가져온 수준..)

아래 부분이 tokenType을 강제로 채워주는 부분이다.

```java
    // for instagram not answering tokentype....
    if(accessTokenType == null)
        accessTokenType = OAuth2AccessToken.TokenType.BEARER;
```


이렇게 새로 정의한 Converter를 아래와 같이 SecurityConfig에 적용해줘야 한다.


```java
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    ...
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        ...
            http.oauth2Login()
                ...
                .tokenEndpoint(token -> token.accessTokenResponseClient(this.accessTokenResponseClient()));
    }

    @Bean
    public OAuth2AccessTokenResponseClient<OAuth2AuthorizationCodeGrantRequest> accessTokenResponseClient() {
        DefaultAuthorizationCodeTokenResponseClient accessTokenResponseClient =
                new DefaultAuthorizationCodeTokenResponseClient();

        OAuth2AccessTokenResponseHttpMessageConverter converter = new OAuth2AccessTokenResponseHttpMessageConverter();
        converter.setAccessTokenResponseConverter(new CustomTokenResponseConverter());
        RestTemplate restTemplate = new RestTemplate(Arrays.asList(
                new FormHttpMessageConverter(), converter));
        restTemplate.setErrorHandler(new OAuth2ErrorResponseErrorHandler());

        accessTokenResponseClient.setRestOperations(restTemplate);
        return accessTokenResponseClient;
    }
}
```
