---
layout: post
title:  "부트스트랩" 
date:    2023-10-13 21:51:12 +09:00
categories: jekyll update
---


백엔드 개발자를 목표로 공부를 하고 있지만, 자산이 만든 백엔드 기능이 잘 기능하고 있는지 테스트를 하기 위해서 웹페이지를 만들 때가 있는데 이럴 때 많은 사람들이 사용되는 오픈소스 CSS 라이브러리 **부트스트랩(bootStrap)**에 대해서 복기하고자 작성한다. <br>

---------------------------------------

bootStrap5 설치
-------------

지금 이 글을 작성하는 시점에는 부트스트랩의 최신버전은 5.3.2이며 다음과 같이 외부 스타일시트를 <head></head>사이에 추가, 웹페이지 </body></html> 끝나기 전 앞에 외부 스크립트를 추가하면 바로 부트스트랩을 사용할 수 있다.<br>

```java
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
.....

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
```


그리고 부트스트랩의 일부 기능들은 이벤트에 반응하는 구조로 되어 있어 몇몇 자바스크립트 라이브러리를 필요로한다.<br>

```java
<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.8/dist/umd/popper.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.min.js"></script>
```


CSS,자바스크립트 말고도 부트스트랩 공식 홈페이지에서 템플릿들을 다운받아서 바로 사용할 수도 있으며, start bootstrap이라는 사이트도 있는데 여기는 부트스트랩 코드를 이용해 완성된 웹을 무료로 이용하게 해주는 사이트이다.<br>

[start Bootstrap](https://startbootstrap.com/templates?showPro=false) 

---------------------------------------

bootStrap5 사용법
-------------

이렇게 부트스트랩을 적용하면 HTML 태그 속성에 부트스트랩에 정의된 다양한 클래스들을 부여하면 많은 속성과 디자인을 사용할 수 있는데, 사용할 수 있는 문법들이 많다보니 헷갈려 때때로 어려울 때도 있어 정리하기로 하였다.<br>

먼저 인풋 버튼이라는 태그를 생성하고 클래스를 ```btn btn-(임의의 클래스명)```을 추가하면 다양한 디자인을 쓸 수 있다.<br>

![10_13button](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/ed0f3a0d-0e1c-4c44-bf59-357d1e4ee607)


```html
<button type="button" class="btn">Basic</button>
<button type="button" class="btn btn-primary">Primary</button>
<button type="button" class="btn btn-secondary">Secondary</button>
<button type="button" class="btn btn-success">Success</button>
<button type="button" class="btn btn-info">Info</button>
<button type="button" class="btn btn-warning">Warning</button>
<button type="button" class="btn btn-danger">Danger</button>
<button type="button" class="btn btn-dark">Dark</button>
<button type="button" class="btn btn-light">Light</button>
<button type="button" class="btn btn-link">Link</button>
```

---------------------------------------

그리드
-------------

부트스트랩의 그리드 시스템은 레이아웃을 **행(row,가로)**클래스 안에 **열(col,세로)**를 사용하여 관리하며, 열은 최대 12개의 열을 기준으로 한다. <br>

![10_13grid12](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/d72b6875-fdc1-4f54-9835-a939b8f43029)


또한 그리드를 관리할 때 6개의 반응형 중단점을 이용한다. 중단점은 min-width 미디어 쿼리를 기반으로 한다. 즉, 중단점과 그 위에 있는 모든 중단점에 영향을 미친다.<br>

|  | xs (<576px) | sm (>=576px) |md (<768px) | lg (<992px)|xl (<1200px) | xxl (<1400px)|
|---|:---:|---:|:---:|---:|:---:|---:|
| `컨테이너 max-width` | 없음 (자동)| 540px | 720px | 960px | 1140px | 1320px |
| `클래스명` | .col- | .col-sm- | .col-md- | .col-lg- | .col-xl- | .col-xxl- |


만약 12개의 칸을 4칸씩 나눠서 사용하고 싶다면 ```col md-4```이라는 클래스를 추가하면 가로 width : 768px 지점에 미디어쿼리의 브레이크 포인트가 동작하게 된다.


### 공백

![html-box](https://github.com/jiuseu/hyuntrace0915.github.io/assets/109057859/e527f53f-7df0-4817-8f05-fbe53f0add90)

위의 이미지 HTML 박스 모델들을 4가지 속성이 있고 부트스트랩에 당연히 이 속성들을 기준으로 공백을 줄 수 있는 클래스들이 있다. <br>

##### m/p

- m : margin

- p : padding

<br>

##### t , b , l , r ,x , y

- t : top

- b : bottom

- l : left

- r : right

- x : x축 -> left , right

- y : y축 -> top , bottom

<br>

##### 0, 1, 2, 3, 4, 5, auto

- 0 : 0

- 1 : .25rem( font-size가 16px이면, 4px) 크기

- 2 : .5rem( font-size가 16px이면, 8px) 크기

- 3 : 1rem( font-size가 16px이면, 16px) 크기

- 4 : 1.5rem( font-size가 16px이면, 24px) 크기

- 5 : 3rem( font-size가 16px이면, 48px) 크기

- auto : margin의 자동으로 세팅

<br>

##### n1, n2, n3, n4, n5

- n : negative을 의미

- n1 : -.25rem( font-size가 16px이면, -4px) 크기

- n2 : -.5rem( font-size가 16px이면, -8px) 크기

- n3 : -1rem( font-size가 16px이면, -16px) 크기

- n4 : -1.5rem( font-size가 16px이면, -24px) 크기

- n5 : -3rem( font-size가 16px이면, -48px) 크기

<br>

예를 들어 ```row mt-3```라는 클래스를 추가하였다면 row 클래스의 margin의 top을 3만큼 공백을 주겠다는 의미이다.


---------------------------------------




[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
