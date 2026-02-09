---
title: 遅延なしでの管理者記述オラクル (Admin-Write Oracle Without Delay)
id: SCWE-130
alias: admin-writable-oracle
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ORACLE]
  scsvs-scg: [SCSVS-ORACLE-1]
  cwe: [346]
status: new
---

## 関連
- CWE-346: Origin Validation Error  
  [https://cwe.mitre.org/data/definitions/346.html](https://cwe.mitre.org/data/definitions/346.html)

## 説明
Price feeds that allow an admin to push arbitrary values immediately (no delay or quorum) let insiders force liquidations, manipulate collateralization, or drain AMMs. Even trusted operators can be compromised.

## 対策
- Require multi-sig + timelock for manual price pushes; emit events for monitoring.
- Use decentralized oracles with aggregation/quorum instead of single-writer feeds.
- Enforce bounds/deviation checks against reference feeds and reject outliers.

## 事例

### 脆弱
```solidity
function setPrice(uint256 p) external onlyOwner {
    price = p; // immediate effect
}
```

### 修正済み
```solidity
function queuePrice(uint256 p) external onlyGuardian {
    queued = Price({val:p, eta:block.timestamp + delay});
}
function execute() external {
    require(block.timestamp >= queued.eta, "too early");
    price = queued.val;
}
```
