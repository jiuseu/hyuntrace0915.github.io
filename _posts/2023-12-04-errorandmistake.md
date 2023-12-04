---
layout: post
title:  "자잘한 에러 및 실수들" 
date:    2023-12-04 22:24:41 +09:00
categories: jekyll update
---

### An error happened during template parsing... 

![20231204-error](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/8a77cb6a-6125-4fba-b4d8-e520b76773ec)

어느 날 만들고 있던 프로젝트의 하나의 게시물에 들어갔는데 또다시 댓글 목록이 출력이 되지 않아 이번엔 무엇 때문인지 로그를 살펴본 결과 ```An error happened during template parsing (template: "class path resource [templates/~~]```이라는 코드를 출력하고 있었다.<br>

원인은 단순한 것이었는데 게시물의 html에 들어가는 자바스크립트 코드 중에서 변수가 오탈자가 났기 때문에 댓글 목록이 출력이 되지 않았던 것이었다.<br>


- 원인이었던 코드 

```javascript
<script layout:fragment="javascript" th:inline="javascript">
  const bno = [[${dto.bno}]]
  const currentAuth = [[${authentication.principal.username}]] //authentication 앞에 #를 누락해 에러가 발생함
  let auth = false

  const replyList = document.querySelector('.replyList') //댓글 목록
  const replyPaging = document.querySelector('.replyPaging')//페이지 목록

  ....

```

---------------------------------------

### Enum 상수들의 순서 

다음 사례는 로그인을 진행하면 페이지가 기본페이지로 접속하게 되고, 다시 접속하고 싶은 게시물에 접속을 시도하면 다시 로그인을 해야 하는 무한반복이 시작되었다.<br>
그래서 왜 이러한 무한반복이 발생되는지 의문이라 DTO의 데이터가 이상한가 싶어 TEST케이스를 생성하여 살펴본 결과 ```Enum```의 권한이 이상한 게 출력이 되는 것을 볼 수 있었다.<br>


- 테스트 케이스

```java
@SpringBootTest
@Log4j2
public class MemberRepositoryTests {

    @Autowired
    private MemberRepository memberRepository;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Test
    public void testRead(){

        Optional<Member> result = memberRepository.getWithRoles("member10");
        Member member = result.orElseThrow();

        log.info(member);
        log.info(member.getRoleSet());

        member.getRoleSet().forEach(memberRole -> log.info(memberRole.name()));
    }
}
```

![20231204-testcase-result](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/b7e41b5d-c030-4669-a982-b1902e096b92)

<br>

원래는 권한을 ```USER```, ```ADMIN```이렇게 2개가 있었는데 ```Enum```특성상으로 순서대로 저장하여 0,1,2,3으로 값을 저장하기 때문에 이미 저장된 db에는 'USER=0', 'ADMIN=1'으로 저장되어 있었다.<br>
허나 새롭게 다시 프로젝트를 생성하면서 이번엔 ```ADMIN```,```USER```순으로 저장하였고, 심지어 컨트롤러에는 어노테이션으로 ```USER```으로만 권한을 설정하였기에 ```ADMIN```권한으로 웹페이지가 접속이 되지 않았던 것이었다.<br>
그래서 상수들의 순서를 바꾸고 다시 로그인하여 웹페이지를 접속한 결과 제대로 접속이 되었다.<br>

- MemberRole(enum)

```java
public enum MemberRole {
    ADMIN, USER;
}
```

<br>

두 개의 에러나 실수의 원인을 살펴보면 결국 오탈자나 누락이었는데 기록한 이유는 처음에 이러한 에러가 나타났을 때 원인이 오탈자나 누락인 것을 알 수 없었다. 그렇기에 다시 이 에러들을 만난다면 빠르게 기억할 수 있게 기록을 해둔다.<br>


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/