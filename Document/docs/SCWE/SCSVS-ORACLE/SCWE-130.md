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
管理者が任意の値を即座に (遅延や定足数なしで) プッシュできる価格フィードは、内部関係者による強制清算、担保設定の操作、AMM の流出につながります。信頼できるオペレータでさえも侵害される可能性があります。

## 対策
- 手動による価格プッシュにはマルチシグとタイムロックを必要とし、監視用のイベントを発行します。
- 単一ライターのフィードの集約/クォーラムでの分散型オラクルを使用します。
- 参照フィードに対して境界/逸脱チェックを強制し、外れ値を拒否します。

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
