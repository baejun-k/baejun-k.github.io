---
title: "[git] git 과거 commit으로 갔다 돌아오기"
date: 2019-04-01
excerpt: "git 과거 commit으로 갔다 돌아오기"
categories:
  - git
tags:
  - git
  - commit
  - checkout
  - log
---  

##  
* 과거의 commit으로 이동  
  
특정 수 만큼 과거로 가고싶을 때(바로 전이나 비교적 최근을 갈 때 편리)  
HEAD~n 으로 입력

``` console
# 1 단계 전
$ git checkout HEAD~1
# 10 단계 전
$ git checkout HEAD~10
```  

##  
* 특정 commit으로 이동  
  
commit 메시지를 확인하고 해당 커밋으로 이동 할 때
해당 commit의 해시코드를 입력해주는데 앞의 6글자만 입력해도 됨.
  
``` console
# commit 확인
$ git log

# commit 34ef2da9bfa95b80a17ae642ece0b2d66bddacf0 (HEAD -> master, origin/master)
# Author: baejun_k <abc@email.com>
# Date:   Sat Jul 13 02:15:51 2019 +0900
# 
#     commit2
# 
# commit 4d2af63d50837a4247aec2279ac1e16fe77242f6
# Author: baejun_k <abc@email.com>
# Date:   Sat Jul 13 02:15:48 2019 +0900
# 
#     commit1

$ git checkout 4d2af6
```  

##  
* 과거 commit 확인 후 다시 돌아가기
  
``` console
$ git checkout master
``` 
