---
title: "JAVA error: unmappable character for encoding UTF-8"
last_modified_at: 2019-09-03T00:00:00+09:00
categories:
  - trouble-shooting
  - web
tags:
  - java
  - spring
  - build
toc: true
toc_sticky: true
toc_label: "목차"
---
spring-boot 프로젝트를 `debug` 로 실행하면 동작을 하지만 `gradle` 로 빌드를 하면 다음과 같은 인코딩 오류가 발생하였다.

    Controller.java:21: error: unmappable character for encoding UTF-8
     * @author 占쏙옙占쏙옙占쏙옙
               ^

한글 주석이 깨지는 것으로 보이는 데  
`unmappable character for encoding UTF-8` 이 에러 메시지로 구글링을 해보면 거의 대부분  
IDE의 환경 설정이나 build.gradle 에서 encoding 옵션을 변경하는 것으로 해결하라고 한다.


아마 대부분 해결 되겠지만 나의 경우는... 그렇지 못하였다 ㅠ  
어쩌면 나와 같은 고생을 하는 사람이 있을 까 하여 글을 공유한다...

개발 환경은 다음과 같다.

- Windows 10
- IntelliJ
- Gradle

우선 intelij 기준으로 빌드 에러 터미널을 보면 오류가 난 소스코드로 이동이 가능하다.

![intellij-error.jpg](/assets/images/posts/2019-09-03/intellij-error.jpg){: .align-center}

소스코드로 가면 친절한 InteliJ (갓갓갓!!) 가 인코딩을 변경하는 것이 어떻겠냐는 안내를 해준다.

![intellij-encoding.jpg](/assets/images/posts/2019-09-03/intellij-encoding.jpg){: .align-center}

그렇다... 보통 이렇게 변경하면 IDE 의 옵션을 변경해주는 것이다.

그것도 이 파일만!

이렇게 해결되는 가 했으나

배포하고 있는 Jenkins 에서는 동일한 빌드 오류로 배포가 되지 않았다.

이유는 이러하였다.

jenkins 의 빌드 환경은 linux 였기 때문에...

# 해결방법

파일 자체의 인코딩을 `UTF-8` 로 변경해 주어야 했다.

방법은 아까 InteliJ의 안내 화면에서 `x-windows-949` 인코딩으로 로드하고 (Reload in 'x-windows-949)

![reload-x-windows-949.png](/assets/images/posts/2019-09-03/reload-x-windows-949.png){: .align-center}

한글 주석이 잘 나오는 것을 확인하고

![hangul-comment.png](/assets/images/posts/2019-09-03/hangul-comment.png){: .align-center}

하단의 인코딩 convert 버튼을 (명칭이 이게 맞는지는 잘 모르겠다) 누르고

`UTF-8` 을 선택하면 파일의 일의 인코딩이 `x-windows-949` 에서 `UTF-8` 로 변경된다.

![intellij-convert-encoding.jpg](/assets/images/posts/2019-09-03/intellij-convert-encoding.jpg){: .align-center}

동일한 오류가 발생하는 파일을 위와 같은 방법으로 모두 고치면 인코딩으로 인한 빌드오류는 해결 된다.

Jenkins 에서 배포 빌드도 잘 된다 😃