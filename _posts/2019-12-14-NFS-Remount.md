---
title: "Stale NFS file handle / NFS 클라이언트쪽에서 NFS 문제"
date: 2019-12-14 12:00:00 -0400
categories: RHEL, Linux, NFS
---

1. Stale NFS file hande 발생 원인
  - 여려 원인이 있겠지만, 먼저 NFS 서버 상태를 체크 해보자, NFS서버가 예기치 않게 종료 되었을 때도 발생할 수 있다.

2. Trouble Shoot
    1. nfs 서버에서 상태 확인
```bash
[nfsserver]$ showmount -e
[nfsclinet]$ showmount -e 123.123.123.123 (nfsserver)
```
    3. NFS 서버 상태 정상일 경우 다시 NFS를 맺는다. 
```bash
[nfsclient]$ umount /mount/point
#만약 umount 가 안될경우
[nfsclinet]$ umount -f /mount/point
#다시 NFS 맺기
#fstab 작성이 되어있다면
[nfsclinet]$ mount -a 
#아니라면
[nfsclinet]$ mount -t nfs -o sync 123.123.123.123:/mtpnt /mount/point
```

