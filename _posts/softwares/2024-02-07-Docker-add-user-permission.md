---
title: Docker 사용 중 "Permission Denied" 오류 해결하기
date: 2024-02-07 12:30:00 +09:00
categories: [S/W]
tags: [Docker, Linux]
---

Docker 사용 시 docker 명령어의 권한을 root 사용자만 소유하고 있기 때문에, 
일반 유저의 계정으로 docker 명령어를 실행하면 "permission denied"와 같은 권한 오류를 마주치게 됩니다. 
이런 문제를 해결하는 가장 일반적인 방법은 사용자에게 Docker 권한을 부여하는 것입니다. 여기서는 두 가지 방법을 알아보겠습니다.

## 사용자를 Docker 그룹에 추가하기

먼저, 현재 사용자의 그룹을 확인해야 합니다.

```bash
$ groups $USER
```

Docker라는 이름의 그룹이 시스템에 없다면, 새로 생성해야 합니다.

```bash
sudo groupadd docker
```

현재 사용자를 Docker 그룹에 추가합니다.

```bash
sudo usermod -aG docker $USER
```

변경 사항을 적용하기 위해 컴퓨터를 재부팅하거나, 터미널 세션을 새로 시작해야 할 수 있습니다.

재부팅 완료 된 후 사용자가 Docker 그룹에 정상적으로 추가되었는지 확인하기 위해 다시 한번 그룹을 확인합니다.

```bash
groups $USER
```

## sudo 없이 Docker 명령어 실행하기 (권장하지 않음)

Docker 데몬 소켓 파일의 권한을 변경하여 모든 사용자가 Docker 명령어를 실행할 수 있게 할 수 있습니다.
(이 방법은 보안상 위험할 수 있으므로, 가능한 1번 방법을 사용하는 것이 좋습니다.)

```bash
sudo chmod 666 /var/run/docker.sock
```

변경 사항을 적용하기 위해 로그아웃하고 다시 로그인합니다.

## 참고
- Docker 권한 관리에 대한 자세한 내용은 [Docker 공식 문서(Docker CLI Reference)](https://docs.docker.com/engine/reference/commandline/cli/)에서 확인할 수 있습니다.
- 위 방법들은 대부분의 Linux 배포판에서 작동합니다. 사용 중인 배포판에 따라 명령어 또는 설정이 다를 수 있으니, 해당 배포판의 문서를 참조하는 것이 좋습니다.
