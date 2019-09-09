---
layout: post
title: "서브넷마스크"
date: 2019-09-09
excerpt: "서브넷 마스크 개념 정리"
tag:
- network
category: [NetWork]
comments: true
---



- - -

# [Network] 서브넷마스크(Subnet Mask)란?

## 1)서브네팅이란? (Subnetting)
- 서브넷마스크를 알기 위해선 먼저 서브네팅 개념을 이해해야합니다. 서브네팅은 "네트워크 관리자가 네트워크 성능을 향상시키기 위해, 자원을 효율적으로 분배하는 것입니다. 여기서 자원을 효율적으로 분배한다는 것은 네트워크 영역과 호스트 영역을 분할 하는 것이라고 생각하면 됩니다." 네트워크적인 측면에서 말하자면, 너무 큰 브로드캐스트 도메인은 네트워크 환경에서 패킷전송을 느리게하고 성능저하를 발생시킵니다. 따라서 네트워크를 쪼개서 통신 성능을 보장하는 것입니다. 또한 IP는 32자리 2진수로 표현할 수 있는데 이 말은 결국 최대 2의 32승만큼의 표현만 가능하다는 뜻 입니다. 즉, 자원의 한계가 존재한다는 뜻이고 결국 제한적인 자원으로 인해 주소에 낭비 없이 아껴써야 합니다. 그래서 등장하는 것이 서브넷마스크라는 녀석입니다. 서브넷 마스크는 필요한 네트워크 주소만 호스트 IP로 할당 할 수 있게 만들어 네트워크 낭비를 방지합니다. 이를 전문용어로 서브네팅이라 하며 그 반대는 슈퍼네팅이라고 합니다.

[IP의 특징]
하나의 IP는 네트워크 부분 + 호스트 부분으로 구성되어 있습니다. 하나의 네트워크 즉 하나의 브로드캐스트 도메인에 있는 IP끼리 통신하기 위해선 네크워크 영역이 같아야하며, 호스트 IP는 달라야합니다. 호스트 IP가 다르지 않다면 IP 충돌이 나겠죠? 당연히 통신하기 위해선 네트워크 영역이 같아야 합니다. 그런데 잠깐! 네트워크 영역이 다르다고 해서 통신할 수 없을까요? 아닙니다. 네트워크 영역이 달라도 라우터나 게이트웨이와 같은 통신장비를 통해 통신할 수 있습니다!. 다만 라우터와 네트워크장비 없이 통신할 수 있는 영역을 우리는 브로드캐스트 도메인이라고 하고 하나의 네티워크 영역에는 당연히 네트워크 영역은 같고, 호스트 IP는 자신의 노드를 식별할 수 있도록 다른 호스트 IP와 달라야 하겠죠? 호스트 IP는 마치 주민번호와 같은겁니다. 중복할 될 수 없는 유일무이한 것이지요.. 예를 들어 192.168.0.3 과 192.168.0.4 를 보면 192.168.0 은 네트워크 영역이고 3과 4는 호스트 IP입니다. 이를 해석하면 192.168.0 이라는 네트워크 영역에서 서로 통신할 수 있는 3과 4가 존재한다고 보면 됩니다.

[정리]
1. 서브네팅 : 네트워크 성능 보장, 자원을 효율적으로 분배하기 위해 네트워크 영역과 호스트 영역을 쪼개는 작업
2. IP : IP는 사실 32자리로 이루어진 2진수, IP는 네트워크 영역과 호스트 IP로 구성됨, 동일한 네트워크 내에서 호스트 IP는 각자 달라야 함.


## 2) 서브넷마스크 (Subnet Mask)
- 서브네팅을 하기 위해선 서브넷마스크라는 녀석이 필요하다고 했습니다. 그럼 서브넷마스크가 뭘까요??? 쉽게 생각해 봅시다. 성형외과에서 코수술을 한다고 했을때, 환자얼굴위에 수술 마스크를 덮습니다. 해당 마스크는 코만 보이고 눈,입,볼 등 코를 제외한 부분은 전부 가려줍니다. 의사가 코에만 집중할 수 있게 말이죠... 이와 비슷한 개념입니다. 어느 한 네트워크에서 1~100까지 IP를 할당받을 수 있고 우리가 필요한 IP가 20개정도라고 가정할 때, 굳이 1~100까지 IP를 줄 필요가 없습니다. 최소 1~20까지만 IP를 주면 되겠죠...그럼 나머지 21~100은 사용자가 신경쓰지 않게 끔, 서브넷마스크로 가려버린다 라고 생각하면 조금 이해하기 쉽습니다. 



