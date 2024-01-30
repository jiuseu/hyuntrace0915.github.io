---
layout: post
title:  "@Modify" 
date:    2024-01-30 23:44:27 +09:00
categories: jekyll update
---

### @Modifying


```@Modifying```은 **Spring Data JPA**에서 사용되는 어노테이션 중 하나이다. <br>
 **Spring Data JPA**에서 기본적으로 제공하는 메서드들은 데이터를 조회하는데 사용되지만 데이터를 수정하거나 삭제하는 경우에는 **@Modifying** 어노테이션을 사용하여 명시적으로 변경 쿼리를 실행할 수 있다.<br>

보통 ```@Query``` 어노테이션과 함께 사용되며, ```@Query``` 어노테이션으로 **JPQL** 또는 네이티브 쿼리를 작성하고, ```@Modifying``` 어노테이션을 함께 사용하여 해당 쿼리가 수정 쿼리임을 명시한다.<br>

- 예시 코드

```java

public interface UserRepository extends CrudRepository<User, Long> {

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.active = true WHERE u.lastLoginDate < :lastLoginDate")
    void activateUsersWithLastLoginBefore(String lastLoginDate);
}

```

```@Modifying``` 어노테이션만 사용하는 경우에는 **Spring Data JPA**에서 트랜잭션 관리를 하지 않기에 이 메서드가 실행되는 동안 트랜잭션을 시작하거나 커밋/롤백하지 않는다<br>

메서드 수행 도중 예외가 발생하면 데이터베이스는 일관성이 없는 상태가 될 수 있어 트랜잭션를 명시적으로 설정하지 않으면 메서드 호출이 끝날 때까지 트랜잭션이 시작되지 않는다.<br> 
이는 데이터의 일관성을 위해 트랜잭션 내에서 실행되어야 하는 수정 작업에 대해 예상치 못한 결과를 초래할 수 있다.<br>

```@Transactional```이 없는 경우에는 예외가 발생하더라도 롤백이 자동으로 처리되지 않는다.<br>
이는 메서드 수행 중에 예외가 발생하면 수정 쿼리에 대한 롤백이 보장되지 않을 수 있음을 의미한다.<br>


이러한 문제들 때문에 ```@Modifying```와 ```@Transactional```는 같이 사용하는 것이 안정적일 것이다.<br>

---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/