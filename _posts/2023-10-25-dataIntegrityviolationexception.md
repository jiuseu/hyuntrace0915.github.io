---
layout: post
title:  "DataIntegrityViolationException" 
date:    2023-10-25 22:53:32 +09:00
categories: jekyll update
---

DataIntegrityViolationException
-------------

웹페이지에 게시물을 등록할 때 파일 첨부 기능을 같이 할 수 있도록 게시물 등록을 담당하고 있는 ```html```의 자바스크립트를 수정하고 있던 중 게시물과 첨부파일을 등록하면 ```DataIntegrityViolationException```에러가 나타나면서 등록 기능을 하지 못하였다.<br>


![10_25_log_error_dataIntegrity](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/ba3e2463-1b2a-4f9f-96e7-fea34ae5b034)


로그에서 출력된 에러 메세지는 ```DataIntegrityViolationException```에러가 발생하였으며 그 뒤엔 ```A different object with the same identifier value was already associated with the session```라는 메세지가 이어져 있었다.<br>
 
위의 에러 메세지를 살펴보았을 때 ```/view/s```라는 객체가 이미 세션에 존재해 있는데, 똑같은 객체가 다시 세션에 들어왔기에 이러한 에러가 나타난 듯하다.<br>
즉 이미 존재하는 엔티티 pk와 동일한 pk를 가진 객체가 들어와 나타난 에러이다.<br>

---------------------------------------

- Board 엔티티

```java
@Entity
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@ToString(exclude = "imageSet")
public class Board extends BaseEntity{

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long bno;

    private String title;

    private String content;

    private String user;

    @OneToMany(mappedBy = "board",
               fetch = FetchType.LAZY,
               cascade = CascadeType.ALL,
               orphanRemoval = true)
    @Builder.Default
    @BatchSize(size = 20)
    private Set<BoardImage> imageSet = new HashSet<>();

    public void addImage(String uuid, String fileName){
        BoardImage boardImage = BoardImage.builder()
                .uuid(uuid)
                .fileName(fileName)
                .board(this)
                .ord(imageSet.size())
                .build();
        imageSet.add(boardImage);
    }

    public void clearImage(){
        getImageSet().forEach(boardImage -> boardImage.changeBoard(null));
        this.imageSet.clear();
    }

    public void Change(String title, String content){
        this.title = title;
        this.content = content;
    }

    public void MapperSetBno(Long bno){
        this.bno = bno;
    }
}
```

- BoardImage 엔티티

```java
@Entity
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@ToString(exclude = "board")
public class BoardImage implements Comparable<BoardImage> {

    @Id
    private String uuid;

    private String fileName;
    private int ord;

    @ManyToOne
    private Board board;

    @Override
    public int compareTo(BoardImage other){
        return this.ord - other.ord;
    }

    public void changeBoard(Board board){
        this.board = board;
    }
}
```
---------------------------------------

```Board```와 ```BoardImage```이 둘은 게시물(Board) 등록할 때 첨부파일(BoardImage)가 있으면 같이 등록할 수 있도록 일대다 연관관계를 맺고 있다.<br>
그래서 아무리봐도 서로 PK값을 건드리는 코드는 보이지 않았고, 심지어 에러 현상은 첨부파일 처음 하나만 등록했을 때는 정상적으로 등록이 되었고 다음에 등록할 때는 하나든 다수이든 이 에러현상이 나타났다.<br>

그래서인지 에러 메세지 뒷부분인 ```/view/s```이 부분이 신경쓰게 되었는데, 이 코드는 이미지 썸네일을 웹페이지에 볼 수 있도록 컨트롤러 ```Get```매핑 값이었다.<br>
그래서 정상적으로 들어간 등록 데이터를 데이터베이스에 조회를 해보자 왜 이런 에러가 나타난지 알 수 있었다.<br>

- 데이터베이스

![10_25_database_error_uuid_filename](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/6427f245-9764-4274-9a76-1638007b7aef)

---------------------------------------

해결 과정
-------------

업로드한 파일의 이름을 ```uuid```와 ```fileName```가 합쳐져 새롭게 이름이 생성이 되면서 다시 데이터베이스에는 이 둘을 나누어 각각 다른 데이터에 저장을 할 수 있도록 로직을 구성하였다.<br> 
그런데 웹페이지에 업로드된 파일 이름이 컨트롤러 ```Get```매핑 값까지 합쳐져 ```uuid```에는 컨트롤러 매핑값, ```fileName```에는 ```uuid```가 잘못 들어가게 된 것이다.<br>
당연히 두번째로 파일을 첨부할 때에는 엔티티에서 설정한 pk값인 ```uuid```가 ```/view/s```가 동일하니 엔티티 pk가 동일한 객체가 들어왔다고 위의 에러가 나타난 것이다.<br> 

그래서 ```html```등록 화면에서 업로드되는 파일 태그 안에 따로 ```data```속성을 이용해서 컨트롤러 매핑값을 뺀 ```uuid```와```fileName```를 입력하여 다시 게시물과 첨부파일을 등록한 결과 정상적으로 기능하였다<br>

결국 이 에러 현상은 자신이 로직을 잘못 구성하였기에 발생하였지만, 그래도 ```DataIntegrityViolationException```에러가 어떠한 원인 때문에 발생하는지 알 수 있는 기회였기에 오늘 복기 해보았다.<br>

---------------------------------------






[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
