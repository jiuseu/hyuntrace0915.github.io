---
layout: post
title:  "OAuth2 KAKAO Error" 
date:    2023-12-12 22:28:12 +09:00
categories: jekyll update
---

### This class supports 'client_secret_basic', 'client_secret_post', and 'none' by default

![20231212-client_secret_post-error](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/9d012073-42db-4e63-b1d0-ddf63a4f8d2e)

오늘 소셜 로그인을 프로젝트와 연동하기 위해서 여러 라이브러리 및 프로젝트 설정과 동시에 카카오 개발자 서비스에 설정을 하고 프로젝트를 실행해 로그인 웹페이지로 이동해 카카오 로그인을 시도한 결과 위와 같은 에러 코드가 출력이 되면서 웹페이지에 500에러가 발생하였다.<br>

분명 책에서 하라는 대로 했고 오탈자가 있는지 몇 번이나 확인했지만 에러의 원인을 찾지 못하였다.
그래서 다시 위에 출력된 에러 코드를 읽어보았는데 대충 ```post``` 대신 ```client_secret_basic```, ```client_secret_post``` 및 ```none```을 지원한다는 것이었다.<br>

- application.properties (before)

```java
spring.security.oauth2.client.provider.kakao.authorization-uri=https://kauth.kakao.com/oauth/authorize
spring.security.oauth2.client.provider.kakao.user-name-attribute=id
spring.security.oauth2.client.provider.kakao.token-uri=https://kauth.kakao.com/oauth/token
spring.security.oauth2.client.provider.kakao.user-info-uri=https://kapi.kakao.com/v2/user/me

spring.security.oauth2.client.registration.kakao.client-name=kakao
spring.security.oauth2.client.registration.kakao.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.kakao.redirect_uri=http://localhost:8080/login/oauth2/code/kakao
spring.security.oauth2.client.registration.kakao.client-id=

spring.security.oauth2.client.registration.kakao.client-secret=
spring.security.oauth2.client.registration.kakao.client-authentication-method=post // 바꿔줄 부분
spring.security.oauth2.client.registration.kakao.scope=profile_nickname,account_email

```

<br>

```spring.security.oauth2.client.registration.kakao.client-authentication```의 ```post```부분을 ```client_secret_post```으로 변경을 해보도록 하겠다.

<br>

- application.properties (after)

```java

 ...

spring.security.oauth2.client.registration.kakao.client-authentication-method=client_secret_post

 ...

```

<br>

변경 후 다시 프로젝트를 실행해 카카오 로그인을 시도한 결과 정상적으로 진행이 되었다.

<br>


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/