---
title: pyenv를 활용한 Python 개발 환경 구성 완벽 가이드
date: 2024-02-13 17:10:00 +09:00
categories: [S/W]
tags: [Linux, Mac, pyenv, python]
---

Python은 그 자체로도 강력하지만, 그 힘은 다양한 라이브러리와 프레임워크에서 비롯됩니다. 하지만 이러한 강점이 때로는 버전 호환성 문제를 야기할 수 있습니다. 예를 들어, 한 프로젝트는 Python 2를 요구할 수 있으며, 다른 하나는 Python 3.8 이상을 요구할 수 있습니다. 이런 상황에서 개발자가 두 프로젝트를 동시에 유지하려면 어떻게 해야 할까요?

여기서 Python 개발 환경의 구성이 중요한 역할을 합니다. 개발 환경을 적절히 구성하면, 다양한 프로젝트 요구사항을 충족시키면서도, 시스템 전체의 안정성을 유지할 수 있습니다. 이 과정에서 pyenv가 등장합니다. pyenv는 Python 버전 관리 시스템으로, 개발자가 시스템 전체, 특정 디렉터리, 혹은 단일 프로젝트에 대해 다양한 Python 버전을 쉽게 설치하고 관리할 수 있게 해줍니다.

pyenv의 사용은 단순히 버전 관리에 그치지 않습니다. 이 도구는 여러 버전의 Python을 동시에 설치하고, 필요에 따라 특정 버전을 전역적으로 사용하거나, 특정 프로젝트에만 특정 버전을 지정하는 등의 유연한 환경 설정을 가능하게 합니다. 이는 개발자가 다양한 프로젝트를 더욱 효율적으로 관리할 수 있게 해주며, 개발 환경을 깔끔하게 유지하면서도 프로젝트의 요구사항을 정확히 충족시킬 수 있는 방법을 제공합니다.

결론적으로, pyenv는 Python 개발자의 필수 도구 중 하나로 자리 잡았습니다. 이 도구를 통해 개발자들은 Python 버전 관리의 복잡성을 줄이고, 다양한 프로젝트를 더욱 원활하게 진행할 수 있습니다. 본문에서는 pyenv의 설치 방법, 사용법, 그리고 이 도구가 개발 환경에 미치는 긍정적인 영향에 대해 더 자세히 살펴보겠습니다.

## pyenv 설치하기

이 섹션에서는 pyenv를 설치하는 방법을 살펴보겠습니다. pyenv를 설치하는 과정은 사용하는 운영 체제에 따라 다를 수 있습니다. 여기서는 가장 일반적인 Linux와 macOS 시스템에서의 설치 방법을 안내합니다.

### Linux에서 설치

Linux 사용자의 경우, `pyenv-installer`를 사용하여 설치할 수 있습니다. 

```bash
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```

이 명령어는 `pyenv`뿐만 아니라, 관련한 아래 패키지도 함께 설치됩니다.

- pyenv-doctor
- pyenv-pip-rehash
- pyenv-update
- pyenv-virtualenv
- pyenv-which-ext

### macOS에서 설치

macOS 사용자는 `Homebrew`를 사용하여 pyenv를 설치할 수 있습니다. 

