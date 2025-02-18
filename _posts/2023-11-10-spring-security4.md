---
layout: post
title:  "스프링 시큐리티 4편 -자동 로그인" 
date:    2023-11-10 22:30:35 +09:00
categories: jekyll update
---

remember-me 
=============

스프링 시큐리티의 'remember-me(로그인 기억하기 혹은 자동로그인)'기능은 쿠키를 이용해서 브라우저에 로그인 했던 정보를 유지하기 때문에 매번 로그인을 실행할 필요가 없어진다.<br>
이러한 기능 때문에 PC, 모바일 등 여러 환경에서 자동로그인 기능을 많이 사용하고 있을 것이다.<br>

자동로그인의 쿠키값을 생성할 때 필요한 정보를 보관하기 위해 여러 방법이 있지만 지금 사용하고 있는 데이터베이스를 이용을 해 볼 것이다.<br>

- 자동 로그인의 정보를 위한 테이블 생성

```
create table persistent_logins(
     username varchar(64) not null,
     series varchar(64) primary key,
     token varchar(64) not null,
     last_used timestamp not null
); 
```

이때 쿠키와 관련된 정보를 테이블로 보관하도록 지정하는데 ```DataSource```가 필요하고 ```UserDetailsService```타입의 객체가 필요하니 코드를 수정해보자.<br>

- CustomSecurityConfig 클래스

```java
@Configuration
@Log4j2
@RequiredArgsConstructor
@EnableMethodSecurity
public class CustomSecurityConfig {

    //주입 필요 
    private final DataSource dataSource;
    private final CustomUserDetailsService userDetailsService;

    ....

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http)throws Exception{

        //커스텀 로그인 페이지
        http.formLogin(form ->{
            form.loginPage("/member/login");
        });
        //CSRF 토큰 비활성화
        http.csrf(httpSecurityCsrfConfigurer -> httpSecurityCsrfConfigurer.disable());

        http.rememberMe(httpSecurityRememberMeConfigurer -> {

            httpSecurityRememberMeConfigurer.key("12345678")
                    .tokenRepository(persistentTokenRepository())
                    .userDetailsService(userDetailsService)
                    .tokenValiditySeconds(60*60*24*30);

        });
        
        return http.build();
    }

    @Bean
    public PersistentTokenRepository persistentTokenRepository(){
        JdbcTokenRepositoryImpl repo = new JdbcTokenRepositoryImpl();
        repo.setDataSource(dataSource);

        return repo;
    }
```

쿠키를 생성할 때 쿠키의 값을 인코딩하기 위해 키값과 필요한 정보를 정하는 ```tokenRepository```를 지정한다.<br>
그래서 위에 코드에서 ```persistentTokenRepository()```라는 메소드를 이용해 이를 처리하였다.<br>

---------------------------------------

자동로그인을 위한 화면 설정
-------------

자동로그인을 활성화 하기 위해서 자동로그인의 값이 같이 전달되어야 하기에 로그인 페이지에 체크박스 인풋을 하나 추가한다.<br>

- 로그인 html

```html
<div class="input-group mb-3">
    <span class="input-group-text">아이디</span>
      <input type="text" name="username" class="form-control" placeholder="USER ID">
</div>
<div class="input-group mb-3">
    <span class="input-group-text">비밀번호</span>
       <input type="text" name="password" class="form-control" placeholder="PASSWORD">
</div>
<div class="input-group mb-3">
    <input class="form-check-input" type="checkbox" name="remember-me">
       <label class="form-check-label">자동로그인</label>
</div>
```

이렇게 설정하고 로그인 페이지를 호출해서 로그인하면 'remember-me'라는 이름의 쿠키가 생성된 것을 확인할 수 있다.<br>
쿠키가 정상적으로 생성되었다면 브라우저를 종료한 후에 다시 권한이 필요한 페이지에 접근하면 자동으로 로그인된 것을 확인할 수 있다.<br>

![20231110-loginpage](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/fd78ab97-e622-4d36-b573-23c596311322)

![20231110-f12applicationcokie](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/edc814a8-5c07-4220-99c8-ff2aaeaae6ec)

<br>

데이터베이스에 'persistent_logins'를 조회하면 다음과 같이 user1 계정의 쿠키가 유지되는 모습 또한 확인할 수 있다.<br>

![20231110-db-cookie](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/2eb46add-3253-4f04-afb7-c0e3c825aa47) 

<br>

로그아웃의 경우 기본 설정은 'remember-me'쿠키의 삭제이다.<br> 
주소창에 로그아웃 호출하는 것도 정상적으로 처리가 되며 그와 동시에 데이터베이스에 'persistent_logins' 테이블에 쿠키 정보가 삭제되는 것이 확인된다.<br>

![20231110-db-cookie-logout](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/2d2a3eae-e6e6-493b-b487-31098d4c7054)




---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
