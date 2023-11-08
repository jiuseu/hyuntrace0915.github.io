---
layout: post
title:  "스프링 시큐리티 2편 - 인증과 인가/권한" 
date:    2023-11-08 23:03:56 +09:00
categories: jekyll update
---

인증과 인가
=============

스프링 시큐리티 전체를 관통하는 가장 중요한 개념은 인증과 인가라는 개념이다.<br>
 
 - 인증: '스스로 증명하다'라는 뜻이며 대체로 로그인 개념이다.<br>
         인증을 위해서 사용자는 자신이 알고 있는 자신의 정보를 제공하는데 아이디와 패스워드가 이에 속함<br>

 - 인가: '허가나 권한'이라는 개념과 같다.<br>
         인증이 된 사용자라고 해도 이에 접근할 수 있는 권한이 있는지를 확인하는 과정을 의미한다.<br>


---------------------------------------

UserDetailService
-------------

스프링 시큐리티에서 가장 중요한 객체는 실제로 인증을 처리하는 ```UserDetailService```라는 인터페이스의 구현체이다.<br> 
```UserDetailService```인터페이스는 ```loadUserByUsername()```이라는 단 하나의 메소드를 가지는데 이것이 실제 인증을 처리 할 때 호출된다.<br>
코드 구현은 ```CustomSecurityConfig```에서 로그인 화면에서 로그인을 진행한다는 설정을 추가하고, 프로젝트에 ```CustomUserDetailService``` 클래스를 생성한다.<br>

- CustomSecurityConfig 클래스

```java
@Log4j2
@RequiredArgsConstructor
@Configuration
public class CustomSecurityConfig{

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http)throws Exception{

        log.info("-------------------configure-------------------");

        http.formLogin(Customizer.withDefaults());
        
        return http.build();
    }

}
```


- CustomUserDetailsService 클래스

```java
@Log4j2
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String username)throws UsernameNotFoundException{

        log.info("loadUserByUsername: "+username);

        return null;
    }

    
}
```
<br>

이렇게 프로젝트를 실행하고 로그인 페이지를 호출하여 아무 내용으로 로그인 처리하면 정상적으로 처리되지는 않겠지만 ```UserDetailService```의 ```loadUserByUsername()```가 실행되는 것을 확인 할 수 있다.<br>

![20231108-loaduserbyusername](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/73965fa9-4134-4edb-873f-6afd7b15318c)


```UserDetailService```는 사용자 인증과 관련된 정보들을 저장하는 역할을 한다.<br>
스프링 시큐리티는 ```UserDetails```타입의 객체를 이용해서 패스워드를 검사하고, 사용자 권한을 확인하는 방식으로 동작한다.<br>



---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