## 3) 서브넷마스크 표시방법
- 서브넷마스크는 기본적으로 1과 0 으로 구성되어 있습니다. IP는 32자리 2진수로 표현할 수 있다고 했는데, 서브넷마스크 또한, IP와 크기만큼 32자리 2진수로 표현합니다. 당연하겠죠? 굳이 IP보다 클 필요가 없고 작으면 마스크하는 의미가 없습니다. 자! 이야기가 길어졌는데 다시 서브넷마스크는 1과 0으로 이뤄져있고, 1이 연속으로 나와야합니다. 11101 안되구요. 11001 안됩니다. 무조건 1111000 이런식으로 1을 표현하기 위해선 1이 연속으로 나와야 합니다.  IP주소에서 만약 192.168.0.1/24 라고 하면 이는 C클래스이고 기본 디폴트 마스크는 255.255.255.0 입니다. 디폴트 마스크도 서브넷마스크라입니다. 255.255.255.0 은 우리가 알기 쉽게 10진수로 표현했을 뿐 2진수로 바꾸면....
결국 1111 1111.1111 1111.1111 1111.0000 0000 입니다. 여기서 연속으로 1이 24개 있습니다. 눈치 채셨나요? IP 뒤에 /24 라는 표현이 1이 24개가 있다는 뜻입니다. 1은 네트워크 영역으로 사용하겠다는 뜻이고 0은 호스트 IP로 사용하겠다는 뜻입니다. 즉 사용자에게 0이 표현된 부분만 호스트 IP를 할당할 수 있게 만들겠다는 뜻입니다.

## 4) 기본 서브넷마스크, 서브넷네트워크, 논리AND계산
- 기본 서브넷마스크와 서브넷 네트워크에 대해 잠깐 알아야 합니다. 예를 들어 210.100.100.1 이라는 IP주소가 있습니다. 이 주소는 C클래스라는 것은 알고계시죠..? 클래스를 사용하고 있다는 뜻은 결국 서브넷마스크를 사용한다는 말과 같습니다. 


즉, C클래스를 그대로 사용한다는 것은 C클래스 네트워크를 쪼개지 않고 그대로 하나의 네트워크에 할당할 수 있는 2^8-2개의 호스트 IP를 사용하겠다는 뜻이고 255.255.255.0 이 기본 서브넷 마스크가 됩니다. 또한, 192.0.0 ~ 223.255.255 까지의 네트워크가 곧 서브넷 네트워크가 됩니다. 별개의 서브넷마스크를 생성하지 않아도 기본적으로 적용되어 있는게 기본 서브넷마스크이고, 기본서브넷마스크로 쪼개진 네트워크 주소를 서브넷 네트워크 라고 합니다. 


따라서 IP 210.100.100.1 기본서브넷마스크 : 255.255.255.0 서브넷네트워크 : 210.100.100.0 입니다. B클래스의 기본서브넷마스크는 그럼 255.255.0.0 이겠죠? A클래스는 255.0.0.0 입니다.





IP 210.100.100.1 를 2진수로 표현하면 다음과 같습니다.



1101 0010. 0110 0100. 0110 0100. 0000 0001 = 210.100.100.1 (IP주소)

1111 1111. 1111 1111. 1111 1111. 0000 0000 = 255.255.255.0 (C클래스 기본서브넷마스크)

1101 0010. 0110 0100. 0110 0100. 0000 0000 = 210.100.100.0 (서브넷네트워크)







위에서 알 수 있듯이 IP주소와 서브넷마스크를 논리 AND 연산하면 서브넷네트워크를 구할 수 있습니다.



논리 AND 연산은 1 AND 1 = 1, 1 AND 0 = 0, 0 AND 1 = 0, 0 AND 0 = 0 입니다.



이번엔, 150.150.100.1 에 대해서 알아볼까요..?



1001 0110. 1001 0110. 0110 0100. 0000 0001 = 150.150.100.1 (IP주소)

1111 1111. 1111 1111. 1111 1111. 0000 0000 = 255.255.255.0 (C클래스 기본서브넷마스크)

