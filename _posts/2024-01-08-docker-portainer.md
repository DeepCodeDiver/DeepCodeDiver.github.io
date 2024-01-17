---
title: Install Docker and Portainer
layout: post
categories: [setup]
tags: [ubuntu23.10, docker, portainer]
---

Ubuntu23.10 이 설치된 Dell workstation에서 SSH 원격까지 설치를 완료했습니다. 하지만 보안상의 문제와 개발 환경의 격리를 통해 우리는 좀더 깔끔하게 서버를 사용할 수 있습니다. 본 포스트에서는 이를 위해 OS상의 가상화를 지원하는 Docker 와 이를 쉽게 관리할 수 있는 Portainer 에 관한 설정을 진행해보도록 하겠습니다.

# 1. Install Docker

docker는 컨테이너화된 어플리케이션을 만들고 쉽게 관리할 수 있는 오픈소스 플랫폼입니다. 기존의 가상화기술과는 달리 호스트 운영체제와의 직접적인 상호작용을 통해 가볍고 효율적인 환경을 제공합니다.

docker 의 설치는 아래 두개의 패키지를 설치하면 완료됩니다. 😛

```bash
sudo apt install docker.io docker-compose
```

docker 서비스를 시작하여 사용할 수 있도록 준비합니다.

```bash
sudo systemctl enable --now docker
sudo systemctl status docker
```

![Untitled](/assets/images/post0108/Untitled.png)

# 2. Verifying Docker version and Testing docker with hello world

설치된 docker 가 잘 동작하는지 확인해보도록 하겠습니다. 먼저 버전을 확인해보면 24.0.5가 설치되어있는 것을 확인할 수 있습니다.

```bash
docker --version
> Docker version 24.0.5, build 24.0.5-0ubuntu1
```

간단하게 hello-world 가 동작하는 docker를 실행시켜 봅시다. 터미널 출력에서 Hello from Docker! 가 잘 출력되었네요. 👏

```bash
sudo docker run hello-world
```

![Untitled](/assets/images/post0108/Untitled 1.png)

# 3. Download and Run Portainer

다음으로 docker를 효율적으로 관리할 수 있는 portainer 라는 웹 서비스가 실행되는 docker 컨테이너를 다운받고 실행시켜보도록 하겠습니다.

아래 명령을 통해 쉽게 다운로드 및 실행이 가능합니다. 자세한 argument 설명은 생략하도록 하겠습니다. 

```bash
sudo docker run -d -p 8000:8000 -p 9000:9000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

![Untitled](/assets/images/post0108/Untitled 2.png)


# 4. Access Portainer

Portainer는 앞서 설명한바와 같이 docker를 효율적으로 관리할 수 있는 웹서비스 입니다. 따라서 Web browser에서 아래 주소로 접속을 하면 사용하기 편리한 웹 UI 가 나타나게 됩니다.

- HTTP: http://[WORKSTATION_IP_ADDRESS]:9000
- HTTPS: https://[WORKSTATION_IP_ADDRESS]:9443

접속을 하게 되면 보안 관련 인증서가 없어서 경고 문구가 뜨지만 무시하고 접속하면 아래와 같은 화면이 나옵니다.

먼저, 초기 관리자의 user name 과 password를 설정해준다.

![Untitled](/assets/images/post0108/Untitled 3.png)


설정을 완료하면 아래와 같은 page 가 나오며 `Get Started`를 눌러서 portainer 에 접속한다.

![Untitled](/assets/images/post0108/Untitled 4.png)

`local`을 클릭하면 현재 docker 의 image와 container volume등의 설정 및 조작이 가능하다.

![Untitled](/assets/images/post0108/Untitled 5.png)

![Untitled](/assets/images/post0108/Untitled 6.png)

다른 docker 이미지가 없다면 아래와 같이 test 에 사용한 hello-world와 portainer container 가 존재하는 것을 볼 수 있고, portainer 가 실행 상태임 확인할 수 있다.

![Untitled](/assets/images/post0108/Untitled 7.png)

# Reference

- [https://shape.host/resources/installing-docker-on-ubuntu-23-10-step-by-step-guide](https://shape.host/resources/installing-docker-on-ubuntu-23-10-step-by-step-guide)
- [https://www.wundertech.net/portainer-on-ubuntu/](https://www.wundertech.net/portainer-on-ubuntu/)