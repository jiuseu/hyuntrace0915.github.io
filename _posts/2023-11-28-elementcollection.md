---
layout: post
title:  "@ElementCollection" 
date:    2023-11-28 22:24:41 +09:00
categories: jekyll update
---

회원 데이터는 가능하면 여러 개의 권한을 가지도록 구성하는 것이 좋기 때문에 보통 ```@OneToMany```,```@ManyToMany``` 를 사용하여 여러 Entity 와 연관관계를 맺을 수 있으며, 연관관계를 맺을 때, 보통 ID 를 사용할 수 있을 것이라는 시뮬레이션을 할 것이다.<br>

허나 얼마 지나지 않아 이러한 엔티티들의 테이블 구조가 복잡해질 것이라는 막막함도 동시에 떠오를 수 있을 것이다.<br>
그래서 일단 하나의 엔티티 객체에 여러 값을 표현할 수 있는 ```@ElementCollection```을 이용하여 회원관리 구성을 해보도록 하겠다.<br>

---------------------------------------

- MemberRole.java 

```java
public enum MemberRole {

    USER, ADMIN;
}
```

각 회원은 'USER' 혹은 'ADMIN' 등 회원 권한을 가질 수 있도록 **Member** 엔티티 클래스를 생성하고 ```@ElementCollection```으로 처리해보도록 하겠다.

<br>


- Member 엔티티 클래스

```java
@Entity
@Builder
@Getter
@AllArgsConstructor
@NoArgsConstructor
@ToString(exclude = "roleSet")
public class Member extends BaseEntity{

    @Id
    private String mid;

    private String mpw;
    private String email;
    private boolean del;

    private boolean social;

    @ElementCollection(fetch = FetchType.LAZY)
    @Builder.Default
    private Set<MemberRole> roleSet = new HashSet<>();

    public void changePassword(String mpw){
        this.mpw = mpw;
    }

    public void changeEmail(String email){
        this.email = email;
    }

    public void changeDel(boolean del){
        this.del = del;
    }

    public void addRole(MemberRole memberRole){
        this.roleSet.add(memberRole);
    }

    public void clearRoles(){
        this.roleSet.clear();
    }

    public void changeSocial(boolean social){
        this.social = social;
    }
}
```

다음으로 **Memberrepository** 인터페이스를 하나 생성해서 제대로 로그인이 진행되는지 테스트를 해보자.<br>
```MemberRole```을 같이 로딩할 수 있도록 ```@EntityGraph```를 추가하고 일반 회원들만 가져오도록 ```social```속성값이 ```false```인 사용자들만 대상으로 처리하였다.

<br>

- MemberRepository 인터페이스

```java
public interface MemberRepository extends JpaRepository<Member,String> {

    @EntityGraph(attributePaths = "roleSet")
    @Query("select m from Member m where m.mid = :mid and m.social = false")
    Optional<Member> getWithRoles(@Param("mid") String mid);
}
```

- Test 케이스

```java
@SpringBootTest
@Log4j2
public class MemberRepositoryTest {

    @Autowired
    private MemberRepository memberRepository;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Test
    public void insertMembers(){

        IntStream.rangeClosed(1,100).forEach(i ->{
            Member member = Member.builder()
                    .mid("member"+i)
                    .mpw(passwordEncoder.encode("1111"))
                    .email("email"+i+"@aaa.bbb")
                    .build();

            member.addRole(MemberRole.USER);
            if(i >= 90){
                member.addRole(MemberRole.ADMIN);
            }
            memberRepository.save(member);
        });
    }

}
```

<br>

테스트 케이스를 실행하고 나면 ```member``` 테이블과 ```member_role_sets``` 테이블이 생성되었고, ```member``` 테이블에는 100개의 사용자 데이터가 생성되었다.<br>
![20231128-member-table](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/d9290c7f-3bca-4155-b6d8-98a031246df8)

동시에 ```member_role_sets``` 테이블에는 member90이상인 계정들은 0과 1이라는 값을 가지게 된다.<br>

![20231128-memberrolesets](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/1b2fb0a1-0aef-48f6-a122-c873ea1ff2ca)





---------------------------------------
[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/