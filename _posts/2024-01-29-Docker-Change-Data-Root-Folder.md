---
title: Docker에서 Data Root 폴더 변경하기
date: 2024-01-29 09:18:00 +09:00
categories: [S/W]
tags: [linux, docker]
---

안녕하세요. 오늘은 Docker를 사용하시는 분들께 특히 도움이 될 주제, 바로 'Docker에서 **Data Root** 폴더를 변경하는 방법'에 대해 알아보겠습니다. Docker를 사용하다 보면 기본 설정된 Data Root 폴더의 용량이 부족하거나 다른 드라이브에 데이터를 저장하고 싶을 때가 있습니다. 이럴 때 **Data Root** 폴더의 위치를 변경하는 방법을 알고 있으면 큰 도움이 됩니다.

##  Data Root 폴더의 중요성

먼저, Docker의 Data Root 폴더는 Docker 이미지, 컨테이너, 볼륨 등이 저장되는 곳입니다. 이 폴더의 위치나 크기는 Docker의 성능과 안정성에 직접적인 영향을 미칩니다. 기본적으로 Linux 시스템에서는 /var/lib/docker에 위치해 있으나, 사용자의 시스템 환경이나 요구에 따라 이를 변경할 필요가 생길 수 있습니다.

##  변경 전 주의사항

Data Root 폴더를 변경하기 전에 중요한 몇 가지 사항을 고려해야 합니다. 먼저, 현재 실행 중인 모든 컨테이너를 정지시켜야 합니다. 또한, 기존 Data Root 폴더의 데이터를 새 위치로 옮길 때 데이터 손실을 방지하기 위해 백업을 진행하는 것이 중요합니다.


## 단계별 변경 방법

이제 본격적으로 Docker의 **Data Root** 폴더를 변경하는 단계를 살펴보겠습니다.

### Docker 서비스 중지

먼저, Docker 서비스를 중지해야 합니다. 이는 `systemctl stop docker` 명령어로 수행할 수 있습니다.

```bash
sudo systemctl stop docker.socket
sudo systemctl stop docker
sudo systemctl stop containerd
```

### 기존 Data Root 폴더의 데이터 이동

기존 **Data Root** 폴더(`/var/lib/docker`)의 데이터를 새로운 위치로 복사합니다. `rsync` 명령어를 사용하여 데이터를 안전하게 이동시킬 수 있습니다.

```bash
rsync -avzh /var/lib/docker/ /new/path/docker
```


### Docker 설정 변경

Docker의 설정 파일인 `/etc/docker/daemon.json`을 편집하여 **data-root** 값을 새 경로로 변경합니다. 설정 파일이 없다면 새로 생성해야 합니다. 아래는 새 경로 설정 예시입니다.

```json
{
  "data-root": "/new/path/docker"
}

```

### Docker 서비스 재시작

변경 사항을 적용하기 위해 Docker 서비스를 다시 시작합니다. `systemctl start docker` 명령어로 재시작할 수 있습니다.

```bash
systemctl start docker
```

### 변경 후 확인 및 테스트

변경 후에는 Docker가 새로운 Data Root 폴더를 정상적으로 사용하는지 확인해야 합니다. 아래의 `docker info` 명령어를 통해 Data Root 경로가 올바르게 변경되었는지 확인할 수 있습니다.

```bash
docker info | grep "Docker Root Dir"
```

또한, 간단한 컨테이너를 실행하여 실제로 데이터가 새로운 경로에 저장되고 있는지 테스트하는 것이 좋습니다.

## 맺음말

이상으로 Docker에서 Data Root 폴더를 변경하는 방법에 대해 알아보았습니다. 이 과정을 통해 Docker의 저장 공간 관리에 더 많은 유연성을 가질 수 있게 되었기를 바랍니다.