```bash
brew update
brew install pyenv
```
Homebrew는 macOS에서 소프트웨어 패키지를 관리하는 편리한 도구입니다. 자세한 내용은 [`Homebrew` 설치 및 사용법](https://minokim82.github.io/posts/homebrew-basic/) 글에서 확인해주세요.

### 환경 변수 설정

설치가 완료되면, pyenv를 시스템의 환경 변수에 추가해야 합니다. 이를 위해 사용자의 쉘 설정 파일 (예: .bashrc, .zshrc 등)을 편집해야 합니다.

`.bashrc` 나 `.zshrc` 파일을 열고 다음 라인을 파일의 끝에 추가하세요:

```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init --path)"
```

이 설정은 새 터미널 세션에서 pyenv를 사용할 수 있게 해줍니다. 변경 사항을 적용하려면 터미널을 재시작하거나, 해당 쉘 설정 파일을 소스로 로드해야 합니다. 예를 들어, bash 사용자는 source ~/.bashrc 명령어를 사용할 수 있습니다.

## pyenv 사용하기

 pyenv의 주요 서브 명령어와 그 사용법에 대한 예시를 소개합니다.

### python 설치 및 제거하기

`install`, `uninstall` 명령을 통해서 특정 버전의 python을 설치 및 제거할 수 있습니다.

```bash
pyenv install 3.9.1 # python 3.9.1 설치하기
pyenv uninstall 3.9.1 #python 3.9.1 제거하기
```

### 설치된 python version 확인하기

`version`, `versions` 명령을 통해서 설치된 python의 버전을 확인할 수 있습니다. `version`은 현재 사용되는 python 버전을 확인할 수 있고, `versions`는 설치된 모든 버전을 확인할 수 있습니다.

```bash
$ pyenv version
3.10.8 (set by /home/root/.pyenv/version)
$ pyenv versions
   system
   2.7.18
   3.8.15
* 3.10.8 (set by /home/root/.pyenv/version)
```

### python version 정의하기

설치된 여러 버전의 python에 대해서 어떤 버전을 사용할 지 정의할 수 있습니다. `global` 명령의 시스템 전체에서 사용할 기본 Python 버전을 설정합니다. `local`은 재 디렉터리(프로젝트)에 대한 Python 버전을 설정합니다. 이 설정은 해당 디렉터리 내에서만 유효합니다.

```bash
$ pyenv global 3.9.1 # default  버전을 3.9.1로 설정
$ pyenv local 3.8.5 #현재 디렉터리의 python 버전을 3.8.5  로 설정
```

잠시 다른 버전으로 테스트하거나 호환성 테스트를 위해서는 다른 버전으로 변경할 필요가 있는데요, 이때 `shell`명령을 통해서 현재 쉘 세션에 대한 Python 버전을 임시로 설정할 수 있습니다. 이 설정은 새 터미널 창이나 쉘 세션을 시작할 때마다 초기화됩니다.

```bash
$ pyenv shell 3.7.4 #현재 쉘 세션에서 Python 3.7.4 버전을 사용하도록 설정
```

## pyenv의 장점

pyenv는 Python 개발 환경을 관리하는 데 있어 매우 강력하고 유연한 도구입니다. 여러 버전의 Python을 쉽게 설치, 관리, 전환할 수 있는 능력을 제공함으로써, 개발자들이 프로젝트별로 다양한 요구사항을 충족시키는 데 필수적인 역할을 합니다. pyenv의 주요 장점은 다음과 같습니다:

- 다양한 버전 관리: 하나의 시스템에서 여러 버전의 Python을 쉽게 설치하고 관리할 수 있습니다.
- 유연한 환경 설정: 전역적 또는 프로젝트별로 다른 Python 버전을 설정할 수 있는 유연성을 제공합니다.
- 간편한 버전 전환: 설치된 Python 버전 간에 쉽게 전환할 수 있어, 다양한 라이브러리와의 호환성을 테스트하기에 이상적입니다.
- 쉬운 업그레이드와 관리: pyenv를 통해 설치된 Python 버전은 쉽게 업그레이드하고 관리할 수 있습니다.

pyenv를 통해 개발자들은 프로젝트의 요구 사항을 정확히 충족하는 동시에, 개발 환경을 깔끔하게 유지할 수 있습니다. 이는 Python 개발 경험을 크게 향상시키는 핵심 요소입니다.

## pyenv 관련 참고 자료 사이트

- [pyenv 공식 문서](https://github.com/pyenv/pyenv): 설치 및 사용 방법, 명령어 목록, FAQ 등 제공
- [pyenv-installer](https://github.com/pyenv/pyenv-installer): Linux 설치 스크립트 제공
