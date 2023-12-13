---
layout: post
title:  "extends, implements" 
date:    2023-12-13 22:42:53 +09:00
categories: jekyll update
---

### 상속

문득 코드를 작성하다가 어떤 클래스에는 ```extends```, ```implements ```를 사용해서 상속받았는데 이 둘의 차이점을 명확하게 구분하지 못해 차이점을 정리하고자 한다.<br> 

상속이라는 것은 하위 객체가 상위 객체(부모)의 특징을 물려받는 것을 의미한다.<br>
예를 들어 A(부모)라는 객체의 메소드인 ```move()```가 존재하고 있고 B,C,D라는 객체가 A를 상속 받고 있다면 이 객체들 모두 ```move()```메소드를 사용할 수 있다.<br>
그리고 이 예는 메소드 뿐만 아니라 변수에도 해당되기에 물려받게 된다.<br>

여기서 ```extends```, ```implements ```의 차이점이 드러나는데, 이 메소드 OR 변수를 **구현**하는가 그대로 **사용**하는가에 따라서 상속의 형태가 갈리게 된다.<br>

--------------------------------------- 

### extends

부모에서 선언 및 정의를 모두하며 자식은 메소드와 변수를 그대로 사용할 수 있으며 오버라이딩 할 필요 없이 부모에 구현되있는 것을 직접 사용 가능하다.<br>

- BaseEntity

```java
@MappedSuperclass
@EntityListeners(value = AuditingEntityListener.class)
@Getter
public class BaseEntity {

    @CreatedDate
    @Column(name = "regdate", updatable = false)
    private LocalDateTime regDate;

    @LastModifiedDate
    @Column(name = "moddate")
    private LocalDateTime modDate;

 
}
```

- Reply

```java
@Entity
@Builder
@Getter
@AllArgsConstructor
@NoArgsConstructor
@ToString(exclude = "board")
public class Reply extends BaseEntity{

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long rno;

    private String replyText;

    private String replyer;

    @ManyToOne(fetch = FetchType.LAZY)
    private Board board;

    public void ChangeReplyText(String replyText){
        this.replyText = replyText;
    }
}
```

<br>

예시로 ```Reply```라는 클래스는 ```BaseEntity```을 상속 받았다. 그리고 extends를 했으니 메소드 뿐만 아니라 변수까지 사용이 가능해 지는 것이다.<br>
그런데 다중상속이란 부모클래스가 두개 이상 존재할 수 있다는 것인데, 자바에서는 이를 지원하지 않는다.<br> 
즉 , ```public class 자식 extends 부모1, 부모2``` 이것이 지원하되지 않는다는 것이다.<br>

그래서 대신 implements(인터페이스)가 등장했다.<br>

---------------------------------------

### implements 


implements의 가장 큰 특징은 부모에서는 선언만 할 수 있으며, 메소드를 반드시 자식에서 오버라이딩(재정의)해야 하며 implements한 클래스는 implements의 내용을 다 사용해야 한다.<br>
또한 이 implements는 다중상속을 대신해준다. <br>

- interface BoardService

```java
public interface BoardService {

    Long register(BoardDTO boardDTO);

    BoardDTO readOne(Long bno);

    void modify(BoardDTO boardDTO);

    void remove(Long bno);
}
```

- class BoardServiceImpl

```java
@Service
@RequiredArgsConstructor
@Log4j2
@Transactional
public class BoardServiceImpl implements BoardService{

    private final BoardRepository boardRepository;
    private final ModelMapper modelMapper;

    @Override
    public Long register(BoardDTO boardDTO){

        Board board = dtoToEntity(boardDTO);
        Long bno = boardRepository.save(board).getBno();

        return bno;
    }

    @Override
    public BoardDTO readOne(Long bno){

        Optional<Board> result = boardRepository.findById(bno);
        Board board = result.orElseThrow();
        BoardDTO boardDTO = entityToDTO(board);

        return boardDTO;
    }

    @Override
    public void modify(BoardDTO boardDTO){

        Optional<Board> result = boardRepository.findById(boardDTO.getBno());
        Board board = result.orElseThrow();
        board.Change(boardDTO.getTitle(), boardDTO.getContent());

       
        boardRepository.save(board);
    }

    @Override
    public void remove(Long bno){

        try{
            Optional<Board> result = boardRepository.findById(bno);
            Board board = result.orElseThrow();
            boardRepository.delete(board);
        }catch (NoSuchElementException e){
            log.info("이미 삭제 되었거나 없는 데이터입니다.");
        }

    }

  
   
}
```


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/