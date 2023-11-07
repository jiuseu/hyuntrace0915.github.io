---
layout: post
title:  "class path resource cannot be opened because it does not exist" 
date:    2023-11-07 22:26:05 +09:00
categories: jekyll update
---

java.io.FileNotFoundException
=============

진행하고 있던 프로젝트의 복사본을 따로 만들어 복사본에 몇 개의 클래스를 지운 후 다시 복사본을 실행했더니 아래와 같은 에러가 출력이 되었다.<br>

```java
Caused by: java.io.FileNotFoundException: class path resource [파일 경로] cannot be opened because it does not exist
```
<br>

당연 이런 에러가 나타난 것은 지워진 파일이 없어져서 발생하는 예외인 것 같은데 그래서 gradle - clean으로 기존에 생성된 것을 지우고 다시 빌드해서 실행했지만 위와 같은 예외가 계속 출력되었다.<br>

---------------------------------------


해결
-------------

그럼 다른 방법이 있지 않을까 검색하던 중, 인텔리제이의 빌드가 꼬였을 수 있다는 경우였다.<br> 
그래서 해결방법은 gradle 탭에서 왼쪽 위를 보면 새로고침 아이콘이 있는데 클릭하면 모든 Gradle 프로젝트를 다시 빌드하는 기능을 갖고 있다.<br>

![20231107-gradle-reload](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/3ca9580e-618b-41bb-8ad7-38d5a67674b0)

<br>

새로고침 후 위 build 메뉴에서 **프로젝트 다시 빌드(Rebuild Project)**를 눌러준다.<br>
이 기능은 인텔리제이가 이전에 생성된 모든 빌드 파일을 삭제한 다음 처음부터 다시 빌드한다.<br>

![20231107-buildproject](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/43328a1f-32ae-451b-9b48-b7af5061c6cc)


이후 다시 실행하면 정상적으로 에러가 출력되지 않고 동작할 것이다.<br>



---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
