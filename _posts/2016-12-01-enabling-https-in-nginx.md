---
layout: post
title: Nginx 웹서버에 HTTPS 설정하기
tags:
  - nginx
  - https
  - certificate
  - 인증서
  - letsencrypt
---
이 글은 ubuntu 16.04(xenial) 환경에서 webroot 플러그인을 사용하여 인증서를 설치하는 상황을 가정한다.
우분투 버전을 확인하기 위해서는 아래 명령을 쉘에서 실행한다.

```
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 16.04.1 LTS
Release:        16.04
Codename:       xenial
```

# Let's Encrypt
사용자의 웹브라우저와 웹서버 사이에 암호화된 보안 통신인 HTTPS를 사용하기 위해서는 디지털 인증서가 필요하다.
[Let's Encrypt](https://letsencrypt.org)는 인증서를 무료로 발급해주는 인증 기관(CA) 서비스이다.

# 인증서 설치하기
* 먼저 let's encrypt의 certbot 패키지를 설치한다.

    ```
    $ sudo apt-get install letsencrypt
    ```

* letsencrypt 프로그램을 실행하여 인증서를 설치한다.
`example.com`은 각자 도메인에 맞게 변경한다.

    ```
    $ sudo letsencrypt certonly --webroot -w /var/www/html -d example.com
    ```

    * 명령이 성공적으로 실행되면 `/etc/letsencrypt/live/example.com` 디렉토리에 인증서 파일을 가리키는 심볼릭 링크들이 생성된다.
        * cert.pem
        * chain.pem
        * fullchain.pem
        * privkey.pem

# nginx 설정하기
* /etc/nginx/sites-available/default 파일을 변경한다.

    ```
    $ sudo vi /etc/nginx/sites-available/default
    ```

* server 블럭에서 80 포트에 관한 설정을 제거한다. 기본 설정에서 아래 두 줄을 삭제한다.

```conf
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;
```

* server 블럭에 SSL 지원하는 443 포트를 설정한다. `example.com`은 각자 도메인에 맞게 변경한다.

```conf
    listen 443 ssl;

    server_name example.com www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
```

* Diffie-Hellman 키 교환에 사용되는 기본 OpenSSL 키 길이는 1024 비트이다.
보안을 강화하기 위해서, 2048 비트 Diffie-Hellman 그룹을 생성한다.

    ```
    $ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
    ```

* 앞에서 생성한 Diffie-Hellman 그룹을 사용하도록 설정한다. 아래의 설정을 server 블럭에 추가한다.

```conf
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
    ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_stapling on;
    ssl_stapling_verify on;
    add_header Strict-Transport-Security max-age=15768000;
```

* HTTP 요청을 HTTPS로 리다이렉트 하도록 별도의 server 블럭을 설정한다.

```conf
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```

# nginx 재시작하기
* 변경된 nginx 설정 파일에 문제가 없는지 확인한다.

```
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

* 설정 파일에 이상이 없으면 nginx를 재시작한다.

```
$ sudo service nginx restart
```

* 이제 웹브라우저에서 https://example.com 으로 접속할 수 있다.

* Qualys SSL Labs 웹페이지에서 서버가 얼마나 잘 설정 되었는지 확인해 볼 수 있다.

```
https://www.ssllabs.com/ssltest/analyze.html?d=example.com
```

# 인증서 갱신 자동화
Let's Encrypt 인증서는 유효기간이 90일로 비교적 짧다. 따라서 자동으로 갱신되도록 설정하는 것이 좋다.

* 인증서 갱신이 잘 동작하는지 테스트를 먼저 실행해 본다.

    ```
    $ sudo letsencrypt renew --dry-run --agree-tos
    ```
* 테스트 결과에 이상이 없다면, 인증서 갱신 명령을 cron 작업으로 등록해서 주기적으로 실행되도록 한다.

    ```
    $ sudo crontab -e
    ```

```
# crontab entry
# 매주 월요일 4:37am 인증서를 갱신하고, 4:42am nginx 로드를 다시 한다.
37 4 * * 1 /usr/bin/letsencrypt renew
42 4 * * 1 service nginx reload
```

# 참고 자료
* [Let's Encrypt](https://letsencrypt.org)
* [Deploying Let's Encrypt certificates with certbot](https://certbot.eff.org/#ubuntuxenial-nginx)
* [How To Secure Nginx with Let's Encrypt on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04)
