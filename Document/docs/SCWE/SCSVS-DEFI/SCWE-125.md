---
title: 操作後ヘルスチェックの欠如 (Missing Post-Operation Health Check)
id: SCWE-125
alias: missing-health-check
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-1]
  cwe: [754]
status: new
---

## 関連
- CWE-754: Improper Check for Unusual or Exceptional Conditions  
  [https://cwe.mitre.org/data/definitions/754.html](https://cwe.mitre.org/data/definitions/754.html)

## 説明
アクション (借入、引出、レバレッジ) 後に健全性要因を再評価しない貸借プロトコルは、ユーザーが不健全なポジションで終了することを可能にします。攻撃者は、操作後の支払い能力チェックをスキップすることで、担保を流用したり、清算を回避できます。

## 対策
- 状態変化アクションの後ごとに健全性要因を再計算し、閾値を下回る場合は元に戻します。
- 操作中は価格/オラクル読み取りをロックし、トランザクション中の操作を防ぎます。
- 不変条件/ファズテストを追加し、すべてのパスが支払能力を実行することを確保します。

## 事例

### 脆弱
```solidity
function borrow(uint256 amount) external {
    _issueDebt(msg.sender, amount);
    // no health check after debt increase
}
```

### 修正済み
```solidity
function borrow(uint256 amount) external {
    _issueDebt(msg.sender, amount);
    require(_health(msg.sender) >= MIN_HF, "insolvent");
}
```
