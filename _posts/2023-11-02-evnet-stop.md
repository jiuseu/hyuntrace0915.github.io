---
layout: post
title:  "자바스크립트 이벤트 중단 처리" 
date:    2023-11-02 22:56:31 +09:00
categories: jekyll update
---

event.preventDefault() / event.stopPropagation()
-------------


웹페이지에서 자바스크립트 이벤트 처리를 생성하다보니 일부 이벤트 함수들에 ```event.preventDefault()```, ```event.stopPropagation()```를 넣어줬는데 정확히 이 두 개의 메소드들이 어떠한 기능을 하는지 모르기에 정리하고자 한다.<br>


이벤트 전파
-------------

html를 보았을 때 가장 최상위 태그를 클릭했을 때 최상위 태그 클릭 이벤트만 발생하지만, 가장 최하위 태그를 클릭하였을 때에는 최하위 뿐만 아니라 최상위 태그까지 이벤트가 발생하는데 이것을 **이벤트 전파**라고 한다.<br> 

```html

<div class = 'row first'>
최상위 태그
   <p class = "col second">
     중간 태그
      <span class = "third">
          최하위 태그 
      </span>
   </p>
</div>

```
```javascript
document.querySelector('.first').addEventListener("click",function(e){
    console.log("퍼스트 이벤트");
},false)

document.querySelector('.second').addEventListener("click",function(e){
    console.log("세컨드 이벤트");
},false)

document.querySelector('.third').addEventListener("click",function(e){
    console.log("서드 이벤트");
},false)
```

이렇게 예시 코드를 만들었을 때 ```third```클래스를 가진 최하위 태그를 클릭하면 최하위 태그 이벤트 뿐만 아니라 맨 위인 div 태그 이벤트까지 발생하여 3개의 이벤트가 출력된다.<br>

```
서드 이벤트
세컨드 이벤트
퍼스트 이벤트
``` 

---------------------------------------

event.stopPropagation()
-------------

그래서 다른 태그 이벤트까지 전파되는 것을 막기 위한 메소드가 ```event.stopPropagation()```이다.<br>
```third```클래스 이벤트에 ```event.stopPropagation()```를 추가하면 상위 DOM으로 이벤트가 전파되지 않아 해당 태그의 이벤트만 동작한다.<br>

```javascript
document.querySelector('.first').addEventListener("click",function(e){
    
    console.log("퍼스트 이벤트");

},false)

document.querySelector('.second').addEventListener("click",function(e){
    
    console.log("세컨드 이벤트");

},false)

document.querySelector('.third').addEventListener("click",function(e){

    e.stopPropagation()
    console.log("서드 이벤트");

},false)
```
```
서드 이벤트
``` 

---------------------------------------

event.preventDefault()
-------------

```html

<div class = 'row first'>
최상위 태그
   <p class = "col second">
     중간 태그
      <a class = "third">
          최하위 태그 
      </a>
   </p>
</div>

```
```html

<form method="get" id="f1">

<input tpye="text" name="name" placeholder="이름"/>
<button type="submit" class="sumbitBtn">Sumbit</button>

</form>

```
```javascript
document.querySelector('.first').addEventListener("click",function(e){
    
    console.log("퍼스트 이벤트");

},false)

document.querySelector('.second').addEventListener("click",function(e){
    
    console.log("세컨드 이벤트");

},false)

document.querySelector('.third').addEventListener("click",function(e){

    e.stopPropagation()
    console.log("서드 이벤트");

},false)

document.querySelector('sumbitBtn').addEventListener("click", function (e){
 
    e.stopPropagation()
    document.querySelector("form").submit()

},false)
```

이번엔 a 태그와 form 예제를 만들었는데 위의 a태그를 클릭하면 상위로 이벤트가 전파가 되지 않겠지만 a태그의 기본 동작인 URL이동이 동작되고, form 또한 ```submitBtn```버튼을 클릭하여 제출하면 페이지가 새로고침이 된다.<br>

이러한 경우에  ```event.preventDefault()```를 추가하면 기본 동작을 막을 수 있기에 a 태그나 form 같은 페이지가 이동하거나 새로고침되는 동작을 막을 수 있다.<br>


```javascript
document.querySelector('.third').addEventListener("click",function(e){

    e.preventDefault()
    e.stopPropagation()
    console.log("서드 이벤트");

},false)

document.querySelector('sumbitBtn').addEventListener("click", function (e){
 
    e.preventDefault()
    e.stopPropagation()
    document.querySelector("form").submit()

},false)
```

---------------------------------------



[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
