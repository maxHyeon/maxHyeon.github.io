---
title: "Powershell GET-ACL 폴더 권한 조회"
date: 2019-12-17 12:00:00 -0400
categories: Window, NT, Powershell, GET-ACL, ACL
---

1. 폴더 권한 가져오는 Powershell 함수 GET-ACL 사용 예제

2. 예
  - 기본 사용   
```bash
[PS1]$ get-acl -path c:\path
```
  - 특정 DIR 아래의 하위 폴더들 권한 조회 
```bash
[PS1]$ get-childitem -path c:\path | foreach-object {get-acl -path $_.fullname}
```
  - Output 에 hostname 추가 
```bash
[PS1]$ get-childitem -path c:\path | foreach-object {get-acl -path $_.fullname} > c:\path\$env:computername"_acl_info.txt"
```
  - BAT 로 ps 호출시   
```bash
[cmd]$ powershell.exe -noprofile -executionpolicy bypass -file c:\file\get-acl.ps1
```
