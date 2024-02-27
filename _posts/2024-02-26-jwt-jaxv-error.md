---
layout: post
title:  "javax/xml/bind/DatatypeConverter 에러" 
date:    2024-02-26 2g2:35:13 +09:00
categories: jekyll update
---

### javax/xml/bind/DatatypeConverter 에러

![2_26_javax-xml-bind-datatypeconverter](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/4a2353e4-2615-46ae-8d4a-8f2f3db348e0)


**JWT**생성 테스트를 하던 중 위의 이미지와 같은 에러가 발생해 생성이 되지 않았다.<br>
원인을 찾아본 결과 **JAVA**버전이 업데이트함에 따라 **JAXV API**를 JDK에서 제거가 된 것이 원인이 되었다.<br>

---------------------------------------


### 해결 방안

- build.gradle

```gradle
dependencies {

implementation 'javax.xml.bind:jaxb-api:2.3.0'

}
```

해결 방법은 단순한데 ```build.gradle```에 위의 디펜던시를 추가 한 뒤 새롭게 **JWT**생성을 다시 시도하면 정상적으로 실행이 되면서 문제가 해결되었다.<br>


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/