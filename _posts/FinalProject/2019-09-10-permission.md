---
layout: post
title: " RasPI Static IP , Docker Server Sync, npm install 권한 문제"
date: 2019-09-10
excerpt: "3_week_Mon"
tag:
- BlockChain
- RaspberryPI
- Embeded
category: [FinalProject]
comments: true

---

## RasPI Static IP 설정 방법

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/rasp_static1.PNG" width = "70%" />

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/rasp_static3.PNG" width = "70%" />

 <span style="color:red;font-size:20pt">sudo netplan apply</span>  하면 static IP 설정 완료.




## Docker Swarm join에서 RootCA 문제발생 

### 중요) RasPI와 Desktop의 서버시간을 같게 해주어야 한다

####우분투 리눅스 타임존 설정

1. sudo timedatectl set-timezone 'GMT'
2. sudo timedatectl set-ntp 0   -> 우분투 시간 동기화 해제
3. sudo date -s "12:38:45"   -> 시간 수동 설정(라즈베리파이와 맞추기)


## npm install 설치문제

1. 같은 상위 디렉터리 내에 npm 모듈이 A 하위 디렉터리에서 사용되고 있을 경우 B 하위 디렉터리에서는 npm install이 깔리지 않는다
  - git push로 다 옮기면 node_modules는 자동으로 git ignore되어 다시 pull하고 설치하면 된다.

2. npm install 할때 permission denied 에러 뜰경우 --unsafe-perm 플래그를 붙여서 설치한다