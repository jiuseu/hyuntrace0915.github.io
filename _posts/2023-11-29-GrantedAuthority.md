---
layout: post
title:  "GrantedAuthority" 
date:    2023-11-29 22:24:41 +09:00
categories: jekyll update
---

**권한(Authority)**들은 어떻게 저장될까? <br>
스프링 시큐리티에서 권한들과 역할은 기본적으로 **GrantedAuthority**에 저장된다. **GrantedAuthority**는 권한이나 역할의 이름을 반환하는 메소드를 제공한다.<br> 

- GrantedAuthority

```java
public interface GrantedAuthority extends Serializable {

	String getAuthority();

}
```

즉 **GrantedAuthority**는 현재 사용자가 가지고 있는 권한이다. <br>
ROLE_ADMIN, ROLE_USER 와 같은 ROLE_* 형식을 사용하며, **UserDetailsService** 구현 객체를 통해 **UserDetails**에 접근하여 불러와 접근 권한이 있는지 확인하고 허용 여부를 결정한다. <br>

- SimpleGrantedAuthority

```java
public final class SimpleGrantedAuthority implements GrantedAuthority {

	private static final long serialVersionUID = SpringSecurityCoreVersion.SERIAL_VERSION_UID;

	private final String role;

	public SimpleGrantedAuthority(String role) {
		Assert.hasText(role, "A granted authority textual representation is required");
		this.role = role;
	}

	@Override
	public String getAuthority() {
		return this.role;
	}
    
    ...
}
```

**SimpleGrantedAuthority** 클래스는 **GrantedAuthority** 인터페이스의 기본으로 구현되는 객체이다.<br>
권한을 저장하기 위해서는 저장하고자 하는 권한 문자열 값을 **SimpleGrantedAuthority**의 생성자 파라미터에 넣어주면 이것으로 권한 객체 생성은 끝이 난다.<br>

---------------------------------------

- 스프링 시큐리티를 담당하는 DTO 클래스 

```java
@Getter
@Setter
@ToString
public class MemberSecurityDTO extends User {

    private String mid;
    private String mpw;
    private String email;
    private boolean del;
    private boolean social;

    public MemberSecurityDTO(String username, String password, String email, boolean del, boolean social,
                             Collection<? extends GrantedAuthority> authorities){

        super(username, password, authorities);

        this.mid = username;
        this.mpw = password;
        this.email = email;
        this.del = del;
        this.social = social;
    }
}
```

- CustomUserDetailsService 클래스 

```java
@Service
@Log4j2
@RequiredArgsConstructor
public class CustomUserDetailsService implements UserDetailsService {

    private final MemberRepository memberRepository;

    @Override
    public UserDetails loadUserByUsername(String username)throws UsernameNotFoundException {

        log.info("loadUserByUsername : "+ username);

        Optional<Member> result = memberRepository.getWithRoles(username);

        if(result.isEmpty()){ //해당 아이디를 가진 사용자가 없다면
            throw new UsernameNotFoundException("username not found....");
        }

        Member member = result.get();

        MemberSecurityDTO memberSecurityDTO = new MemberSecurityDTO(
                member.getMid(),
                member.getMpw(),
                member.getEmail(),
                member.isDel(),
                false,
                member.getRoleSet().stream().map(memberRole ->
                        new SimpleGrantedAuthority("ROLE_"+memberRole.name())).collect(Collectors.toList())
        );

        log.info("memberSecurityDTO");
        log.info(memberSecurityDTO);

        return memberSecurityDTO;
    }
}
```

위의 **GrantedAuthority**를 활용해서 저번에 작성한 회원 관리에 관련된 클래스들과 실제 로그인 처리를 담당하는 ```CustomUserDetailsService```를 수정한다면 데이터베이스에 존재하는 아이디와 패스워드를 이용해서 실제 로그인 처리가 가능하다.<br> 

---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/