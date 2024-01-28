---
title: Mac OS에서 rbenv로 ruby 설치하기
date: 2024-01-28 22:39:00 +09:00
categories: [S/W]
tags: [Mac, ruby, jekyll]
---

Mac OS에는 기본적으로 `ruby`가 설치되어 있습니다. 하지만, 설치된 버전이 낮아 Version update가 필요합니다. 또, 프로젝트 별로 서로 다른 `ruby` 버전을 사용하는 경우 가 있어, 여러 버전의 `ruby`를 설치 할 수 있도록 `rbenv`를 사용하겟습니다.

## `rbenv` 설치

Mac OS에서 `rbenv`의 설치는 `Homebrew`를 사용합니다.


```bash
brew install rbenv
```

설치가 완료되면 환경 설정 파일에 `rbenv init`을 추가하여 `rbenv`가 자동으로 초기화되도록 합니다.

```bash
[[ -d ~/.rbenv  ]] && \
  export PATH=${HOME}/.rbenv/bin:${PATH} && \
  eval "$(rbenv init -)"
```
위 내용을 `~/.zshrc` 파일에 추가하고, 터미널을 다시 실행해줍니다.

## ruby 설치하기 

이제 `rbenv`에 필요한 버전의 `ruby`를 설치하도록 하겠습니다.

### 지원 가능한 버전 확인하기

```bash
rbenv install -l
```

명령을 입력하면 아래와 같이 다운로드 가능한 `ruby` 버전을 모두 확인 할 수 있습니다.

```plaintext
3.0.6
3.1.4
3.2.3
3.3.0
jruby-9.4.5.0
mruby-3.2.0
picoruby-3.0.0
truffleruby-23.1.2
truffleruby+graalvm-23.1.2

Only latest stable releases for each Ruby implementation are shown.
Use `rbenv install --list-all' to show all local versions.
```

### 특정 버전 설치하기 

버전을 확인하였으면, 해당 버전을 설치해 보도록 하겟습니다. `rbenv install <version>`으로 원하는 버전을 설치 후 `rbenv rehash`로 `ruby`를 재실행합니다.

```sh
rbenv install 3.2.3 
rbenv rehash         
```

### 버전 정의하기

여러 개의 `ruby` 버전을 설치하는 경우, 어떤 버전의 `ruby`가 동작할 지 정의 할 수 있습니다.

언제 어디서나 사용하는 default 버전의 경우 `rbenv global <version>`으로 지정하고, 특정 폴더에서 사용을 원하는 경우에는 해당 폴더에서 `rbenv local <version>`으로 지정합니다.

```sh
$ rbenv global 3.2.3 
$ rbenv local 3.2.3 
```

### 버전 확인하기 

현재 설치된 모든 버전을 확인하는 방법은 아래 명령을 입력합니다.

```sh
rbenv versions
```

### 설치된 버전 삭제하기

```sh
rbenv uninstall 3.2.3
```

## rbenv 제거

설치와 마찬가지로 `Homebrew`를 이용하여 삭제합니다.

```bash
brew uninstall rbenv
```

`.zshrc` 파일에 추가한 내용도 삭제해 주시면 됩니다.

## 참고
- [ruby 홈페이지](https://github.com/rbenv/rbenv)