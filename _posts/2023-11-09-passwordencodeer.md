---
layout: post
title:  "스프링 시큐리티 3편 - PasswordEncoder" 
date:    2023-11-09 22:35:11 +09:00
categories: jekyll update
---

인증과 인가
=============

- PasswordEncoder

 스프링 시큐리티에서 비밀번호를 안전하게 저장할 수 있도록 비밀번호의 단방향 암호화를 지원하는 ```PasswordEncoder``` 인터페이스와 구현체들을 제공하고 있다.<br>
 ```PasswordEncoder```타입의 클래스 중에서 무난하게 많이 쓰이고 있는 것이 ```BCryptPasswordEncoder```라는 클래스이다.<br>

 ```BCryptPasswordEncoder```는 해시 알고리즘으로 암호화 처리되는데 같은 문자열이라고 해도 매번 해시 처리된 결과가 다르므로 패스워드 암호화에 많이 사용되고 있다.<br>


---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/


