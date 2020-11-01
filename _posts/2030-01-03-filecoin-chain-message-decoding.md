---
layout: post
title: "Filecoin Lotus 链消息解析"
date: 2030-01-02 16:30 +0800
categories: tech
---

Filecoin中的链消息的参数是通过CBOR方式编码的，在解码前是不知道里面存的具体是什么数据。CBOR将对象编码成二进制数据，在传输过程中以base64编码方式传输，所以在解码是现将base64格式字符串解码，然后在解析成对应类型。

这里以`PreCommitSector`提交扇区消息为例，说明如何解析链消息中的`Params`字段，其他字段按原来JSON格式解析。

# 链消息格式
```go
type Message struct {
	Version uint64

	To   address.Address
	From address.Address

	Nonce uint64

	Value abi.TokenAmount

	GasLimit   int64
	GasFeeCap  abi.TokenAmount
	GasPremium abi.TokenAmount

	Method abi.MethodNum
	Params []byte
}
```
消息中的`Params`为消息参数，在传输过程中该字段会被编码成`CBOR`格式。

`PreCommitSector`消息对应的参数`Params`类型为: `SectorPreCommitInfo`，源码路径: `github.com/filecoin-project/specs-actors/actors/builtin/miner/miner_state` -> SectorPreCommitInfo。

SectorPreCommitInfo: 
```go
// Information provided by a miner when pre-committing a sector.
type SectorPreCommitInfo struct {
	SealProof       abi.RegisteredSealProof
	SectorNumber    abi.SectorNumber
	SealedCID       cid.Cid `checked:"true"` // CommR
	SealRandEpoch   abi.ChainEpoch
	DealIDs         []abi.DealID
	Expiration      abi.ChainEpoch
	ReplaceCapacity bool // Whether to replace a "committed capacity" no-deal sector (requires non-empty DealIDs)
	// The committed capacity sector to replace, and it's deadline/partition location
	ReplaceSectorDeadline  uint64
	ReplaceSectorPartition uint64
	ReplaceSectorNumber    abi.SectorNumber
}
```

# 查询一个`PreCommitSector`消息
```shell
// Get message by using lotus CLI
$> ~/hlm-miner/script/lotus/lotus-user# ./lotus.sh chain getmessage bafy2bzacec3x5rlwdveh46m55dbe47g7bmzlhncpczwwgcuoowvazumpf25se
{
	"Version": 0,
	"To": "f03176",
	"From": "f3ruolcbftfwhudz2qexbkylbj2y2awtxeopovv5jeoafikmstk4j7bwowynmnes66xv6fselgolug55uuxp4q",
	"Nonce": 687292,
	"Value": "150610342700684832",
	"GasLimit": 15145026,
	"GasFeeCap": "101967",
	"GasPremium": "100913",
	"Method": 6,
	"Params": "igMaAAVUk9gqWCkAAYLiA4HoAiCHKF0gQg0SAAfbmkUG8iNEAyelBUyRNNBOfCQJBZZXSxoAAslIgRoADudPGgAK4gz0AAAA",
	"CID": {
	"/": "bafy2bzacec3x5rlwdveh46m55dbe47g7bmzlhncpczwwgcuoowvazumpf25se"
	}
}
```

`"Method:": 6` => 6表示此消息方法为`PreCommitSector`。

`CID` => 消息ID。
```
"Params": "igMaAAVUk9gqWCkAAYLiA4HoAiCHKF0gQg0SAAfbmkUG8iNEAyelBUyRNNBOfCQJBZZXSxoAAslIgRoADudPGgAK4gz0AAAA"
```
以上`Params`为传输中被编码为base64的`SectorPreCommitInfo`结构信息。

# 解码

## 解码base64: 
```go
// base64 encoded params
params := "igMaAAVUk9gqWCkAAYLiA4HoAiCHKF0gQg0SAAfbmkUG8iNEAyelBUyRNNBOfCQJBZZXSxoAAslIgRoADudPGgAK4gz0AAAA"

var precommitInfo miner.SectorPreCommitInfo
bin, err := base64.StdEncoding.DecodeString(params)
if err != nil {
	log.Error(err)
	return
}
```

## 解码CBOR格式数据： 

解码CBOR格式数据的代码可以参考源码`github.com/lotus/cmd/lotus-pcr/` -> main.go。
```go
m := &types.Message{
	Params: bin,
}

if err := precommitInfo.UnmarshalCBOR(bytes.NewReader(m.Params)); err != nil {
	log.Error(err)
	return
}
```

