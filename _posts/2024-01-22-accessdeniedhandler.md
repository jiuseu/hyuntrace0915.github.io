---
layout: post
title:  "AccessDeniedHandler" 
date:    2024-01-22 17:06:11 +09:00
categories: jekyll update
---

### AccessDeniedHandler

- BoardController

```java

@GetMapping({"/read","/modify"})
    @PreAuthorize("isAuthenticated()")
    public void read(Long bno,Model model,PageRequestDTO pageRequestDTO){

        log.info("read/modify Get....");
        BoardDTO boardDTO = boardService.readOne(bno);
        model.addAttribute("dto",boardDTO);
    }

```


프로젝트 내의 컨트롤러에 ```@PreAuthorize("isAuthenticated()")```를 설정해 사용자 로그인이 안 되었다면 302 메세지와 함께 로그인 경로로 이동하나, 403에러는 앞의 그림과 같이 에러가 발생한다.<br>
하지만 403 에러는 다양한 원인과 경우가 많기 때문에 이것을 수정하기 위해 보다 많은 힘을 들어야 할 수도 있다.<br>
그래서 기본으로 보여주는 403에러 페이지 대신에 ```AccessDeniedHandler``` 인터페이스를 구현해서 상황에 맞게 처리하는 방법이 있다.<br>

- ```<form>``` 태그의 요청이 403 에러인 경우 로그인 페이지로 이동할 때 ```ACCESS_DENIED```값을 파라미터로 전달<br>
- Ajax인 경우에는 JSON 데이터를 만들어서 전송<br>    

이번 경우는 ```<form>```태그를 사용했기에 첫번째 방법으로 처리하도록 하겠다.<br>

- Custom403Handler
```java
@Log4j2
public class Custom403Handler implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response,
                       AccessDeniedException accessDeniedException)throws IOException, ServletException{

        log.info("-----------------------ACCESS DENIED------------------------");

        response.setStatus(HttpStatus.FORBIDDEN.value());

        String contentType = request.getHeader("Content-Type");
        boolean jsonRequest = contentType.startsWith("application/json");

        if(!jsonRequest){
            response.sendRedirect("/member/login?error=ACCESS_DENIED");
        }
    }
}
```

```Custom403Handler```클래스는 ```AccessHandler```인터페이스를 구현해서 403에러를 처리하기 위해서 생성하였다.<br>
앞에서 말한 것처럼 사용자가 권한이 없는 경우 등 위의 코드처럼 로그인 페이지로 리다이렉트하게 처리된다.<br>
```Custom403Handler```가 동작하기 위해서 스프링 시큐리티의 설정을 담당하는 ```CustomSecurityConfig```에 ```@Bean```처리와 예외 처리를 지정해 주어야 한다.<br>

- CustomSecurityConfig

```java
@Configuration
@Log4j2
@RequiredArgsConstructor
@EnableMethodSecurity
public class CustomSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http)throws Exception{

       http.formLogin(form -> form.loginPage("/member/login"));
       
       ...

       http.exceptionHandling(form -> form.accessDeniedHandler(accessDeniedHandler()));

        return http.build();
    }

    ...

    @Bean
    public AccessDeniedHandler accessDeniedHandler(){
        return new Custom403Handler();
    }

}    
```    

---------------------------------------

### 이 글을 쓴 경위

```AccessDeniedHandler ```를 왜 쓰는가에 대해서 여태 이야기 했지만 이 글을 쓴 진짜 이유는 오늘 회원가입 기능을 구현하여 회원가입을 하고 로그인해서 등록 게시물을 접속하였는데 아래와 같은 에러 페이지가 떴다.<br>

![1_22_500errorpage](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/d5362eec-1957-4c22-bb77-c697f8843bc5)

에러가 일어난 출력된 로그 중에 먼저 내가 만든 ```Custom403Handler```으로 연결되어 처리되다가 도중에 ```contentType``` 변수가 ```null```값이라 최종적으로 500에러 페이지가 출력이 된 것 같았다.<br>

이렇게 된 원인은 게시물 등록 페이지는 권한을 가진 사용자만 접속할 수 있었고, 회원가입 클래스에 회원가입을 한 사용자에게 권한을 주는 코드를 안 넣어주었기 때문에 정상적으로 수정한 결과 정상적으로 접속할 수 있었다.<br>

여기서 의문은 ```Custom403Handler```에서 처리되다가 중간에 왜 ```contentType``` 변수가 ```null```값이 처리되었는지에 대해서 정답을 찾지 못했다.<br>
내 생각에는 권한이 없는 사용자를 처리하기 위한 과정이 도중에 끝냈기에 결국 사용자의 데이터가 없어지면서 ```contentType``` 변수가 ```null```값이 되었다 라는 결론으로 일단 추측하였다.<br>

결국 에러라는 결과를 고쳤지만 원인을 정확히 파악하지 못했다는 점에서 더욱 열심히 해야할 것이다.<br>


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/