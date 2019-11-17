---
layout: post
title: "Docker Swarm 다른 네트워크끼리 join하기"
date: 2019-11-18
excerpt: "network up"
tag:
- BlockChain
- RaspberryPI
- Embeded
category: [FinalProject]
comments: true

---

# 다른 네트워크 컴퓨터와 연결하기

보통 ``` sudo docker swarm init ``` 을 하면  다음과 같은 형태의 메세지가 출력된다.

```
docker swarm join --token SWMTKN-
-4xtelv38m4ide4rbe3jljv0s2sh73miup6v449sqosk2zy7yye-dwgz4xw77hrkipus7v3vnkuhe 
192.168.0.10:2377
```

이 때, 맨 마지막의 아이피와 포트번호는 swarm의 호스트 주소로써,

local IP Address를 사용하기 때문에 같은 공유기 내에서

해당 명령어를 입력하여 접근이 가능하다.

그럼 다른 네트워크에서는 어떻게 해야할까?

포트포워딩을 사용하면 된다.

---

# 포트 포워딩

포트 포워딩에 대한 자세한 내용은 아래 유튜브로 대체한다.

모르면 읽어보길 바란다.

[![image](https://user-images.githubusercontent.com/40852277/69009150-0ca36280-0996-11ea-8b44-a959e4b6a8a8.png)](https://www.youtube.com/watch?v=XT_jt0CmBbU)

따라서, 외부에서 192.168.0.10:2377로 접속하고 싶으면

해당 공유기에서 자신에게 오는 2377포트를 내부 192.168.0.10 기기의 2377포트를

포워딩 시켜주면 되는 것이다.

일단, 네트워크의 게이트웨이로 접근해서 공유기 설정을 바꿔주어야 한다.

VM ware IP가 192.168.0.10/24 이므로 게이트웨이는 192.168.0.1이다.

접속해서 포트포워딩 설정을 다음과 같이 보여준다.

![image](https://user-images.githubusercontent.com/40852277/69009243-ecc06e80-0996-11ea-8f76-dfb4b01c3d29.png)

2377포트로 연결해준 이유는 ```docker swarm init``` 명령의 결과에서 2377포트로 연결하기 때문이다.

**이렇게 설정을 해 줌으로써, 외부에서 2377포트로 접근하게 되면 VM ware의 2377 포트와 연결된다.**

또한 찾아보니 내 공유기 ( 영찬의 자취방 공유기 ) 주소는 

![image](https://user-images.githubusercontent.com/40852277/69009203-86d3e700-0996-11ea-9525-6ac22fb2a0e7.png)

211.217.202.115이다. 

이제 211.217.202.115:2377 로 접근하게 될 경우, 자취방의 192.168.0.10:2377 로 연결될 것이다.

---

# 테스트

다음과 같은 환경을 구현한다.

![image](https://user-images.githubusercontent.com/40852277/69009376-ec74a300-0997-11ea-8572-2c27cb810eb8.png)

아래 사진처럼, 데스크탑은 이더넷 (유선) 으로 공유기와 물려있으며,

![image](https://user-images.githubusercontent.com/40852277/69009422-58efa200-0998-11ea-89c3-a11f0143f789.png)

노트북은 와이파이 (무선) 으로 핫스팟과 연결되어 있는 상황이다.

![image](https://user-images.githubusercontent.com/40852277/69009433-6efd6280-0998-11ea-860e-92546fb43c11.png)



일단 blockchain, 즉 데탑에서 ```sudo docker swarm init``` 을 한다.

그럼 다음처럼 나온다.

![image](https://user-images.githubusercontent.com/40852277/69009390-0dd58f00-0998-11ea-8fc5-86b2e92e66b2.png)

하는 방법은 똑같이 이걸 다른 컴터에 붙혀 넣으면 되는데,

주의할 점은 192.168.0.10이 아니라, 외부에서 접속 할 수 있도록

192.168.0.10을 211.217.202.115 로 바꿔서 입력해준다.

![image](https://user-images.githubusercontent.com/40852277/69009461-b08e0d80-0998-11ea-9e64-5af8746d77f9.png)

그럼 놀랍게도 Join이 된다.

![image](https://user-images.githubusercontent.com/40852277/69009505-111d4a80-0999-11ea-859c-2a61459c5b78.png)

다시 데스크탑으로 돌아와서, swarm의 상태를 확인하는 명령어인 ```sudo docekr node ls```를 입력하면

아래처럼 잘 붙어있는 모습을 확인 할 수 있다.

![image](https://user-images.githubusercontent.com/40852277/69009531-4de94180-0999-11ea-8217-dc83763fa36a.png)

---

# 참고

포트포워딩은 swarm의 관리자, 즉 ```sudo docker swarm init```을 입력한 컴퓨터만 해주면 된다.

즉, 이번 실험에서 나는 핸드폰에서의 포트포워딩 설정까진 해주지 않았다.

<br>

<br>

<br>
