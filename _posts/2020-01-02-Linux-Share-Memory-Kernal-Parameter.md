---
title: "리눅스 커널 파라메터 설정 (For Oracle)"
date: 2020-01-02 12:00:00 -0400
categories: Linux, kernal, oracle, parameter, share, memory
---

1. Linux 에서 각종 Subprogram 을 위한 Kernel parameter 설정 방법
    - 파라메터 설정값들은 특성에 맞추어 설정 하여야 한다. 

2. 설정 방법
    - sysctl 명령 이용 (ex $ sysctl -w kernel.shmall=1111 ) 
    - /etc/sysctl.conf 파일 변경 (재기동시에도 반영된다.)

3. 공유 메모리 및 세마포어 설정
    - 현 설정값 확인 - ipcs -l
    - /etc/systctl.conf 
    - DB등에서 필요한 shared buffer 를 위한 설정
    - 리눅스 시스템에서 프로세스간에 공유를 하기 위한 메모리
```bash
[Server]# vi /etc/sysctl.conf
### /etc/systel.conf 파일 작성
# 공유 메모리 설정  

kernel.shmmni=4096
#공유 메모리 세그먼트의 최대 크기 ( byte 단위 ) 
# 물리 메모리 크기 혹은 물리 메모리 1/2 로 한다 각 db 별 가이드를 참조 하도록 하자 
# 아래는 16GB 메모리와 동일하게 설정한것이다. 16 * 1024 * 1024 * 1024 
kernel.shmmax=17179869184
#공유 메모리의 최대 총합 ( page 단위 )
# shmmax / 4096 (page size) ( 페이지 크기 확인을 위한 명령어 [svr]# getconf PAGESIZE )
kernel.shmall=8388608

# 세마포어 설정 
# semmsl semmni semmns semopm
# semmsl : 세마포어 set 당 최대 개수 
# semmni : 전체 세마포어 set 개수
# semns : 전체 세마포어 최대 개수
# semopm : system call 별로 수해오딜수 잇는 작업의 수
##      semmsl semmni semmns semopm
kernel.sem=250 32000 32 128

[Server]# sysctl -p  ( 설정된 설정값 로드 ) 
[Server]# ipcs -l (공유 메모리 설정값 확인)
[Server]# ipcs -a (공유 메모리 현재 사용량 확인)

```
4. File IO 설정
```bash
[Server]# vi /etc/sysctl.conf
### /etc/systel.conf 파일 작성
# 최대 open 가능 file 갯수
fs.file-max=65536
# asynchronum non-blocking io request 갯수
fs.aio-max-nr=1048576

```
5. net 설정
아래의 링크에 정리 잘 되어 있다. 
https://meetup.toast.com/posts/53

