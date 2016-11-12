---
layout: post
title: pip 버전 9.0.1로 업그레이드 하기
tags:
  - pip
---
최근 macOS를 10.12.1로 업데이트 한 이후로 `pip list` 명령을 실행할 때마다 아래와 같은 경고 메시지가 뜨기 시작했다.

```shell
$ pip list
pip (8.1.2)
setuptools (23.1.0)
wheel (0.29.0)
You are using pip version 8.1.2, however version 9.0.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```

귀찮아서 계속 무시하다가 주말에 pip을 9.0.1 버전으로 업그레이드 했다.

```
$ pip install --upgrade pip
Collecting pip
  Using cached pip-9.0.1-py2.py3-none-any.whl
Installing collected packages: pip
  Found existing installation: pip 8.1.2
    Uninstalling pip-8.1.2:
      Successfully uninstalled pip-8.1.2
Successfully installed pip-9.0.1
```

pip 9.0.1 버전으로 업그레이드 하고 `pip list`를 실행했더니 앞으로 기본 출력 방식이 columns 포맷으로 변경될 거라는 경고 메시지가 나온다.

```shell
(venv) $ pip list
DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
pip (9.0.1)
setuptools (28.8.0)
wheel (0.30.0a0)
```

`--format=columns` 옵션을 사용했더니 pip 목록이 출력되는 방식이 달라진 모습을 확인할 수 있었다.

```shell
(venv) $ pip list --format=columns
Package    Version
---------- --------
pip        9.0.1
setuptools 28.8.0
wheel      0.30.0a0
```

매번 `--format=columns` 옵션을 지정하지 않아도 되도록 `${HOME}/.pip/pip.conf` 파일에 다음과 같이 list 옵션을 지정했다.

```
# ~/.pip/pip.conf
[list]
format = columns
```

이제 매번 `--format=columns`옵션을 지정하지 않아도 columns 형식으로 표시되는 것을 확인할 수 있었다.

```shell
(venv) $ pip list
Package    Version
---------- --------
pip        9.0.1
setuptools 28.8.0
wheel      0.30.0a0
```
