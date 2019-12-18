---
title: "Windows 10 Docker Toolbox 에서 volume 마운트 permission 오류"
last_modified_at: 2019-11-19T21:00:00+09:00
categories:
  - trouble-shooting
tags:
  - docker
  - permission error
toc: true
toc_sticky: true
toc_label: "shortcut"
---

Windows 10 home 환경에서 docker 컨테이너가 제대로 뜨지 않았다...

## 원인 파악

오류가 발생한 컨테이너의 로그를 확인해보니

![view-logs.png](/assets/images/posts/2019-11-19/view-logs.png){: .align-center}

jekyll docker 에서 파일을 생성할 때, docker 를 실행하는 가상 os (virtual linux) 에서 host os (windows 10) 로 마운트된 경로에서 폴더나 파일의 생성 권한이 없기에 발생한 오류였다  
(home 폴더에서 작업하면 해당 오류는 발생하지 않는다)

```bash
'mkdir' : Permission denied
```

![docker-volume-permission-error.jpg](/assets/images/posts/2019-11-19/docker-volume-permission-error.jpg){: .align-center}

## 문제 해결

[Docker Toolbox 에서 D 드라이브 볼륨 경로 지정하기](https://velog.io/@public_danuel/volume-path-in-docker){: target="\_blank"}

위 링크의 방법을 이용하여 해결하였다
