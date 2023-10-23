---
layout: post
title:  "@Transactional" 
date:    2023-10-23 22:49:05 +09:00
categories: jekyll update
---

No Session
-------------

오늘 이미지 추가를 위한 코드를 작성하다가 게시글 수정 기능에 이미지 수정 기능을 추가하였는데 새로운 에러가 발생하였다. <br>

![10-20proxy-no-session-error](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/c2c1ed3d-6ca7-4cf8-8e56-b9da1a64f51a)


일단 에러를 보면 imageSet 프록시 객체를 준비(?) 할 수 없는 걸로 보이며, **Session**는 영속성 컨텍스트를 의미 할테니 아마 연관관계를 맺고 있는 다른 엔티티 객체를 로드할려고 하였지만 **영속성 컨텍스트(session)**가 없어 이러한 에러가 나타난 것이라 추측된다.<br>

* proxy - 대리인 역할. 즉 JPA에서의 프록시는 진짜 엔티티가 아닌 임시(가짜) 엔티티를 의미.<br>


---------------------------------------

@Transactional
-------------

이 문제는 간단하게 해결할 수 있는데 바로 ```@Transactional``` 어노테이션을 서비스 부분에 추가하니 바로 해결되었다.<br>
하지만 해결되었다고 끝내면 얻어가는 것은 별로 없으니 **왜** 해결되었는지 한번 찾아보기로 하였다.<br>

```java
Hibernate: 
    select
        b1_0.bno,
        .....
    from
        board b1_0 
    where
        b1_0.bno=?
Hibernate: 
    select
        i1_0.board_bno,
        ......
    from
        board_image i1_0 
    where
        i1_0.board_bno=?
Hibernate: 
    select
        b1_0.uuid,
        ......
    from
        board_image b1_0 
    left join
        board b2_0 
            on b2_0.bno=b1_0.board_bno 
    where
        b1_0.uuid=?
Hibernate: 
    insert 
    into
        board_image
        (board_bno,file_name,ord,uuid) 
    values
        (?,?,?,?)

```

---------------------------------------

# WHY?

**JPA**는 스프링에서 기본으로 제공하는 전략을 따르고 있는데 트랜잭션이 시작하는 순간 영속성 컨텍스트가 생성되고, 트랜잭션이 끝나는 순간 영속성 컨텍스트가 종료가 된다.<br> 
위의 에러는 한번 엔티티 객체를 select하고 출력을 끝난 뒤 다시 select문을 실행할려고 하지만 이미 데이터베이스와 연결은 끝난 상태이니 **준영속** 상태로 전이되어 'no session'이라는 에러가 나타난 듯하다.<br>

즉 **준영속** 상태라는 것은 엔티티가 관리 범위 밖으로 나갔으니, ```@Transactional``` 어노테이션이 없을 경우 ```@OneToMany```, ```@ManyToMany``` 등 ```Lazy loding(지연로딩)```을 Default로 사용하는 엔티티들을 정상적으로 조회할 수 없다.<br>
먼저 JPA 구현체들은 프록시 패턴을 통해서 객체를 조회할 때 연관된 객체를 바로 조회하지 않고 실제로 사용할 때만 조회를 한다.<br>

프록시를 사용해서 조회할 경우에는 해당 객체에 접근 할 때 조회 하겠다고 요청을 하는데 이 부분이 **Lazy loading(지연로딩)** 이며, ```@Transactional```이 붙어있지 않을 경우, 준영속 상태에 있는 엔티티들은 지연로딩을 할 수 없다.<br> 

그래서 ```@Transactional``` 어노테이션으로 해당 메소드를 실행할 때 영속성 컨텍스트는 트랜잭션의 끝까지 유지가 되므로 이 에러문제는 해결이 된 것이다.<br>
따라서 ```@Transactional```이 있어야 **지연로딩(Lazy loading)**이 필요한 엔티티들을 정상 조회 할 수 있다.<br>


---------------------------------------






[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
