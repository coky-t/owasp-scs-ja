---
title: Transfer と Send の安全でない使用 (Insecure Use of Transfer and Send)
id: SCWE-079
alias: insecure-use-of-transfer-send
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption  
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)

## 説明
Solidity における Ether 送金のための `.transfer()` と `.send()` の使用は、2300 ガスの固定のガス制限を課すため、安全ではありません。この制限は、受信側コントラクトが多くのガスを必要とする複雑なロジックを持つ場合、トランザクションが予期せず失敗することにつながる可能性があります。さらに、受信側コントラクトが不十分なガスにより実行できない場合、サービス拒否 (DoS) 脆弱性につながる可能性があります。

この問題は、受信側コントラクトのガス要件が時間経過とともに変化する可能性がある、アップグレード可能なスマートコントラクトやプロトコルインタラクションにおいて特に厄介です。


## 対策
`.transfer()` や `.send()` ではなく `.call{value: msg.value}("")` を使用します。これはより柔軟なガス割り当てを可能にし、DoS リスクを防ぎます。常に `.call()` の戻り値をチェックし、送金が成功したことを確認します。


### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function transferEther(address payable _to) public payable {
        // Fixed gas limit of 2300 gas can cause unintended failures
        _to.transfer(msg.value);  
    }
}
```
**問題点**:
- `_to` が `2300 gas` を超えるコントラクト (たとえば、状態変化を伴うフォールバック関数を持つもの) である場合、この送信は失敗します。
- コントラクトはエラー処理を持たず、送信側は失敗に気が付かないことを意味します。


### 修正したコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function transferEther(address payable _to) public payable {
        // Use call() for better gas flexibility and proper error handling
        (bool success, ) = _to.call{value: msg.value}("");
        require(success, "Transfer failed");
    }
}
```
**なぜこれが優れているのか？**
- `.call{value: msg.value}("")` はガス制限を課さず、受信側コントラクトは必要に応じて実行できます。
- `require(success, "Transfer failed");` チェックを含み、失敗が適切に処理されるようにしています。
