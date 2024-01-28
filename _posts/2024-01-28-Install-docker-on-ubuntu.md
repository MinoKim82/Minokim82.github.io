---
title: 우분투에 도커 설치하기
date: 2024-01-28 10:20:00 +09:00
categories: [S/W]
tags: [리눅스, 도커, 셀프호스팅]
---

## 준비사항

- 우분투 시스템 (권장 버전: 18.04 이상)
- 인터넷 연결
- sudo 권한을 가진 사용자 계정

## 기존 버전 제거

리눅스 배포판에서는 비공식 Docker Engine이 설치 되어있을 수 있기 때문에 새로운 Docker Engine을 설치하기 전에, 충돌하는 패키지들을 제거해야 합니다.
제거해야 할 패키지는 다음과 같습니다:

- docker.io
- docker-compose
- docker-compose-v2
- docker-doc
- podman-docker
- containerd
- runc

아래 명령을 통해서 설치되 package를 제거합니다.

```sh
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

기존 docker 패키지를 사용해서 생성된 이미지, 컨테이너, 볼륨 및 네트워크는 자동으로 삭제되지 않기 때문에 더이상 사용하지 않는 다면 아래 명령을 이용해 삭제합니다.

```sh
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## 도커 APT Repository 등록하기

apt repository를 등록하면 간편하게 설치 및 업데이트가 가능하기 때문에 repository를 등록해줍니다. repository 등록 과정은 GPG Key 등록 과정과 apt 소스 등록 과정으로 이루어져 있습니다.

```sh
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## 도커 패키지 설치하기

이제 도커 패키지를 apt를 사용해서 설치 합니다.

```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 도커 설치 확인하기

아래 명령을 통해서 도커가 정상적으로 설치 되었는지 확인해 봅니다.

```sh
sudo docker run hello-world
```

정상적으로 설치 되었다면 아래와 같은 메시지가 표시됩니다.

```sh
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete
Digest: sha256:4bd78111b6914a99dbc560e6a20eab57ff6655aea4a80c50b0c5491968cbc2e6
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## 도커 제거하기

먼저 도커 엔진과 CLI 등 관련 패키지를 제거합니다.

```sh
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

Images, containers, volumes, custom configuration files은 자동으로 삭제 되지 않기 때문에 직접 삭제를 진행합니다.

```sh
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## 참조

- [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
