---
title: "[linux] 폴더 구조만 복사하기"
date: 2019-10-22
excerpt: "리눅스에서 폴더 구조만 복사하는 명령어"
categories:
  - linux
  - command
tags:
  - linux
  - command
  - cp
  - find
---  


현재 폴더의 구조를 'dir'아래에 복사하는 명령어.

``` console
find ./ -type d -exec mkdir -p dir/{} \;
```
