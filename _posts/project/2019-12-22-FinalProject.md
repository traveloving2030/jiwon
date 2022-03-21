---
layout: post
title:  "[2019 학부 종합설계]임베디드 기반 하이퍼레저 패브릭을 활용한 출입관리시스템"
date:   2019-12-22
excerpt: "임베디드 기반 하이퍼레저 패브릭을 활용한 출입관리시스템"
project: true
tag:
- blockchain
- hyperledger
- Raspberry Pi
- Imbeded System
- NFC

comments: true
---


# 블록체인 기반 출입관리시스템

<img width="715" alt="contentMap" src="https://user-images.githubusercontent.com/45926066/159282661-f1210405-3026-4a13-a614-20ee6945c054.png">

- 문을 제어하는 라즈베리파이
- 관리자가 네트워크를 모니터링 할 수 있는 PC들로 구성
    - 네트워크는 하이퍼레저 패브릭 블록체인으로 구성
    - 각 라즈베리파이와 PC들은 하이퍼레저 패브릭을 구성하는 노드(Peer, Orderer)


## Getting started

> 임베디드 시스템과 블록체인을 활용하여 블록체인 모델을 만들 때 어떤 것이 좋을지 고민을 많이 하였습니다. <br>
가장 간단하게 실험할 수 있는 NFC 모듈과 라즈베리파이, 하이퍼레저 패브릭 블록체인을 결합하는 모델을 생각하다보니 **임베디드 기반의 출입관리시스템** 을 생각하게 되었습니다.
<br>[![Video Label](https://img.youtube.com/vi/ci2D25heTXE/0.jpg)](https://youtu.be/ci2D25heTXE) 


## Network Flow

<img width="715" alt="contentMap" src="https://user-images.githubusercontent.com/45926066/159282677-5d1e6e8b-81ff-43f2-8a97-98a39551df3d.png">

- 라즈베리파이들과 데스크탑, 모두 동등한 하나의 피어로서 네트워크에 참여하고 있습니다.

- 모든 피어들은 모두 같은 분산원장을 공유하고 있습니다.



## Application Workflow

<img width="715" alt="contentMap" src="https://user-images.githubusercontent.com/45926066/159282682-fea83789-1300-4741-9333-a0c46a71873b.png">

1. 게이트에 출입하고 싶은 사용자는 NFC 카드키를 단말에 접촉시킵니다.

2. 인터럽트가 들어오면 NFC 프로그램은 문을 열어주고 블록체인 네트워크에 해당 출입 로그를 저장하려고 합니다.

3. 하지만, 이 태그키가 애초에 등록이 되지 않은 태그 키이거나, 또는 가입 후 탈퇴한 태그 키 일 수 있습니다. 이럴 경우 문을 열어주면 안되됩니다.

4. 따라서 첫번째로, 이 태그 키의 주인이 출입 가능한 인물인지 쿼리를 진행합니다.

5. 먼저,Wallet을 통한 검사로 해당 NFC 키가 우리가 알고있는 키인지 확인합니다. 만약, 전혀 엉뚱한 키라면 이곳에서 차단받습니다.

6. 그 다음, 사용자가 유효한지 검사합니다.

 - 분산원장에서 NFC 키를 통해 쿼리를 하여 확인하는 것입니다. 탈퇴 된 회원은 이곳에서 차단받습니다.

 - 이에 비해, 아무 이상이 없다면 해당 사용자는 출입이 가능하다는 뜻이므로 문을 열어주게 되고, 현재의 출입 로그를 블록체인에 넣을 준비를 하게 됩니다.




