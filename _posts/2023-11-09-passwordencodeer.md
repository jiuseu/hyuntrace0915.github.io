---
layout: post
title:  "스프링 시큐리티 3편 - PasswordEncoder 및 어노테이션 시큐리티" 
date:    2023-11-09 22:41:02 +09:00
categories: jekyll update
---


PasswordEncoder
-------------

 스프링 시큐리티에서 비밀번호를 안전하게 저장할 수 있도록 비밀번호의 단방향 암호화를 지원하는 ```PasswordEncoder``` 인터페이스와 구현체들을 제공하고 있다.<br>
 ```PasswordEncoder```타입의 클래스 중에서 무난하게 많이 쓰이고 있는 것이 ```BCryptPasswordEncoder```라는 클래스이다.<br>

 ```BCryptPasswordEncoder```는 해시 알고리즘으로 암호화 처리되는데 같은 문자열이라고 해도 매번 해시 처리된 결과가 다르므로 패스워드 암호화에 많이 사용되고 있다.<br>

그럼 연습을 위해 ```CustomUserDetailsService```가 정상적으로 동작하기 위한 단계로 아래와 같이 설정한다.<br>

1. ```CustomSecurityConfig```에 ```PasswordEncoder```를 ```@Bean```으로 지정
2. ```CustomUserDetailsService```에 ```PasswordEncoder``` 주입

<br>

- CustomSecurityConfig 클래스

```java
@Log4j2
@RequiredArgsConstructor
@Configuration
public class CustomSecurityConfig{

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

}
```


- CustomUserDetailsService 클래스

```java
@Log4j2
@Service
public class CustomUserDetailsService implements UserDetailsService {

    private PasswordEncoder passwordEncoder;

    public CustomUserDetailsService(){ // 테스트를 위해 임시로 생성하여 동작
        this.passwordEncoder = new BCryptPasswordEncoder(); 
    }

    @Override
    public UserDetails loadUserByUsername(String username)throws UsernameNotFoundException{

        log.info("loadUserByUsername: "+ username);

        UserDetails userDetails = User.builder()
                .username("user1")
                .password(passwordEncoder.encode("1111"))
                .authorities("ROLE_USER")
                .build();

        return userDetails;
    }

    
}
```
<br>

프로젝트를 실행해서 로그인 페이지에 'user1 / 1111' 각각 아이디, 패스워드를 입력하여 로그인하면 지정한 경로로 이동하지만 지금은 아무것도 지정하지 않아 에러가 나긴 하지만 서버 로그를 보면 정상적으로 로그인된 것을 알 수 있다.<br>

![20231109-login-log](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/62810f34-4561-4294-8382-559e8820836f)

 <br> 


---------------------------------------

어노테이션을 이용한 권한 체크
-------------

이번엔 특정 페이지들은 로그인 여부에 관계없이 볼 수 있지만 특정 페이지의 로그인한 사용자만 접근하는 방법을 알아볼려고 한다.<br>
이처럼 특정 경로에 접근할 수 있는 권한을 설정하는 작업은 코드로 설정할 수 있고, 어노테이션을 이용해서 지정할 수 있는데 이번엔 어노테이션을 이용하는 방식을 살펴보자. <br>

@EnableMethodSecurity
-------------

어노테이션으로 권한을 설정하려면 ```@Configuration```를 추가한 ```CustomSecurityConfig```클래스에 ```@EnableMethodSecurity```어노테이션을 추가한다.<br>

- CustomSecurityConfig 클래스

```java
@Log4j2
@RequiredArgsConstructor
@Configuration
@EnableMethodSecurity
public class CustomSecurityConfig{

    ...

}
```

```@EnableMethodSecurity```옆에 ```proPostEnabled```속성을 추가할 수 있는데 ```proPostEnabled = true```가 기본 설정이기에 생략할 수 있다.<br>

예시로 등록 게시물로 접근하고자 할 때 컨트롤러에서 권한을 체크할 수 있도록 수정한다.<br>
```registerGet()```에는 ```@PreAuthorize```어노테이션을 적용을 하고 ```@PreAuthorize```안에는 표현식을 이용해서 특정한 권한 가진 사용자만이 접근 가능하도록 지정한다.<br>

- 등록 게시물 컨트롤러

```java
@Controller
@Log4j2
@RequiredArgsConstructor
@RequestMapping("/board")
public class BoardController {

    @GetMapping("/register")
    @PreAuthorize("hasRole('USER')")
    public void registerGet(){
    }
 }   
```

이렇게 프로젝트를 실행해서 등록 게시물을 호출하면 ```@PreAuthorize```에 막혀서 로그인 페이지로 이동하게 될 것이고, 로그인을 하면 어디에서부터 로그인 페이지로 이동했는지를 저장했기 때문에 로그인 후에는 막혔던 해당 경로로 자동 이동된다.<br>

 'user1 / 1111' 으로 다시 로그인을 시도하면 ```CustomUserDetailsService```에서 설정한 ```ROLE_USER```라는 **인가(Authority)**를 가지도록 코드를 작성했기에 ```@PreAuthorize```에서 ```hasRole('USER')```라는 표현식 값은 true가 된다.<br>

 - @PreAuthorize 표현식

 |표현식(메소드)  | 설명 |
|---|:---:|
| `authenticated()` | 인증된 사용자들만 허용 | 
| `permitAll()` | 모두 허용 | 
| `anoymous()` | 익명의 사용자 허용 | 
| `hasRole(표현식)` | 특정한 권한이 있는 사용자 허용 | 
| `hasAnyRole(표현식)` | 여러 권한 중 하나만 존재해도 허용 | 

---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/


