---
layout: post
title: "새로 Chaincode 만들때 유의사항"
date: 2019-09-20
excerpt: "chaincode config"
tag:
- BlockChain
- RaspberryPI
- Embeded
category: [FinalProject]
comments: true
---

## Chaincode 새로 적용하는 방법

1. docker-compose.yaml 파일의 fabric-tools 컨테이너에 적용된 chaincode의 환경변수를 보면
`/data/Hyperprov/chaincode` 변수는 `:/opt/gopath/src/github.com/hyperledger/fabric/examples/chaincode/go`를 가리킨다.
따라서 /data/Hyperprov/chaincode 디렉터리 내에 적용할 chaincode를 넣는다

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/chaincode.PNG" width = "70%" />

2. scripts 폴더 내의 script_ds.sh 파일로 가서 하고싶은 chaincode 이름을 넣어주고 1번에서 적용한 chaincode 디렉터리를 넣는다

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/install.PNG" width = "70%" />
