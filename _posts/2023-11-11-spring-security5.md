---
layout: post
title:  "스프링 시큐리티 5편 - 화면에서 인증 처리하기" 
date:    2023-11-14 22:38:26 +09:00
categories: jekyll update
---

Thymeleaf 인증 정보 활용
=============

**Thymeleaf**에서 인증 정보를 처리하기 위해서는 **Thymeleaf**에서 스프링 시큐리티를 활용하려고 하면 ```bundle.gradle```에 ```Thymeleaf Extras Springsecurity6``` 라이브러리를 추가한다.<br>

```

dependencies {
	...

	implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6'

}

```

참고로 ```Thymeleaf Extras Springsecurity```에서 6버전을 사용한 이유는 스프링 부트 버전 3.0이상, 자바 17 버전을 사용하기 있었기 때문에 호환되는 것은 6버전 밖에 되지 않았고 다른 버전을 사용할 때에는 아예 웹페이지에서 기능을 하지 못하였다.<br>
그러니 만약 자신이 프로젝트를 만드는데 스프링 부트나 자바 버전 환경이 달라졌다면 라이브러리 버전도 신경을 써야 할 것이다.<br>

라이브러리를 추가한 후에 사용자의 정보를 보고 싶은 웹페이지 html 스크립트에 다음과 같이 코드를 추가한다.<br>

```html
<script layout:fragment="javascript" th:inline="javascript">


  const auth = [[${#authentication.principal}]]


</script>
```

프로젝트를 실행하고 로그인된 상황에서 코드를 추가한 웹페이지를 호출하면 다음과 같이 사용자 인증 정보가 생성된 것을 확인할 수 있다.<br> 

![20231114-authenticaion-script](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/0ff9cba7-3d3b-43d2-9f2c-c18b0ad8bb37)

<br>

그리고 웹페이지 내에서 사용자 인증 정보(아이디 등)를 활용하기 위해서 html 코드 위에 추가된 ```Thymeleaf Extras Springsecurity```의 네임스페이스를 추가한다.<br>

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity6"
      xmlns:layout="http://ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{/layout/basic.html}">
<head>
```
<br>

추가한 후 현재 사용자 아이디(스프링 시큐리티에서는 username)를 출력하고 읽기 전용으로 처리해보자.<br>

```html
<div class="input-group mb-3">
             <span class="input-group-text">User</span>
             <input type="text" class="form-control" name="user" placeholder="User"
              th:value="${#authentication.principal.username}" readonly>
           </div>
```
<br>

웹페이지에서 작성자(user)부분에 어떻게 처리되는지 확인한다.<br>

![20231114-authenticaion-register-username](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/12235ad7-4f84-4a4b-a4a6-63be6b09ed1d)



---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

