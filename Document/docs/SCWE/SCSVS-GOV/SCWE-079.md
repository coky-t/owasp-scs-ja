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
Solidity における Ether 送金のための `.transfer()` と ` .send() ` の使用は、2300 ガスの固定のガス制限を課すため、安全ではありません。この制限は、受信するコントラクトが多くのガスを必要とする複雑なロジックを持つ場合、トランザクションが予期せず失敗することにつながる可能性があります。さらに、受信するコントラクトが不十分なガスにより実行できない場合、サービス拒否 (DoS) 脆弱性につながる可能性があります。

この問題は、受信するコントラクトのガス要件が時間経過とともに変化する可能性がある、アップグレード可能なスマートコントラクトやプロトコルインタラクションにおいて特に厄介です。


## 対策
Instead of `.transfer()` and `.send()`, use `.call{value: msg.value}("")`, which allows more flexible gas allocation and prevents DoS risks. Always check for the return value of `.call()` to ensure the transfer was successful.


### 脆弱なコントラクトの例
```solidity
contract Example {
    function transferEther(address payable _to) public payable {
        // Fixed gas limit of 2300 gas can cause unintended failures
        _to.transfer(msg.value);  
    }
}
```
**Problem**:
- If `_to` is a contract that requires more than `2300 gas` (e.g., it has a fallback function with state changes), this transfer will fail.
- The contract has no error handling, meaning the sender won't be aware of the failure.


### 修正したコントラクトの例
```solidity
contract Example {
    function transferEther(address payable _to) public payable {
        // Use call() for better gas flexibility and proper error handling
        (bool success, ) = _to.call{value: msg.value}("");
        require(success, "Transfer failed");
    }
}
```
**Why is this better?**
- `.call{value: msg.value}("")` does not impose a gas limit, allowing the receiving contract to execute as needed.
- It includes a `require(success, "Transfer failed");` check, ensuring failures are properly handled.
