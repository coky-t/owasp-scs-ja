---
title: 無制限の引き落としキューの増加 (Unbounded Withdrawal Queue Growth)
id: SCWE-126
alias: unbounded-withdrawal-queue
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
長さに制限を設けず、バッチ処理も行わずに引き落としをキューするプロトコルでは、大規模なキューを実行する際にガス枯渇に直面する可能性があります。攻撃者は小さなリクエストをスパムして、引き落とし実行を DoS したり、ユーザーが遅延を受け入れることを強制します。

## 対策
- キューのサイズを制限するか、ガスの上限があるバッチ処理/ページ処理を使用します。
- 手数料を課すか、最小額を要求して、スパムを抑制します。
- キュー全体を一度に処理するのではなく、ユーザーが小さな単位でキャンセル/請求できるようにします。

## 事例

### 脆弱
```solidity
function processAll() external {
    for (uint256 i = 0; i < queue.length; i++) {
        _pay(queue[i]);
    }
}
```

### 修正済み
```solidity
function processBatch(uint256 start, uint256 max) external {
    uint256 end = start + max;
    if (end > queue.length) end = queue.length;
    for (uint256 i = start; i < end; i++) {
        _pay(queue[i]);
    }
}
```
