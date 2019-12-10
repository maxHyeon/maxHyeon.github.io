---
title: "Parted 사용법"
date: 2019-12-10 12:00:00 -0400
categories: Linux, Parted
---

1. Parted 명령어
 - 2TB 이상부터는 Fdisk 파티션 분할이 어렵다. Parted를 사용하자

2. 기초 사용 
```bash
# parted /dev/sdb
(parted) mklabel gpt (parted 에서는 mbr이 아닌 gpt를 사용하여야 한다)
(parted) mkpart primary [start] [end] 
#unit 값을 변경 할 수 있다.
(parted) unit GB 
ex) 
(parted) mkpart primary 0 100GB (파티션 1 생성) 
(parted) mkpart primary 100GB 100% (파티션 2 생성)

(parted) print
(parted) q 

mkfs.ext4 /dev/sdb1 
```

3. gpt, mbr 차이점 및 ext 파일시스템 별 차이점
todo..추가하기
 1. gpt
 2. mbr
 3. ext1~4 
 
4. 심화 parted
todo..추가하기 (extension 등) 

5. parted 이후 vg, pv, lv 작업 
```bash
(parted) set 1 lvm on (lvm 활성화)
(parted) q 

pvcreate /dev/sdb1
vgcreate TTvg /dev/sdb1 
lvcreate -n ttLV -L 100G TTvg
vi /etc/fstab (fstab 작성)
mount -a

```
