---
layout: post
title:  "VSCode C/C++ 세팅하기 위한 gcc 설치" 
date:    2024-06-18 22:42:22 +09:00
categories: jekyll update
---

### GCC 설치하기

<br>

정보처리기사를 공부하면서 주로 배우는 자바 뿐만 아니라 C언어, 파이썬이 나오면서 특히 코딩 문제였기에 각자의 언어에 디테일한 부분이 조금씩 달랐기에 같은 코드라도 다른 결과가 나오기도 하였다.<br>

백문이 불여일견이라고 생소한 것은 이론도 몇 번 읽어보는 것도 당연 중요하겠지만 코딩을 직접하는 것이 이해가 빠르다는 것은 자바를 여태 코딩을 해본 경험으로써 알고 있다.<br> 

먼저 윈도우 환경에 **VSCode**에서 **C언어**를 컴파일하고 실행하기 위해서는 **gcc**를 설치해야 한다.<br>

다운로드 링크 : <https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/>


![6_18_gccdownload](https://github.com/jiuseu/project/assets/109057859/44f35888-da3d-406e-9da9-c077b9430ddf)

먼저 링크로 들어가서 거의 맨아래로 내리면 위의 이미지와 같이 실행 파일의 다운로드 링크가 있는데 나는 실행자체가 안되서 그 밑에 있는 압축 파일을 받아 압축 해제하여 설치하는 방법을 사용하였다.<br>

![6_18_gcc_folder](https://github.com/jiuseu/project/assets/109057859/70e40140-ee54-4119-83aa-959b73dbb086)


--------------------------------------- 

### 윈도우 환경 변수 설정


- 시스템 -> 시스템 속성 -> 환경 변수 -> 시스템 변수 탭에서 Path를 눌러 편집 -> 새로 만들기 -> mingw64\bin (gcc가 설치한 경로)        

![6_18_gcc_system](https://github.com/jiuseu/project/assets/109057859/700b4c11-521c-4b54-a654-b576b3e718df) 


####  GCC 설치 버전 확인

 - **cmd**를 관리자 권한으로 실행하여 ```gcc --version```를 입력하여 버전이 나온다면 제대로 설치가 되었다는 것이다.<br>

![6_18_gcc_cmd_version](https://github.com/jiuseu/project/assets/109057859/3280a3f8-7719-43db-a2d9-b831f7141c5c)



---------------------------------------







[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/