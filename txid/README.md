# rawblockdata

## 비트코인 익스플로러에서 Transaction을 보면 JSON 형식으로 정리되어 보이지만, 실제 데이터가 어떻게 기록되는지 알아봅시다.

### 실습

- https://www.blockchain.com/explorer/transactions/btc/3500b0e3452e801e648a0aff1a5a7bd6f2c3f9ce26fd4b1920a391e18d1f9d53

위 Tx의 JSON은 아래와 같습니다.

#### json tx

```json
{
  "txid": "3500b0e3452e801e648a0aff1a5a7bd6f2c3f9ce26fd4b1920a391e18d1f9d53",
  "size": 259,
  "version": 1,
  "locktime": 0,
  "fee": 0,
  "inputs": [
    {
      "coinbase": false,
      "txid": "3f6c9c6d10f5de9987ea9ccc93de4de6e623d473c096d38a1b6295daa90b3d50",
      "output": 0,
      "sigscript": "493046022100b14862b44b36923c3ae37b5a1213d393bbb3aa1628f0cdc05445a79bf30e4c3b0221009ba1dd9c3c6b54db8a662062f03a663e1ce9fdb67e8b8c64198f3aa0c3c1c432014104b672897640cfd6e1224deb01b5d5c93642f6d68162257ed8368912110076a21d6652a7a06ca9d83fe438201b0a8005051b7ff6895e57e6eeec2e76fe338960e9",
      "sequence": 4294967295,
      "pkscript": "76a914d5041b24e832c57f0eb740933f60f3272ab700bb88ac",
      "value": 3879000000,
      "address": "1LRKr9pFgdf21rjaggqAdNcW5fspmqpzvc",
      "witness": []
    }
  ],
  "outputs": [
    {
      "address": "1LNcm9WfCBmtdXMERVN2XL76DccvsmNxos",
      "pkscript": "76a914d480fa1c53784740a78625ef63dccd7826fdc2e488ac",
      "value": 5000000,
      "spent": true,
      "spender": {
        "txid": "318664514045632ca8de2f0dbe3434a5ffaa51cab2da1abf36234f439cd6e844",
        "input": 7
      }
    },
    {
      "address": "1MZRzXUVt9Fqj9fZrJMVRiswWmyKXqdTmz",
      "pkscript": "76a914e184b0463811a9af802debfcfa73665250a8cf2588ac",
      "value": 3874000000,
      "spent": true,
      "spender": {
        "txid": "0160c5686b5c3c7610558742ddafa88e3e2f92d77d08d03ec2148d5330c22b5b",
        "input": 0
      }
    }
  ],
  "block": {
    "height": 95000,
    "position": 1
  },
  "deleted": false,
  "time": 1291256434,
  "rbf": false,
  "weight": 1036
}
```

위 처럼 Tx의 거래가 JSON으로 정리되어 있어 보이지만, 실제로는 아래와 같이 데이터가 저장되어있습니다.

- https://chainquery.com/bitcoin-cli/getrawtransaction

링크에서 txid에 "3500b0e3452e801e648a0aff1a5a7bd6f2c3f9ce26fd4b1920a391e18d1f9d53" 를 넣고 실행해보세요.

그럼 아래와 같은 결과가 나옵니다.

#### raw tx

```json
{
  "result": "0100000001503d0ba9da95621b8ad396c073d423e6e64dde93cc9cea8799def5106d9c6c3f000000008c493046022100b14862b44b36923c3ae37b5a1213d393bbb3aa1628f0cdc05445a79bf30e4c3b0221009ba1dd9c3c6b54db8a662062f03a663e1ce9fdb67e8b8c64198f3aa0c3c1c432014104b672897640cfd6e1224deb01b5d5c93642f6d68162257ed8368912110076a21d6652a7a06ca9d83fe438201b0a8005051b7ff6895e57e6eeec2e76fe338960e9ffffffff02404b4c00000000001976a914d480fa1c53784740a78625ef63dccd7826fdc2e488ac808ce8e6000000001976a914e184b0463811a9af802debfcfa73665250a8cf2588ac00000000",
  "error": null,
  "id": null
}
```