1001 0110. 1001 0110. 0110 0100. 0000 0000 = 150.150.100.0 (서브넷네트워크)



뭔가 이상하다고 느끼실 겁니다. 150.150.100.1 은 분명 B클래스인데, 서브넷 마스크가 C클래스입니다. 150.150.100.1의 기본서브넷마스크인 255.255.0.0을 사용하지 않고 C클래스 255.255.255.0 을 사용한다는 것은 B클래스 주소를 마치 C클래스 주소처럼 사용하겠다는 뜻입니다. 



다시말해 B클래스에 의해서 150.150 까지가 네트워크 영역이고 나머지 3,4번째 옥태드가 호스트 영역인데, C클래스 기본 서브넷마스크인 255.255.255.0을 씌우면서 150.150.100까지 네트워크영역이 되고 4번째 옥태드만 호스트 IP를 배정할 수 있게 만든 것 입니다.



즉 네트워크 영역을 늘리고 호스트 영역을 줄이겠다는 뜻을 내포하고 있다는 것을 여러분은 눈치채셔야 합니다. 이렇게 하나의 주소에 서브넷 마스크를 씌워서 자신에게 맞는 네트워크를 만드는 것을 서브네팅이라고 합니다. (참고로 서브넷마스크로 나뉘어진 서브넷네트워크간 통신은 라우터를 통해서 통신이 되어야 합니다. 다른 말로 서브넷네트워크는 하나의 독립적인 네트워크 입니다.)


## 5) 네트워크 영역, 호스트 영역 범위는??(갯수)

서브넷마스크를 통해서 얻을 수 있는 서브넷네트워크 갯수와 각 서브넷네트워크에 할당할 수 있는 호스트 갯수에 대해서 알아보겠습니다. 이부분은 잘 아셔야 합니다. 전산시험에 단골로 출제되거든요 ㅎㅎㅎ....



201.222.5.0 IP를 255.255.255.248 서브넷마스크(기본서브넷마스크가 아닌 사용자가 만든 서브넷마스크)에 씌워볼까요?



11001001 11011110 00000101 00000000 = 201.222.5.0 (IP 주소)

11111111 11111111 11111111 11111000 = 255.255.255.248 (서브넷마스크)



11111111 11111111 11111111 (기본 C클래스가 가질 수 있는 네트워크 영역)

11111 (사용자 지정한 네트워크영역)

000 (사용자가 지정한 호스트영역)



위와 같이 서브넷마스크를 적용했을 때, 서브넷네크워크는 몇개가 있을까요?

파란색부분이 바로 서브넷네트워크가 가질 수 있는 범위 입니다. 즉 00000 ~ 11111 (2^5) 까지 계속 바꿔 넣으면 32개의 서브넷네트워크로 쪼갤 수 있습니다. 그럼 각 서브넷마스크가 가질 수 있는 호스트 IP 갯수는 몇개일까요? 초록색 부분입니다. 000 ~ 111 (2^3-2) 까지 계쏙 바꿔 넣으면 8개를 가질 수 있습니다. 하지만 네트워크 주소와 브로드캐스트 주소를 써야하기 때문에 -2 를 해줍니다. 이건 지난시간에 배웠습니다. ㅎㅎ 결국 호스트 IP 갯수는 6개 입니다. 





어려우신가요..? 자 다시 생각해봅시다. 서브넷마스크는 하나의 IP에서 사용자의 입맛에 맞게 네트워크 영역과 호스트 영역을 나눠주는 녀석입니다. 기본서브넷네트워크도 물론 네트워크 영역과 호스트 영역을 나눠주기 때문에 서브넷마스크라고도 합니다. 하지만 진정한 의미에 서브넷마스크는 A클래스, B클래스, C클래스의 기본 서브넷마스크에서 가질 수 있는 네트워크영역과 호스트영역을 더 쪼개서 더 효율적으로 서브네팅하는 것입니다. 그렇기 때문에 위에 처럼 C클래스(3옥테드까지만 네트워크로 사용함) 이상으로 4옥테드 영역에 5자리 2진수를 네트워크 영역으로 확대하고! 호스트 영역을 3자리 2진수를 할당함으로써 기본서브넷마스크로 커버할 수 없었던 서브네팅을 구현할 수 있습니다.
- - -
