---
layout: post
title:  "Gradle 프로젝트 인식 (이전 글에 대한 문제에 해결)" 
date:    2024-01-26 23:01:11 +09:00
categories: jekyll update
---

### Gradle 탭이 안보이거나 Gradle 프로젝트 인식

인텔리제이 모듈 경로를 지정해서 어제와 같은 프로젝트가 인식을 하지 못하는 문제를 해결이 되었는 줄 알았으나 오늘도 프로젝트를 실행시켜 똑같이 인식하지 못하여 모듈 경로 지정을 해줬으나 프로젝트 빌드를 하면 여러 클래스들이 컴파일러가 되지 않았다.<br>

결국 어떤 문제 때문에 잘 되던 많은 클래스들이 인식되지 않을까 인텔리제이 설정들을 건드려 본 결과 다른 정상적으로 작동되는 프로젝트를 비교해본 결과 문제가 된 프로젝트의 ```gradle``` 설정 안에 세부적인 설정을 할 수 있는 부분이 없다는 것이 차이점이었다.<br>

![1_26_intelliJ gradle_project_setting](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/74891a32-a531-4b5d-8eaa-9297333e6c8e)

그렇다면 어떻게 프로젝트를 **Gradle**으로 인식시킬 수 있냐면 왼쪽 프로젝트 탭에 ```build.gradle```파일을 우클릭하여 **Gradle 프로젝트 연결(Link/import Gradle Project)**을 클릭하면 **Gradle** 플러그인 탭이 생기게 되면서 인식하게 된다.<br>

다음으로 프로젝트의 **JDK(SDK)**와 **Gradle**의**JVM**의 자바 버전을 맞추면 이번엔 진짜로 정상적으로 프로젝트가 인식이 될 것이다.<br>



---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/