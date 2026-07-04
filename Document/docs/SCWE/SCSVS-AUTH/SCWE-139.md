---
title: 確認なしでの単一ステップの所有者移転 (Single-Step Ownership Transfer Without Confirmation)
id: SCWE-139
alias: missing-two-step-ownership-transfer
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [670]
status: new
---

## 関連
- CWE-670: Always-Incorrect Control Flow Implementation  
  [https://cwe.mitre.org/data/definitions/670.html](https://cwe.mitre.org/data/definitions/670.html)

## 説明
`transferOwnership(newOwner)` という単一ステップが所有権を新しいアドレスに即座に割り当てます。誤ったアドレスが使用された場合 (誤記、バーンアドレス `address(0)`、所有権を受け取ることができないコントラクト)、コントラクトは永久にロックされ、所有者のみが可能なアクションを実行したり誤りを正すことはできなくなります。二ステップのプロセス (提案→承認) は、意図した受信者が確認でき、意図しないコントロールの喪失を防ぎます。

## 対策
- 二ステップの所有権移転を実装します。`transferOwnershipPending(newOwner)` が保留中の所有者を設定し、`acceptOwnership()` (保留中の所有者のみが呼び出し可能) が移転を完了します。
- `newOwner != address(0)` であることを検証し、運用上の理由から所有者が EOA である必要がある場合にはコントラクトアドレスを拒否することを検討します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Ownable {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function transferOwnership(address newOwner) external {
        require(msg.sender == owner, "Not owner");
        owner = newOwner;  // Single step: typo or address(0) permanently locks contract
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Ownable {
    address public owner;
    address public pendingOwner;

    constructor() {
        owner = msg.sender;
    }

    function transferOwnership(address newOwner) external {
        require(msg.sender == owner, "Not owner");
        require(newOwner != address(0), "Zero address");
        pendingOwner = newOwner;
    }

    function acceptOwnership() external {
        require(msg.sender == pendingOwner, "Not pending owner");
        owner = pendingOwner;
        delete pendingOwner;
    }
}
```
