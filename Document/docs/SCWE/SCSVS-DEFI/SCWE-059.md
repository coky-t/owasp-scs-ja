---
title: 不十分なガスブリーフィング (Insufficient Gas Griefing)
id: SCWE-059
alias: insufficient-gas-griefing
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-2]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption  
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)


## 説明
不十分なガスグリーフィングは、攻撃者が意図的に不十分なガスでトランザクションを送信し、コントラクトを失敗するように強制することで発生します。これはリソース消費の問題や、コントラクトや他のユーザーに対する潜在的なサービス拒否につながる可能性があります。コントラクトが外部呼び出しに依存していたり、他のコントラクトとやり取りしていてガス推定を適切に処理していない場合、そのような攻撃に対して脆弱になる可能性があります。

## 対策
この脆弱性を緩和するには、コントラクト呼び出し時、特に他のコントラクトとやり取りする際に、ガス推定と適切なガス制限が処理されるようにします。さらに、重要な操作を開始する前にメッセージを元に戻したり、十分なガスがあることをチェックするなど、障害を適切に処理するメカニズムを使用します。

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract GasGriefing {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function transferFunds(address payable recipient, uint256 amount) public {
        require(msg.sender == owner, "Not the owner");
        recipient.transfer(amount);  // Potential for griefing with insufficient gas
    }
}
```

### 修正したコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract GasGriefingSafe {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function transferFunds(address payable recipient, uint256 amount) public {
        require(msg.sender == owner, "Not the owner");
        (bool success, ) = recipient.call{value: amount}("");
        require(success, "Transfer failed");
    }
}
```
