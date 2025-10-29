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
To mitigate this vulnerability, ensure that gas estimation and proper gas limits are handled when performing contract calls, especially when interacting with other contracts. Additionally, use mechanisms to handle failures gracefully, such as revert messages and checks for sufficient gas before initiating important operations.

### 脆弱なコントラクトの例
```solidity
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
contract GasGriefingSafe {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function transferFunds(address payable recipient, uint256 amount) public {
        require(msg.sender == owner, "Not the owner");
        bool success = recipient.send(amount);  // Safe transfer with gas estimation
        require(success, "Transfer failed due to insufficient gas");
    }
}
```
