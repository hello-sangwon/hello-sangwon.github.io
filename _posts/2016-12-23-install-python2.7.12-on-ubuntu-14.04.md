---
layout: post
title: ubuntu 14.04에 python 2.7.12 설치하기
tags:
  - ubuntu 14.04
  - python 2.7.12
  - 파이썬
---
ubuntu 14.04에서 apt-get을 이용하여 설치할 수 있는 가장 최근 버전의 파이썬2는 2.7.6이다.
이 글에서는 우분투의 파이썬 기본 버전을 덮어쓰지 않고, 가상 환경에서 파이써 2.7.12를 사용하는 방법에 대해서 설명한다.

왜 아직도 파이썬2를 사용하고 있냐고 묻는다면 딱히 할 말은 없다.
굳이 변명하자면 AWS 람다는 아직 파이썬3를 지원하지 않는다.
(마침 오늘 파이썬 3.6.0 이 배포되었다.)

---

# 설치하기
0\. utbutu 14.04 환경인지 확인

```
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 14.04.5 LTS
Release:        14.04
Codename:       trusty
```

1\. virtualenv 업데이트

```shell
$ sudo pip install -U virtualenv
```

2\. openssl 설치

```
$ sudo apt-get install openssl libssl-dev
```

3\. python 2.7.12 다운로드

```
$ wget https://www.python.org/ftp/python/2.7.12/Python-2.7.12.tgz
$ tar xvzf Python-2.7.12.tgz
$ cd Python-2.7.12
```

4\. SSL 지원 설정

* Modules/Setup.dist 파일(219-221줄)에서 주석 처리 제외한다.

```
# Socket module helper for SSL support; you must comment out the other
# socket line above, and possibly edit the SSL variable:
#SSL=/usr/local/ssl
_ssl _ssl.c \
    -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
    -L$(SSL)/lib -lssl -lcrypto
```

5\. 빌드 및 설치

```
$ ./configure --prefix=/usr/local/lib/python2.7.12 --enable-ipv6
$ make
$ sudo make install
```

6\. 가상 환경 설치

```
$ virtualenv --python=/usr/local/lib/python2.7.12/bin/python venv
```

7\. python 2.7.12 실행 확인

```
$ source venv/bin/activate
(venv) $ python
Python 2.7.12 (default, Dec 23 2016, 15:41:12) 
[GCC 4.8.4] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import ssl
>>> 
```

# 참고 자료
* https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
* http://mbless.de/blog/2016/01/09/upgrade-to-python-2711-on-ubuntu-1404-lts.html
* https://techglimpse.com/install-python-openssl-support-tutorial/
* http://askubuntu.com/questions/429385/upgrade-openssl-on-ubuntu-12-04
