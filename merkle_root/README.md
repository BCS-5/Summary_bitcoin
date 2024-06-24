# merkle root

## 머클루트가 어떻게 만들어질까요?

### 실습 참고 링크

실습 참고 링크

- 비트코인 116250블록(6개의 거래가 있음)

  > https://www.blockchain.com/explorer/blocks/btc/116250

- Sha256

  > https://emn178.github.io/online-tools/sha256.html

- Endian converter

  > https://blockchain-academy.hs-mittweida.de/litte-big-endian-converter/

### 해당 블록의 머클루트 값 : bb38954bc9e5feca03651a9c2c1c2eeb45b15c2f06446087b6bbf7c9ee739590

### 거래 목록

블록 내 거래가 6개이므로 5번과 6번 거래를 복사합니다. 이유는 머클루트 계산 시 거래 수가 2의 제곱이어야 하기 때문입니다. 따라서, 최종 거래 목록은 12 / 34 / 56 / 56입니다.

### 실습 과정

1. 각 거래 해시를 Endian 형식으로 변경
2. 두 개씩 이어서 해시를 두 번 실행
3. 각 단계에서 남은 거래도 동일한 방법으로 해시 실행
4. 최종적으로 두 개씩 묶어 해시를 두 번 실행
5. 마지막 결과를 Endian 형식으로 변경

원본 해시

- 1,2번
  c803a889290cc61acff4bb5996eb393e9d342127c6f07ed366f107d9b8b6ee3b  
  fd22c409d0abd42d3bb53359d3b08fd767e7fd4cf0f58be8c5a0fbe37ed731ad

- 3,4번
  ea3020a38addbd0daa8c7f1ad691e4f2417b587b553007773ead698b5235589e  
  f1798de1e9c9ddbb6dba3dff9a1627d41c8c7272184b2faaf16fcdc14112a861

- 5,6번
  e3e1148badb3061d9e801c73729962f1dfbca6949cd69f69b3f816c2a5a3634b  
  f5ac84a686bc528818e95005e47d224fc348040e6e5be9dff0b4d3ba47747257

- 5,6번
  e3e1148badb3061d9e801c73729962f1dfbca6949cd69f69b3f816c2a5a3634b  
  f5ac84a686bc528818e95005e47d224fc348040e6e5be9dff0b4d3ba47747257

1. 각 거래 해시를 Endian 형식으로 변경

- 1,2번
  3BEEB6B8D907F166D37EF0C62721349D3E39EB9659BBF4CF1AC60C2989A803C8  
  AD31D77EE3FBA0C5E88BF5F04CFDE767D78FB0D35933B53B2DD4ABD009C422FD

- 3,4번
  9E5835528B69AD3E770730557B587B41F2E491D61A7F8CAA0DBDDD8AA32030EA  
  61A81241C1CD6FF1AA2F4B1872728C1CD427169AFF3DBA6DBBDDC9E9E18D79F1

- 5,6번
  4B63A3A5C216F8B3699FD69C94A6BCDFF1629972731C809E1D06B3AD8B14E1E3  
  57727447BAD3B4F0DFE95B6E0E0448C34F227DE40550E9188852BC86A684ACF5

- 5,6번
  4B63A3A5C216F8B3699FD69C94A6BCDFF1629972731C809E1D06B3AD8B14E1E3  
  57727447BAD3B4F0DFE95B6E0E0448C34F227DE40550E9188852BC86A684ACF5

2. 두 개씩 이어서 해시를 두 번 실행 (위 실습 링크에서 sha256실행 할 때, 타입이 Hex인지 확인!)

- 1+2
  d6b6a9ea6dcc08c48c4e0323f7140516dba8bd8024f0d78228162b78601eb575

- 3+4
  5c4df40a651a4bae6a2c57b418b2addbef7e5ff0ff75f709da3a529390157a93

- 5+6
  e86ef74be8f8913e53fe8c4489fac0fa13ce338f63de459fb65affccd2659a21

- 5+6
  e86ef74be8f8913e53fe8c4489fac0fa13ce338f63de459fb65affccd2659a21

3. 각 단계에서 남은 거래도 동일한 방법으로 해시 실행

- 1+2+3+4
  69f8b5a36de92cf232bb64bfcbd3cfc92f7b2bee5f6a2368d97809c2f1bc6695

- 5+6+5+6
  cffe2535c2cc43a819aa88c94727f00468e9e68fc7e4f33b3f258621952986b4

4. 최종적으로 두 개씩 묶어 해시를 두 번 실행

- 1+2+3+4+5+6+5+6
  909573eec9f7bbb6876044062f5cb145eb2e1c2c9c1a6503cafee5c94b9538bb

5. 마지막 결과를 Endian 형식으로 변경

> BB38954BC9E5FECA03651A9C2C1C2EEB45B15C2F06446087B6BBF7C9EE739590

결과 값을 해당 블록의 머클루트와 비교하면 일차하는 것을 확인 할 수 있습니다.

## Path 구하기

문제) 1~11의 거래가 있는데 9번 거래의 검증 요청이 들어왔음. Path 를 구해보세요!

원래 거래 리스트: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11

2의 제곱승이 되도록 복사: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 11, 9, 10, 11, 11

### 9번 거래의 경로

9번 거래가 경로에서 거치는 중간 값들을 추적하면:

- 첫 단계: 9의 짝인 10 => 10
- 두 번째 단계: 910 => 9101111
- 세 번째 단계: 9101111
- 네 번째 단계: 12345678

따라서 9번 거래의 검증 경로는 `10, 1111, 9101111, 12345678` 입니다.
