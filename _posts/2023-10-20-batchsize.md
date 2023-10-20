---
layout: post
title:  "@BatchSize" 
date:    2023-10-20 23:01:23 +09:00
categories: jekyll update
---

N+1 문제
-------------

연관 관계에서 발생하는 문제로 연관 관계를 설정한 엔티티를 조회할 경우에 조회된 데이터 갯수(N) 만큼 조회 쿼리가 추가로 발생하여 데이터를 읽어오게 된다. 이를 **N+1** 문제라고 한다. <br>
당연 **N+1**로 실행되는 퀴리는 데이터베이스를 엄청나게 많이 사용하기 때문에 문제가 된다.<br>

---------------------------------------


@BatchSize
-------------

이 **N+1** 문제에 대한 가장 간단한 보완책은 **@BatchSize**이용하는 것이며, **@BatchSize**에는 **size**라는 속성을 지정하는 데 이를 이용해서 '**N**번'에 해당하는 쿼리를 모아서 한 번에 실행할 수 있다.<br>
Path를 통해, 파일, 디렉토리 옮기기, 복사, 삭제, 읽기, 쓰기 등의 작업을 수행할 수 있는데, 자주 쓰는 코드는 Path 구현 객체는 java.nio.file.Paths 클래스의 get() 정적 메소드를 호출하여 얻는다. <br>

```java
@OneToMany(mappedBy = "board",
               cascade = {CascadeType.ALL},
               fetch = FetchType.LAZY,
               orphanRemoval = true)
    @Builder.Default
    @BatchSize(size = 20)
    private Set<BoardImage> imageSet = new HashSet<>();
```

**@BatchSize**의 **size**속성 값은 지정된 수만큼 엔티티를 조회할 때 한 번에 in조건으로 사용된다. <br>
in조건은 조건의 범위를 지정하는 데 사용되며, 지정된 값 중에서 하나 이상과 일치하면 조건에 맞는 것으로 처리한다.<br>

**@BatchSize**가 많은 엔티티를 최적화를 위해서 당연 쿼리 조회 수를 최소한으로 줄어야 하기 때문에캐싱케이스를 줄여야 하는데 임의로 설정한 **size**를 기준으로 절반식 나눠가면서 쿼리 조회를 한다.<br>

즉, in절 항목값을 100으로 잡은 경우 '1,2,3,4,5,6,7,8,9,10,12,25,50,100' 이렇게 범위를 줄여나가는데 예를 들어 실제 데이터수가 20개라면 100-> 50 -> 25 -> 12 이렇게 범위를 좁혀서 12개의 데이터를 한번에 묶어서 조회를 하고 나머지 8개의 데이터는 당연 8개의 범위로 조회를 하게 된다.<br>  

그러니 **@BatchSize**의 범위 설정은 자신이 진행하고 있는 프로젝트의 데이터베이스 데이터가 어느 정도의 규모에 따라서 **size**를 설정해야 할 것이다.<br>

---------------------------------------






[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
