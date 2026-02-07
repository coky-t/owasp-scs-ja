---
title: Ether 送金の不適切な取り扱い (Improper Handling of Ether Transfers)
id: SCWE-078
alias: improper-ether-transfer-handling
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [703]
status: new
---

## 関連
- CWE-703: Improper Check or Handling of Exceptional Conditions  
  [https://cwe.mitre.org/data/definitions/703.html](https://cwe.mitre.org/data/definitions/703.html)

## 説明
Solidity における Ether 送金の不適切な取り扱いは、トランザクションの失敗や資金の喪失などの予期せぬ動作につながる可能性があります。たとえば、`transfer()` や `send()` を使用する際に、実行の成功チェックを怠ったり、例外処理を適切に行わないと、Ether がコントラクト内でロックされたり、失われる可能性があります。

## 対策
Ether を送金する際は、常に例外を適切に処理します。Ether 送金後は `require()` または `assert()` を使用して、成功または失敗をチェックするようにします。さらに、柔軟性とエラー処理を向上するために、ガス制限を指定して `call()` を使用します。

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function transferEther(address payable _to) public payable {
        // Fails silently if transfer fails
        _to.transfer(msg.value);  // No error handling, can cause issues
    }
}
```
### 修正したコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function transferEther(address payable _to) public payable {
        (bool success, ) = _to.call{value: msg.value}("");
        require(success, "Transfer failed");
    }
}
```
