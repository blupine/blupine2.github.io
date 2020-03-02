---
layout: post
title:  "4. flag"
subtitle: ""
date:   2018-09-21 18:35:13 +0900
categories: security
tags : pwnable
---

![1]({{"/assets/img/security/pwnable/4/1.png" | absolute_url}})

패킹된(packed) 선물을 줬단다.

패킹된 바이너리를 던져주고 플래그를 찾는 것 같다.

링크로 접속하여 바이너리를 다운받고 hex editor로 열어봤다.


![2]({{"/assets/img/security/pwnable/4/2.png" | absolute_url}})

`elf` 형식의 리눅스 실행 파일이고 UPX 패킹된 것을 확인할 수 있었다.

리눅스 환경에서 언패킹을 진행한다.


![3]({{"/assets/img/security/pwnable/4/3.png" | absolute_url}})

`elf` 형식 언패킹을 해본적이 없어서 검색해보니 위와 같이 `upx -d` 명령어로 간단하게 언패킹이 가능했다.

언패킹 된 바이너리를 디버거에 올려봤다.


![4]({{"/assets/img/security/pwnable/4/4.png" | absolute_url}})

바로 flag를 참조하는 명령어 발견...


![5]({{"/assets/img/security/pwnable/4/5.png" | absolute_url}})
