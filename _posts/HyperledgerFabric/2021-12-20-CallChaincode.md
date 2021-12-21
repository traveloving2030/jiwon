---
layout: post
title: "체인코드 내에서 체인코드 호출하기"
date: 2021-12-20
excerpt: "chaincode"
tag:
- BlockChain
- Hyperledger Fabric
category: [HyperledgerFabric]
comments: true

---


# 체인코드 내에서 체인코드 호출하기

- 기본 Basic Network가 실행중인 상태라고 가정
    - `다른 체인코드의 데이터를 query` 하는 것이기 때문에 2개의 체인코드가 필요
    - 2ndChaincode.go 내에서 my-cc-contract 체인코드 내의 create 함수 호출부

```go
// 2ndChaincode.go
func init() {
	if os.Getenv("DEV_CHANNEL_NAME") != "" { // dev mode
		ContractCfg.CC = "my-cc-contract"
	} else {
		ContractCfg.CC = "my-contract"
	}
}

func CreateContract(stub shim.ChaincodeStubInterface, doc []byte, expiry int64, signers *stringset.Set) (*Contract, error) {
	if nil == signers || signers.Size() < 2 {
		return nil, errors.New("signers must be 2+")
	}
	expb := []byte(strconv.FormatInt(expiry, 10))
	args := [][]byte{[]byte("create"), doc, expb} 
	for signer := range signers.Map() {
		args = append(args, []byte(signer))
	}
	// invoke
	res := stub.InvokeChaincode(ContractCfg.CC, args, "")
	if res.GetStatus() == 200 {
		payload := res.GetPayload()
		m := make(map[string]interface{})
		err := json.Unmarshal(payload, &m)
		if err != nil {
			return nil, err
		}
		contract := &Contract{_payload: payload, _map: m}
		return contract, nil
	}
	return nil, errors.New(res.GetMessage())
}
```