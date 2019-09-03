---
title: "VSCODE 에서 DOCKER 로 Jekyll 블로그 로컬 환경 셋팅하기"
last_modified_at: 2019-09-02T00:00:00+09:00
categories:
  - web
  - blog
tags:
  - jekyll
  - docker
toc: true
toc_sticky: true
toc_label: "목차"
---
## Docker 로 Jekyll 개발 환경 만들기

여기서는 `docker` 로 개발환경을 만드는 방법을 `Jekyll 블로그 만들기 tutorial` 로 설명해보겠습니다.

직접 설치하는 방법은 아래의 공식 홈페이지를 참고해주세요.

[Installation](https://jekyllrb.com/docs/installation)

## Jekyll blog 만들기 (git fork)

### step 1. Git Repository Fork 하기

> [https://jekyllthemes.io](https://jekyllthemes.io/)

마음에 드는 테마를 골랐다면, 해당 테마의 git 저장소에 들어가서 자신의 저장소로 fork 합니다.

✔ 저는 minimal-mistakes 를 골랐습니다.

### step 2. 기본 설정하기

- repository 이름 변경

    `username.github.io` 로 변경해야 Github Page 에서 `https://username.github.io` 주소로 호스팅 해줍니다.

- `_config.yml` 변경

    fork 한 테마의 가이드에 따라 설정 yml 을 변경합니다.

    주로 변경할 곳은  `title`, `author`, `base_url` 정도 있습니다.

이정도면 github 에서 바로 호스팅이 되어 `https://username.github.io` 로 접속이 가능합니다.

매우 간단한 방법이지만 게시글을 작성하거나, 블로그를 커스텀 하게 변경하고 결과를 확인하고자 한다면 매번 commit 해서 확인해야 하기 때문에 git commit 이력이 매우 지저분해 질 것입니다.ㅠ

## Docker 로 Local 에 개발환경 만들기

> 사전 준비 : git, docker 설치, vscode 설치

### step 1. clone

fork 한 repository 를 local 에 clone 합니다.

    $git clone https://github.com/username/username.github.io.git

### step 2. docker-compose.yml 작성

    version: '3.3'
    
    services:
      blog:
        image: jekyll/jekyll:latest
        command: jekyll serve --force_polling --draft --livereload
        ports:
          - "4000:4000"
        volumes:
          - ".:/srv/jekyll"

clone 한 프로젝트 디렉토리에 `docker-compose.yml` 을 생성하고 내용을 위와 같이 작성합니다.

- `version`  : docker-compose 의 버전을 의미합니다.
- `services` : docker-compose 는 여러개의 컨테이너를 띄울 수 있습니다. 하단 블록에 기입합니다.
- `blog` : 실행할 컨테이너의 이름입니다.
- `image` : docker 이미지
- `command` : 컨테이너가 run 하면 실행할 명령
- `ports` : `{local_port}:{container_port}` , 로컬 4000번 port를 컨테이너의 4000번 포트와 매핑
- `volumes` : `{local_dir}:{container_dir}` , 현재 경로를 컨테이너의 `/srv/jekyll` 경로에 mount 하겠다는 뜻

### step 3. vscode 의 docker 플러그인 설치

![vscode-docker-plugin.jpg](/assets/images/posts/2019-09-02/vscode-docker-plugin.jpg)

플러그인 설치 후, `docker-compose.yml` 파일에서 마우스 오른쪽 클릭하면

- Compose Down
- Compose Restart
- Compose Up **← 선택**

![vscode-docker.jpg](/assets/images/posts/2019-09-02/vscode-docker.jpg)

Docker tab 으로 이동 하면 container 가 실행 된 것을 확인 할 수 있습니다.

![vscode-docker-container.jpg](/assets/images/posts/2019-09-02/vscode-docker-container.jpg)

이제 [`localhost:4000`](http://localhost:4000) 으로 접속하면 블로그가 실행 되었음을 확인 할 수 있습니다.

게시글을 작성하거나 새로 업로드하면 자동으로 jekyll 이 재시작하지만, 설정 파일이 변경되어도 jekyll을 재시작 해주지 않으므로 필요하다면 컨테이너를 오른쪽 클릭해서 `restart` 해줍니다.

![vscode-docker-restart.jpg](/assets/images/posts/2019-09-02/vscode-docker-restart.jpg)

그밖에 `View Logs` 버튼은 컨테이너의 로그를 볼 수 있고,

`Attach Shell` 버튼은 컨테이너에 접속해서 명령을 수행 할 수 있습니다.

만약, windows 10 pro 이하 버전에서 docker 를 설치 했다면 docker toolbox 로 실행 할 텐데, 

그런 경우에는 실행을 확인 할 때 `[localhost:4000](http://localhost:4000)` 이 아니라 `http://192.168.99.100:4000` (linux virtualbox) 로 접속해야 합니다.