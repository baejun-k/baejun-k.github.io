---
title: "[console] Windows 10 encoding utf-8 기본 설정하기"
date: 2019-04-01
excerpt: "윈도우 콘솔창 codepage 인코딩을 utf-8로 바꾸기"
categories:
  - settings
tags:
  - console
  - utf8
  - utf-8
  - windows
  - 인코딩
  - encoding
---  
  
1. regedit 실행  
2. [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor\Autorun]으로 이동  
***없다면 [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor]에서 "Autorun"을 문자열 값으로 생성***  
  
3. "Autorun"의 값을 "chcp 65001" 로 수정  