위 raw tx 값만 봐서는 이해하기 어렵습니다. result를 sha256함수로 두 번 실행 후 Endian변경을 해보세요.

- 링크

> https://emn178.github.io/online-tools/sha256.html
> https://blockchain-academy.hs-mittweida.de/litte-big-endian-converter/

- sha 1회

  > 9e9e375a6b6c315a4fcaae107aa7f21a88501950e04378dd64fe2551658a1a95

- sha 2회

  > 539d1f8de191a320194bfd26cef9c3f2d67b5a1aff0a8a641e802e45e3b00035

- Endian 변경

  > 3500B0E3452E801E648A0AFF1A5A7BD6F2C3F9CE26FD4B1920A391E18D1F9D53

이 결과는 txid와 동일합니다.

그럼 result 값의 각 부분이 어떤 의미인지 나누어서 알아봅시다.

```json
{
  "result":
  "01
  00000001
  503d0ba9da95621b8ad396c073d423e6e64dde93cc9cea8799def5106d9c6c3f
  00000000
  8c
  493046022100b14862b44b36923c3ae37b5a1213d393bbb3aa1628f0cdc05445a79bf30e4c3b0221009ba1dd9c3c6b54db8a662062f03a663e1ce9fdb67e8b8c64198f3aa0c3c1c432014104b672897640cfd6e1224deb01b5d5c93642f6d68162257ed8368912110076a21d6652a7a06ca9d83fe438201b0a8005051b7ff6895e57e6eeec2e76fe338960e9
  ffffffff
  02
  404b4c0000000000
  19
  76a914d480fa1c53784740a78625ef63dccd7826fdc2e488ac
  808ce8e600000000
  19
  76a914e184b0463811a9af802debfcfa73665250a8cf2588ac
  00000000",
}
```

- 01 : version

- 00000001 : 트랜잭션 인풋값 갯수. 이 트랜잭션이 참조하는 이전 트랜잭션의 입력값(UTXO)의 개수를 나타냄.

- 503d0ba9da95621b8ad396c073d423e6e64dde93cc9cea8799def5106d9c6c3f : 이 트랜잭션이 참조하는 UTXO가 존재하는 트랜잭션의 식별자(어떤 거래로부터 왔는지)

- 00000000 : 아웃풋 인덱스(8자리 고정임)

- 8c : 10진수로 140. 140bytes임.(뒤의 스크립트 용량을 의미함. 16진수 280개)

- 493046022100b14862b44b36923c3ae37b5a1213d393bbb3aa1628f0cdc05445a79bf30e4c3b0221009ba1dd9c3c6b54db8a662062f03a663e1ce9fdb67e8b8c64198f3aa0c3c1c432014104b672897640cfd6e1224deb01b5d5c93642f6d68162257ed8368912110076a21d6652a7a06ca9d83fe438201b0a8005051b7ff6895e57e6eeec2e76fe338960e9 : 언락킹 스크립트.(280개)(sigscript)

- ffffffff

- 02 : 아웃풋 갯수. 트랜잭션의 출력값(새로운 UTXO)의 개수

- 404b4c0000000000 : endian변경 후 10진수로 바꾸면 5000000(value). 출력값의 금액을 나타내며, 사토시 단위로 표시. 여기서는 5000000 사토시, 즉 0.05 BTC임.

- 19 : 락킹스크립트의 길이(25bytes = 50자리)

- 76a914d480fa1c53784740a78625ef63dccd7826fdc2e488ac : P2PKH (Pay to Public Key Hash) 스크립트.

- 808ce8e600000000 : endian변경 후 10진수로 바꾸면 3874000000(value). 3874000000 사토시, 즉 38.74 BTC임.

- 19 : 락킹스크립트 길이

- 76a914e184b0463811a9af802debfcfa73665250a8cf2588ac : P2PKH (Pay to Public Key Hash) 스크립트

- 00000000 : 락타임