解析后的`precommitInfo`数据: 
```shell
{
	SealProof:3 
	SectorNumber:349331 
	SealedCID:bagboea4b5abcbbziluqeedisaad5xgsfa3zcgrade6sqktergtie47bebeczmv2l 
	SealRandEpoch:182600 
	DealIDs:[976719] 
	Expiration:713228 
	ReplaceCapacity:false 
	ReplaceSectorDeadline:0 
	ReplaceSectorPartition:0 
	ReplaceSectorNumber:0
} 
```

## 完整的解析例子代码
```go
package filmessage

import (
	"bytes"
	"encoding/base64"
	"testing"

	"github.com/filecoin-project/lotus/chain/types"
	"github.com/filecoin-project/specs-actors/actors/builtin/miner"
	log "github.com/sirupsen/logrus"
)

func TestChainMessageDecoding(t *testing.T) {
	// Message struct
	// lotus -> chain -> types -> Message
	// type Message struct {
	// 	Version uint64

	// 	To   address.Address
	// 	From address.Address

	// 	Nonce uint64

	// 	Value abi.TokenAmount

	// 	GasLimit   int64
	// 	GasFeeCap  abi.TokenAmount
	// 	GasPremium abi.TokenAmount

	// 	Method abi.MethodNum
	// 	Params []byte
	// }

	// Get message by using lotus CLI
	// $> ~/hlm-miner/script/lotus/lotus-user# ./lotus.sh chain getmessage bafy2bzacec3x5rlwdveh46m55dbe47g7bmzlhncpczwwgcuoowvazumpf25se
	// {
	//   "Version": 0,
	//   "To": "f03176",
	//   "From": "f3ruolcbftfwhudz2qexbkylbj2y2awtxeopovv5jeoafikmstk4j7bwowynmnes66xv6fselgolug55uuxp4q",
	//   "Nonce": 687292,
	//   "Value": "150610342700684832",
	//   "GasLimit": 15145026,
	//   "GasFeeCap": "101967",
	//   "GasPremium": "100913",
	//   "Method": 6,
	//   "Params": "igMaAAVUk9gqWCkAAYLiA4HoAiCHKF0gQg0SAAfbmkUG8iNEAyelBUyRNNBOfCQJBZZXSxoAAslIgRoADudPGgAK4gz0AAAA",
	//   "CID": {
	//     "/": "bafy2bzacec3x5rlwdveh46m55dbe47g7bmzlhncpczwwgcuoowvazumpf25se"
	//   }
	// }

	// base64 encoded params
	params := "igMaAAVUk9gqWCkAAYLiA4HoAiCHKF0gQg0SAAfbmkUG8iNEAyelBUyRNNBOfCQJBZZXSxoAAslIgRoADudPGgAK4gz0AAAA"

	var precommitInfo miner.SectorPreCommitInfo
	bin, err := base64.StdEncoding.DecodeString(params)
	if err != nil {
		log.Error(err)
		return
	}

	m := &types.Message{
		Params: bin,
	}

	log.Println("bin => ", bin)

	if err := precommitInfo.UnmarshalCBOR(bytes.NewReader(m.Params)); err != nil {
		// log.Warnw("failed to decode precommit params", "err", err, "method", messageMethod, "cid", msg.Cid, "miner", m.To)
		// return false, messageMethod, types.NewInt(0), nil
		log.Error(err)
		return
	}

	log.Printf("precommit info => %+v", precommitInfo)

	//Output:
	// INFO[0000] bin =>  [138 3 26 0 5 84 147 216 42 88 41 0 1 130 226 3 129 232 2 32 135 40 93 32 66 13 18 0 7 219 154 69 6 242 35 68 3 39 165 5 76 145 52 208 78 124 36 9 5 150 87 75 26 0 2 201 72 129 26 0 14 231 79 26 0 10 226 12 244 0 0 0]
	// INFO[0000] precommit info => {SealProof:3 SectorNumber:349331 SealedCID:bagboea4b5abcbbziluqeedisaad5xgsfa3zcgrade6sqktergtie47bebeczmv2l SealRandEpoch:182600 DealIDs:[976719] Expiration:713228 ReplaceCapacity:false ReplaceSectorDeadline:0 ReplaceSectorPartition:0 ReplaceSectorNumber:0}
	// PASS
	// ok  	grandhelmsman.com/test/chain	0.003s
}

```

# 参考
[https://github.com/whyrusleeping/cbor/blob/master/go/cbor_test.go](https://github.com/whyrusleeping/cbor/blob/master/go/cbor_test.go)

