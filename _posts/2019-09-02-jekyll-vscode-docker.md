---
title: "VSCODE 에서 DOCKER 로 Jekyll 블로그 로컬 환경 셋팅하기"
last_modified_at: 2019-09-02T00:00:00+09:00
categories:
  - setup
tags:
  - jekyll
  - docker
toc: true
toc_sticky: true
toc_label: "shortcut"
---

docker 로 Jekyll 개발환경 셋팅 방법을 간단한 블로그를 하나 만들어 보면서 설명하고자 한다

그냥 jekyll 을 직접 설치하는 방법은 아래의 공식 홈페이지를 참고하자  
[Jekyll 설치하기](https://jekyllrb.com/docs/installation){: target="_blank"}

## 1. Jekyll blog 만들기

### - Git Fork

> [https://jekyllthemes.io](https://jekyllthemes.io){: target="_blank"}

위의 테마모음 사이트에서 마음에 드는 테마를 골랐다면, 테마의 git 저장소에 들어가서 자신의 저장소로 fork 한다

초보자는 Readme.md (가이드) 가 잘 되어있는 테마가 좋을 것 같다

> ✔ 나는 minimal-mistakes 를 선택했다  
[minimal-mistakes Quick Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide){: target="_blank"}

### - 기본 설정

- repository 이름 변경하기  
    `username.github.io` 로 변경해야 Github Page 에서 `https://username.github.io` 주소로 호스팅 해준다

- `_config.yml`  
    fork 한 테마의 가이드에 따라 `_config.yml`을 변경한다  
    주로 변경할 곳은 `title`, `author`, `base_url` 항목이다

여기까지 진행한다면 github 에서 바로 호스팅이 되어 `https://username.github.io` 로 접속이 가능하다

그러나, 이런 구성으로는 게시글을 작성하거나, 블로그 레이아웃 등을 변경하고 결과를 확인하려면 매번 commit 해서 확인해야 하기 때문에 git commit 이력이 매우 지저분해진다...ㅠ

수정사항들을 바로 확인하기 위해 Local 환경에 Jekyll 을 셋팅해보도록 하자!

## 2. Docker로 Local에 Jekyll 개발환경 셋팅하기

> 사전 준비 : git, docker 설치, vscode 설치

혹시 docker 를 설치하지 못한 사람은 먼저 설치해야 한다  
여기서 주의할 점은 windows 10 pro 사용자가 아닌 경우 Docker Toolbox 를 설치 해야 한다는 점이다  
[Docker Toolbox 설치하기](https://docs.docker.com/toolbox/toolbox_install_windows){: target="_blank"}

### - Git Clone

fork 한 repository 를 local 에 clone 한다

```bash
$git clone https://github.com/username/username.github.io.git
```

### - docker-compose.yml 작성하기

```yml
version: '3.3'

services:
  blog:
    image: jekyll/jekyll:latest
    command: jekyll serve --force_polling --drafts --livereload --trace
    ports:
      - "4000:4000"
    volumes:
      - ".:/srv/jekyll"
```

clone 한 프로젝트 디렉토리에 `docker-compose.yml` 을 생성하고 내용을 위와 같이 작성한다

- `version`  : docker-compose 의 버전을 의미
- `services` : docker-compose 는 여러개의 컨테이너를 띄울 수 있음. 하단 블록에 기입
- `blog` : 실행할 컨테이너의 이름
- `image` : docker 이미지
- `command` : 컨테이너가 run 하면 실행할 명령
- `ports` : `{local_port}:{container_port}` , 로컬 4000번 port를 컨테이너의 4000번 포트와 매핑
- `volumes` : `{local_dir}:{container_dir}` , 현재 경로를 컨테이너의 `/srv/jekyll` 경로에 mount 하겠다는 뜻

### - Vscode에 Docker 플러그인 설치하기

![vscode-docker-plugin.jpg](/assets/images/posts/2019-09-02/vscode-docker-plugin.jpg){: .align-center}

플러그인 설치 후, `docker-compose.yml` 파일에서 마우스 오른쪽 클릭

![vscode-docker.jpg](/assets/images/posts/2019-09-02/vscode-docker.jpg){: .align-center}

- Compose Down
- Compose Restart
- Compose Up **← 선택**

Docker tab 으로 이동 하면 container 가 실행 된 것을 확인 할 수 있다

![vscode-docker-container.jpg](/assets/images/posts/2019-09-02/vscode-docker-container.jpg){: .align-center}

이제 [`localhost:4000`](http://localhost:4000){: target="_blank"} 으로 접속하면 블로그가 실행되었다

게시글을 작성하거나 수정하는 컨텐츠 변경은 자동으로 jekyll 이 감지하고 재시작하여 반영하지만 설정 파일의 변경에는 따로 재시작 해주지 않으므로
수정사항이 반영되지 않는다면 컨테이너를 오른쪽 클릭해서 `restart` 버튼을 누른다

![vscode-docker-restart.jpg](/assets/images/posts/2019-09-02/vscode-docker-restart.jpg){: .align-center}

그밖에 `View Logs` 버튼은 컨테이너의 로그를 볼 수 있고  
`Attach Shell` 버튼은 컨테이너에 접속해서 명령을 수행 할 수 있다

만약, windows 10 pro 이하 버전에서 docker 를 설치 했다면 docker toolbox 로 실행 할 텐데
그런 경우에는 실행을 확인 할 때 [localhost:4000](http://localhost:4000){: target="_blank"} 이 아니라 `http://192.168.99.100:4000` (virtualbox) 로 접속해야 한다
