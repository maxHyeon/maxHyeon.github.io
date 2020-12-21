---
layout: posts
title: "SSH PasswordLess 로그인"
date: 2019-12-16 12:00:00 -0400
categories: linux
---

1. SSH Passwordless 로그인을 위해선 SSH 공개키 배포가 필요하다.
    - 테스트 OS RHEL7
    - 서버 구성 sshServer - sshClient 
    - sshServer -> sshClinet로 ssh 비밀번호 없어 접속

2. 절차
    1. ssh keygen 
```bash
[sshServer]$ ssh-keygen -t rsa
# 아래에 나오는 추가 설정들은 알맞게 해 주자, 따로 요구사항이 없다면 그냥 enter해도 무방
# 꼭 permission 을 설정해주자
# user명에 맞는 chown도 필수
[sshServer]$ chmod 700 ~/.ssh
[sshServer]$ chmod 600 ~/.ssh/id_rsa
[sshServer]$ chmod 600 ~/.ssh/id_rsa.pub  
[sshServer]$ chmod 600 ~/.ssh/authorized_keys
[sshServer]$ chmod 600 ~/.ssh/known_host
```
    2. ssh copy
```bash
#옵션1
[sshServer]$ ssh-copy-id -i ~/.ssh/id_rsa.pub userid@sshServer
#옵션2
#SCP 혹은 직접 copy 를 통해 sshClient쪽에 authorized_keys를 추가해준다.
[sshClient]$ vi ~/.ssh/authorized_keys
```
    3. ssh Client쪽 permission 설정

```bash
# 꼭 permission 을 설정해주자
# user명에 맞는 chown도 필수
[sshClient]$ chmod 700 ~/.ssh
[sshClient]$ chmod 600 ~/.ssh/authorized_keys
```

    4. ssh test
```bash
#옵션1
[sshServer]$ ssh userid@sshclinet
```  
