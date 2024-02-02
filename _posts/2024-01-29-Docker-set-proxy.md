---
title: Docker에서 Proxy 설정하기
date: 2024-01-29 09:50:00 +09:00
categories: [S/W]
tags: [linux, docker]
---

안녕하세요,  Docker 사용자들이 자주 마주치는 문제 중 하나인 'Proxy 설정'에 대해 알아보겠습니다. 특히 회사나 기관에서 인터넷 접속 시 Proxy를 거치는 경우, Docker도 이 Proxy 설정을 통해 외부 네트워크와 소통해야 합니다. 이 글을 통해 Docker에서 Proxy 설정하는 방법과 프록시를 설정하지 않았을 때 발생할 수 있는 에러 케이스를 단계별로 쉽게 설명드리겠습니다.

## Proxy 설정의 필요성

Docker를 사용하면서 외부 리소스를 다운로드하거나 API에 접근할 때, Proxy 서버를 거치지 않으면 네트워크 제한으로 인해 연결이 되지 않는 경우가 종종 있습니다. 이러한 상황에서 Proxy 설정은 필수적이며, 올바른 설정을 통해 Docker가 원활하게 외부 네트워크와 통신할 수 있도록 해줍니다.

##  프록시 미설정 시 발생하는 에러 케이스

Proxy를 설정하지 않았을 때, Docker는 다양한 네트워크 관련 에러를 일으킬 수 있습니다. 예를 들어, 이미지를 pull하거나 컨테이너에서 외부 API에 접근할 때 네트워크 연결 오류 메시지가 표시될 수 있습니다. 

이러한 오류 메시지 들은 아래 처럼 나타날 수 있습니다.

- `Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)`
- `Error response from daemon: Get https://registry-1.docker.io/v2/: read tcp 192.168.0.1:8585->34.205.13.154:443: read connection reset by peer`

이는 Docker가 Proxy를 통해 외부 네트워크에 접근할 수 없기 때문에 발생하는 문제입니다.

## Docker Proxy 설정 방법

Docker에서 Proxy 설정을 하는 방법은 크게 두 가지가 있습니다: `Docker 서비스 파일`을 편집하는 방법과 `Docker Daemon 설정 파일`을 사용하는 방법입니다.

### Docker 서비스 파일 편집

이 방법은 시스템 전체에 대한 Docker 서비스의 Proxy 설정을 변경합니다. `/etc/systemd/system/docker.service.d/http-proxy.conf `파일을 생성하거나 편집하여 Proxy 정보를 입력합니다.

```bash
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/"
Environment="HTTPS_PROXY=https://proxy.example.com:443/"
Environment="NO_PROXY=localhost,127.0.0.1,docker-registry.example.com"
```

### Docker Daemon 설정 파일 사용

Docker Daemon 설정 파일(`/etc/docker/daemon.json`)을 편집하여 Proxy 설정을 추가할 수 있습니다. 이 파일이 없다면 새로 생성해야 합니다.

```json
{
  "proxies":
  {
    "default":
    {
      "httpProxy": "http://proxy.example.com:80",
      "httpsProxy": "http://proxy.example.com:443",
      "noProxy": "localhost,127.0.0.1,docker-registry.example.com"
    }
  }
}
```

## 설정 변경 후 Docker 재시작

설정을 변경한 후에는 Docker 서비스를 재시작해야 합니다. 아래 명령어를 사용하여 Docker 서비스를 재시작할 수 있습니다.

```bash
systemctl daemon-reload
systemctl restart docker
```

## Prxoy 설정 확인하기

Docker 가 재시작 되면 아래 명령을 통해서 docker의 proxy 값을 확인할 수 있습니다.

```bash
sudo systemctl show --property Environment docker
```

`Environment=HTTP_PROXY=http://proxy.example.com:80"` 과 같이 나타나는지 확인합니다.

이후 Docker가 Proxy 설정을 통해 외부 네트워크와 원활하게 소통하는지 확인합니다.

## 맺음말

오늘은 Docker에서 Proxy 설정하는 방법과 프록시를 설정하지 않았을 때 발생할 수 있는 에러 케이스에 대해 알아보았습니다. 이 설정을 통해 Docker가 네트워크 제약 없이 다양한 리소스에 접근할 수 있게 되었기를 바랍니다. 
