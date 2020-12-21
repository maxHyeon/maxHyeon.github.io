---
layout: post
title: "Linux(RHEL) raw-device 생성"
date: 2019-12-26 12:00:00 -0400
categories: Linux, redhat, raw-device, raw, oracle
sitemap :
  changefreq : daily
---

1. raw-device 
    - OS의 캐싱없이 DB나 다른 시스템에서 직접 캐싱을 사용하려고 할 때 쓰인다.

2. 생성방법 (RHEL 7 버전 기준. RHEL 6에서도 되는것으로 보인다.)
    - LUN에서 바로 할당 할 수도 있지만. LV까지 생성 이후 Raw-device로도 변환 가능하기에 LV 생성하는 방법으로 진행한다.
    
    1. 파티션 생성, pv, vg, lv 생성
```bash
[rhelServer]$ parted 혹은 fdisk 이용하여 partion 생성
[rhelServer]$ pvcreate /dev/sdb1
[rhelServer]$ vgcreate rawvg /dev/sdb1
[rhelServer]$ lvcreate -n lv-raw01 -L 100G rawvg
```
      2. 단순 1개 raw 생성할 때
```bash
[rhelServer]$ raw /dev/raw/raw1 /dev/rawvg/lv-raw01
#재부팅시 적용을 원하면 스크립트 rc.loacl에 등록 진행
```
    3. 여러 raw 생성시 
```bash
[rhelServer]$ vi /etc/udev/rules.d/60-raw.rules
#아래의 사항 등록
# ==, != 등의 조건을 만족하면 뒤의 할당문을 실행시키게 된다. 
#예를들면 아래의 구문은 udevadm trigger --type=devices --action=change action 부분이 
# add 혹은 change 가 아니면 raw_end로 이동시킨다.

ACTION!="add|change", GOTO="raw_end" 

# Create raw character device mapping: 
# vg_name, lvname 이 조건에 맞을때 뒤의 run을 할당한다. 
ENV{DM_VG_NAME}=="rawvg", ENV{DM_LV_NAME}=="lv-raw01", RUN+="/bin/raw /dev/raw/raw1 %N"
ENV{DM_VG_NAME}=="rawvg", ENV{DM_LV_NAME}=="lv-raw02", RUN+="/bin/raw /dev/raw/raw2 %N"

# To set permissions: 
KERNEL=="raw*", OWNER:="username", GROUP:="groupname", MODE:="0660"

LABEL="raw_end"

```

