---
title: 管理鍵管理での単一障害点 (Single Point of Failure in Administrative Key Management)
id: SCWE-155
alias: centralization-risk-single-admin-key
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [284]
status: new
---

## 関連
- CWE-284: Improper Access Control  
  [https://cwe.mitre.org/data/definitions/284.html](https://cwe.mitre.org/data/definitions/284.html)

## 説明
マルチシグ、タイムロック、キーローテーションなしで単一の管理鍵 (EOA またはコントラクト) に依存するコントラクトは単一障害点を生み出します。その鍵の侵害 (フィッシング、マルウェア、物理的な窃取) は完全な制御を与えます。鍵の喪失 (バックアップなしなど) は重要な機能を永久にロックできます。SCWE-129 は単一の EOA 管理をカバーしますが、この弱点は単一鍵設計の *リスク* とその緩和策の欠如に焦点を当てています。

## 対策
- 管理アクションにはマルチシグウォレット (例: Gnosis Safe) を使用します。
- 機密性の高い操作にはタイムロックを実装します (SCWE-020)。
- キーローテーションやリカバリメカニズムを計画します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Treasury {
    address public owner;

    function withdraw(uint256 amount) external {
        require(msg.sender == owner, "Not owner");
        (bool ok, ) = owner.call{value: amount}("");
        require(ok, "Transfer failed");
    }
}
```
**リスク:** 単一の `owner` 鍵であり、マルチシグやタイムロックがありません。侵害や紛失はトレジャリーに永続的な影響を及ぼします。

### 修正済み
```solidity
contract Treasury {
    address public owner;  // In production: use multisig (e.g., Gnosis Safe)
    uint256 public constant TIMELOCK = 2 days;
    uint256 public pendingAmount;
    uint256 public unlockTime;

    function proposeWithdraw(uint256 amount) external {
        require(msg.sender == owner, "Not owner");
        pendingAmount = amount;
        unlockTime = block.timestamp + TIMELOCK;
    }

    function executeWithdraw() external {
        require(msg.sender == owner, "Not owner");
        require(block.timestamp >= unlockTime, "Timelock");
        uint256 amount = pendingAmount;
        pendingAmount = 0;
        (bool ok, ) = owner.call{value: amount}("");
        require(ok, "Transfer failed");
    }
}
```
**修正:** タイムロックは遅延を設けることで、多額の引き落としには意義を申し立てることができます。本番環境では `owner` にマルチシグを使用します。
