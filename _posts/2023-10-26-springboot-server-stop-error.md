---
layout: post
title:  "Spring Boot 서버 종료시 에러 출력" 
date:    2023-10-26 22:46:26 +09:00
categories: jekyll update
---

Build cancelled while executing task...
-------------

항상 스프링부트 서버를 정지를 하면 에러 메세지들이 출력이 되었다.<br>
그렇다고 아무런 문제가 일어나지 않았고 메세지들만 출력되었기에 놔두었지만 서버만 정지시켜도 에러 메세지들이 출력되는 것이 신경이 쓰일 것 같았다.<br>

![10_26_build_error](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/db6fbed2-c1cb-4dd8-b5de-929d4bfef78a)


이러한 에러 메세지들이 출력된 원인은 인텔리제이의 환경 설정 때문인데 여태까지 **gradle**로 빌드하여 실행하였다.<br>

그렇다면 해결방법은 그냥 환경 설정을 변경만 해주면 된다.<br>
인텔리제이의 왼쪽 위에 위치한 '파일 -> 설정'에 들어가서 설정 검색창에 **gradle** 키워드를 검색하면 왼쪽 탭에 '빌드,실행,배포 -> 빌드 도구 -> Gradle' 설정창이 선택되면서 아래와 같은 화면이 뜰 것이다.<br> 

![10_26_setting_gradle](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/1e6240cb-32a9-40e3-8616-283369882602)

 
중간에 위치한 '빌드 및 실행'과 '테스트 실행' 설정이 처음에는 **gradle**로 기본으로 설정되어 있을 것이다.<br> 
이것을 **intellij IDEA**로 변경 후 적용 및 확인을 하고 다시 인텔리제이를 재시작하여 서버를 시작 및 정지를 하면 위와 같은 에러 메세지들은 출력되지 않고 정상적으로 종료된다.<br>

---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
