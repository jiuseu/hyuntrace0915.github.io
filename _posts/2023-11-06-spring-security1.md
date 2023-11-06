---
layout: post
title:  "스프링 시큐리티 1편 - 스프링 웹 시큐리티 기본 설정 및 적용" 
date:    2023-11-06 22:35:11 +09:00
categories: jekyll update
---

스프링 시큐리티 적용하기
=============

인터넷을 쓴다고 하면 어디 사이트든 로그인을 사용하면서 자신의 개인 정보를 지키는 보안 기능을 알고 있을 것이다.<br>
이러한 보안 기능인 스프링 시큐리티를 적용하려면 추가적인 라이브러리가 필요하므로 ```build.gradle```파일의 ```dependencies```에 스프링 시큐리티 라이브러리를 추가하는 것이 첫단계이다.<br>


```
dependencies {
    ...

    implementation 'org.springframework.boot:spring-boot-starter-security'

}
```
---------------------------------------

스프링 시큐리티 관련 설정 추가
-------------

스프링 시큐리티 설정을 코드를 이용해 조정하는 경우가 많기에 별도의 클래스를 생성하여 설정을 조정할 수 있다.<br> 

```java
@Log4j2
@RequiredArgsConstructor
@Configuration
public class CustomSecurityConfig{

}
```

이렇게 ```@Configuration``` 어노테이션을 적용하고, 프로젝트를 실행하면 아래와 같은 알 수 없는 패스워드가 생성되어 로그에 출력될 것을 볼 수 있다.<br>
물론 패스워드는 매번 다르게 생성되나, 사용자의 아이디는 'user'로 고정이다.<br>

![20231106-password](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/62ebe40f-db94-4a10-bbc6-d3222e73e194)

<br>


실행된 프로젝트에서 작업중인 웹페이지에 접근을 시도하면 밑에 이미지처럼 로그인 처리가 필요한 것을 알 수 있다.<br>

![20231106-loginpage](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/d8a7197b-cbfb-4391-ab33-f8b91b2be477)

<br> 


그렇다면 로그인하지 않아도 볼 수 있도록 설정하고 싶다면 개발자가 직접 설정하는 코드가 반드시 있어야 하기 때문에 ```SecurityFilterChain``` 객체를 반환하는 메소드를 생성하자.<br>

```java
@Log4j2
@RequiredArgsConstructor
@Configuration
public class CustomSecurityConfig{
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http)throws Exception{

        log.info("-------------------configure-------------------");

        //http.formLogin(Customizer.withDefaults());
        http.formLogin(form -> {

            form.loginPage("/member/login");

        });
        http.csrf(httpSecurityCsrfConfigurer -> httpSecurityCsrfConfigurer.disable());
        return http.build();
    }
    
}

``` 

```filterChain```를 작성하고 다시 프로젝트를 실행하여 프로젝트의 웹페이지에 바로 접근할 수 있다.<br>
당연 ```SecurityFilterChain```의 ```filterChain```설정은 모든 사용자가 모든 경로에 접근할 수 있게 된다.<br>
```filterChain```으로 이제 최소한의 설정으로 필요한 자원의 접근을 제어할 수 있을 것이다.<br>

---------------------------------------

로그 레벨 조정
-------------

이러한 스프링 시큐리티의 기능은 웹에서 사용되는 필터(filter)들이 단계별로 동작하고 있다.<br>
그래서 에러가 발생하면 어느 필터 단계에서 어떤 문제가 발생하였는지 알 수 있도록 ```aplication.properties```의 로그 설정을 최대한 낮게 설정해서 관련된 에러를 볼 수 있도록 하는 것이 좋을 것이다.<br> 

```
logging.level.org.springframework=info
logging.level.com.example=debug

logging.level.org.springframework.security=trace
```

<br>

설정을 추가하고 다시 웹페이지를 호출하면 로그에서 보지 못했던 코드들이 출력되는 것을 볼 수 있을 것이다.<br>

![20231106-logleveltrace](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/e1dc981b-143e-470b-96fa-3a3fc9e2a3d9) <br>

---------------------------------------

정적 자원의 처리
-------------

필터가 단순한 css파일이나 js파일에도 적용이 되고 있기 때문에 프로젝트에서 완전히 정적으로 동작하는 파일들에게 굳이 시큐리티를 적용할 필요가 없기 때문에 ```CustomSecurityConfig```에 새로운 ```webSecurityCustomizer```메소드를 생성한다. <br>

```java
@Bean
public WebSecurityCustomizer webSecurityCustomizer(){

   log.info("-------------------web configure-------------------");
   return (web) -> web.ignoring().requestMatchers(
    PathRequest.toStaticResources().atCommonLocations());
}
```

<br>

위의 메소드 코드들로 설정하면 정적 파일들은 스프링 시큐리티 적용에서 제외시킬 수 있다.<br>
예시를 들어 ```/css/styles.css```를 호출하면 필터가 동작하지 않는다.<br>

![20231106-stylecss](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/2e2aa5e3-fca4-4d36-a698-cd3dcdbdf003)


---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
