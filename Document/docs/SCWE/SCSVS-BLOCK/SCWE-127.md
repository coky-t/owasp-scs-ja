---
title: EIP-1559 基本料金の想定 (EIP-1559 Basefee Assumptions)
id: SCWE-127
alias: basefee-assumptions
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BLOCK]
  scsvs-scg: [SCSVS-BLOCK-2]
  cwe: [841]
status: new
---

## 関連
- CWE-841: Improper Enforcement of Behavioral Workflow  
  [https://cwe.mitre.org/data/definitions/841.html](https://cwe.mitre.org/data/definitions/841.html)

## 説明
ガス価格/基本料金の想定をハードコードする (固定の `gasprice` 制限や払い戻しの期待など) と、EIP-1559 チェーンで機能しない可能性があります。正確な `block.basefee` 範囲を要求したり、固定ガスを払い戻すコントラクトは、ガス荒らしによってスタックしたり悪用できる可能性があります。

## 対策
- Avoid relying on specific basefee values; parameterize and allow updates via governance with delays.
- Use robust gas estimation and caps on external calls; avoid refund-dependent logic.
- Simulate on mainnet forks with varying basefee to validate liveness.

## 事例

### 脆弱
```solidity
require(tx.gasprice <= 30 gwei, "gas too high"); // fails as basefee rises
```

### 修正済み
```solidity
uint256 maxGasPrice = governanceMaxGasPrice;
require(tx.gasprice <= maxGasPrice, "gas too high"); // upgradable with delays
```
