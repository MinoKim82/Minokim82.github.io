---
title: MacOS 패키지관리도구 Homebrew 설치 및 사용법
date: 2024-02-01 13:44:00 +09:00
categories: [S/W]
tags: [Mac]
---

## Homebrew 란?
![Homebrew](https://brew.sh/assets/img/homebrew.svg)

Homebrew는 맥OS 환경에서 사용할 수 있는 패키지 관리 도구로서, 개발자들에게 매우 유용한 도구로 인식되고 있습니다. Homebrew를 사용하면 터미널에서 간단하게 명령을 입력해 다양한 소프트웨어를 쉽게 설치하고 업데이트할 수 있습니다.

Homebrew는 루비(Ruby)로 개발되어 있으며, 2009년에 처음 공개되었습니다. 이후 지속적인 업데이트를 거쳐 현재는 애플 실리콘을 지원하는 버전까지 출시되었습니다. 또한, Homebrew는 "캐스크"라는 기능을 제공하는데, 이를 사용하면 그래픽 유저 인터페이스(GUI) 기반의 애플리케이션도 쉽게 설치할 수 있습니다.

개발자들 사이에서 Homebrew가 인기를 끄는 이유 중 하나는 리눅스의 패키지 관리 도구들과 사용법이 비슷하다는 점입니다. 이로 인해 맥OS 환경에서 개발하는 개발자들이 쉽게 Homebrew를 사용할 수 있습니다. 또한, Homebrew는 커맨드라인 도구나 시스템 패키지뿐만 아니라, GUI 애플리케이션 설치에도 사용할 수 있어 범용성이 뛰어납니다.

하지만, Homebrew는 커뮤니티에 의해 운영되는 오픈소스 프로젝트이므로, 패키지의 안정성은 보장되지 않을 수 있습니다. 따라서, 패키지를 설치하기 전에는 충분한 검토를 거쳐야 하며, 필요에 따라서는 다른 패키지 관리 도구를 함께 사용하는 것도 고려해봐야 합니다.

더 자세한 내용은 [Homebrew 공식 문서](https://docs.brew.sh/)에서 확인하실 수 있습니다.

## Homebrew 사용하기

맥OS에서는 터미널이나 iTerm2에서 설치하고 사용할 수 있습니다. 

### Homebrew 설치준비

Homebrew는 Xcode command line tool을 사용하고 있습니다. 따라서, Homebrew를 설치하기 전에 Xcode command line tool을 먼저 설치해야 합니다.

```sh
$ xcode-select --install
```

### Homebrew 설치하기

Homebrew 설치는 아주 간단합니다. 아래 명령어를 터미널에 입력하면 Homebrew가 설치됩니다.

```sh
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Homebrew의 명령어는 `brew`입니다. 설치가 완료되면 `brew --version` 명령을 통해서 Homebrew가 잘 설치되었는지 확인할 수 있습니다.

```sh
$ brew --version
Homebrew 4.2.5
```

### Homebrew 업데이트하기

Homebrew를 업데이트하기 위해서는 `brew update` 명령을 사용합니다.

```sh
$ brew update
```

### Homebrew 삭제하기

Homebrew를 삭제하기 위해서는 Homebrew에서 제공하는 uninstall 스크립트를 사용합니다.

```sh
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

## Homebrew에서 패키지 관리하기

### Homebrew 패키지 검색하기

Homebrew에서 패키지를 설치하고자 하면 해당 패키지가 Homebrew에 있는지 먼저 확인해야 합니다. Homebrew에서는 `brew search` 명령을 통해 패키지를 검색할 수 있습니다.

```sh
$ brew search vim
==> Formulae
avimetaedit     neovim ✔        neovim-remote   pacvim          vim             vimpc           vifm            vit             vis             nim
macvim          neovim-qt       nvimpager       pyvim           vimpager        zsh-vi-mode     viu             vip             zim

==> Casks
fvim              goneovim          kindavim          macvim            via               vimcal            vimediamanager    vimr              vym
```

일반 커맨드라인 패키지인 `Formulae`와 GUI 애플리케이션인 `Casks`로 구분되어 검색 결과가 출력됩니다.

제공되는 패키지 정보는 [Homebrew Formulae](https://formulae.brew.sh/formula/)에서도 확인할 수 있습니다.

### Homebrew 패키지 정보 확인하기

패키지를 설치하기 전에 해당 패키지의 정보를 먼저 확인하는 것이 좋습니다. Homebrew에서는 `brew info` 명령을 통해 패키지의 정보를 확인할 수 있습니다.

```sh
$ brew info vim
==> vim: stable 9.1.0050 (bottled), HEAD
Vi 'workalike' with many additional features
https://www.vim.org/
Conflicts with:
  ex-vi (because vim and ex-vi both install bin/ex and bin/view)
  macvim (because vim and macvim both install vi* binaries)
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/v/vim.rb
License: Vim
==> Dependencies
Required: gettext ✔, libsodium ✘, lua ✘, ncurses ✘, perl ✘, python@3.12 ✘, ruby ✘
==> Options
--HEAD
        Install HEAD version
==> Analytics
install: 35,094 (30 days), 88,183 (90 days), 292,729 (365 days)
install-on-request: 33,388 (30 days), 85,291 (90 days), 287,809 (365 days)
build-error: 11 (30 days)
```

### Homebrew 패키지 설치하기

패키지를 설치하기 위해서는 `brew install` 명령을 사용합니다.

```sh
$ brew install <PACKAGE_NAME>
```

설치가 완료되면 `brew list` 명령을 통해 설치된 패키지를 확인할 수 있습니다.

```sh
$ brew list pyenv
/opt/homebrew/Cellar/pyenv/2.3.24/bin/pyenv
/opt/homebrew/Cellar/pyenv/2.3.24/bin/pyenv-install
/opt/homebrew/Cellar/pyenv/2.3.24/bin/pyenv-uninstall
/opt/homebrew/Cellar/pyenv/2.3.24/bin/python-build
/opt/homebrew/Cellar/pyenv/2.3.24/COMMANDS.md
/opt/homebrew/Cellar/pyenv/2.3.24/completions/ (3 files)
/opt/homebrew/Cellar/pyenv/2.3.24/CONDUCT.md
/opt/homebrew/Cellar/pyenv/2.3.24/CONTRIBUTING.md
/opt/homebrew/Cellar/pyenv/2.3.24/Dockerfile
/opt/homebrew/Cellar/pyenv/2.3.24/libexec/ (27 files)
/opt/homebrew/Cellar/pyenv/2.3.24/MAINTENANCE.md
/opt/homebrew/Cellar/pyenv/2.3.24/Makefile
/opt/homebrew/Cellar/pyenv/2.3.24/plugins/python-build/ (1012 files)
/opt/homebrew/Cellar/pyenv/2.3.24/plugins/.gitignore
/opt/homebrew/Cellar/pyenv/2.3.24/pyenv.d/exec/ (4 files)
/opt/homebrew/Cellar/pyenv/2.3.24/pyenv.d/install/latest.bash
/opt/homebrew/Cellar/pyenv/2.3.24/pyenv.d/rehash/ (6 files)
/opt/homebrew/Cellar/pyenv/2.3.24/share/man/man1/pyenv.1
/opt/homebrew/Cellar/pyenv/2.3.24/src/ (7 files)
/opt/homebrew/Cellar/pyenv/2.3.24/terminal_output.png
/opt/homebrew/Cellar/pyenv/2.3.24/test/libexec/pyenv-echo
/opt/homebrew/Cellar/pyenv/2.3.24/test/ (27 files)
```

### Homebrew 패키지 업데이트하기

패키지를 업데이트하기 위해서는 `brew upgrade` 명령을 사용합니다.

```sh
$ brew upgrade <PACKAGE_NAME>
```

### Homebrew 패키지 삭제하기

패키지를 삭제할 때는 remove 서브 명령어를 사용합니다.

```sh
$ brew remove <PACKAGE_NAME>
```

### Homebrew cask 패키지 설치하기

GUI 애플리케이션인 cask 패키지를 설치할 때는 `brew cask install` 명령을 사용합니다.

```sh
brew cask install <PACKAGE_NAME>
```

설치할 경우에만, `cask` command를 사용하고 그 외의 경우에는 일반 패키지와 동일하 업데이트, 삭제를 합니다.
