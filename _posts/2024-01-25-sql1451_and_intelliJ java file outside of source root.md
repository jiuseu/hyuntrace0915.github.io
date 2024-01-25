---
layout: post
title:  "sql error 1451 / intelliJ java file outside of source root" 
date:    2024-01-25 23:05:41 +09:00
categories: jekyll update
---

### sql error 1451

![1_25_sql_1451_error](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/75904ac6-415f-40ac-b2db-e48d34a74264)

코드 작성을 실수로 인해 잘못된 데이터들이 db에 저장이 되서 ```heidisql```에서 제거를 시도하였으나 위와 같은 에러가 출력이 되면서 제거가 되지 않았다.<br>

원인은 데이터들이 외래키와 연결이 되어서 제거와 업데이트를 할 수 없게 된 것이다.<br>
그래도 해결방법은 단순한데 외래키가 문제이니 외래키 설정을 해제하고 데이터를 제거하면 되는 것이다.<br>
SQL문에 ```SET FOREIGN_KEY_CHECKS=0;```으로 잠시 FK설정 풀고 하고싶은 SQL문 실행한 다음에 ```SET FOREIGN_KEY_CHECKS=1;``` 다시 FK설정을 되돌리면 되는 것이다.<br>

위의 방법을 사용하여 정상적으로 데이터들이 제거되었다.<br>

---------------------------------------

### intelliJ java file outside of source root

![1_25_intelliJ java file outside of source root](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/12df6b6e-79d4-4e94-ada0-84c494fc7c49)

어느 날 인텔리제이 프로그램이 렉이 걸려 비정상적으로 종료를 하고 다시 재실행하였더니 위의 화면처럼 자바 내의 클래스 파일들이 인식을 하지 못하는 현상이 나타났다.<br>

이 현상은 인텔리제이가 소스 코드가 들어있는 폴더를 자동으로 지정을 못해 위와 같이 인식을 하지 못하여 결국 소스 코드를 수동으로 지정을 해줘야 한다.<br>
그리고 이 현상 몇 번이나 겪고 정확한 해결법을 기억하지 않아 이번엔 글로 남기려 한다.<br>

![1_25_intelliJ java file outside of source root2](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/38c5c01d-480b-4909-a1e9-7e615a75b0a5)

인텔리제이 왼쪽 위의 파일 - 프로젝트 구조 - 모듈 탭을 눌러 해당 모듈에 포함된 폴더들을 확인할 수 있다.<br>
그래서 **java**폴더를 우클릭하면 Sources 항목이 체크가 해제되었는지 확인하고 헤재되었다면 체크하여 적용하면 정상적으로 인식이 되어 있을 것이다.<br>


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/