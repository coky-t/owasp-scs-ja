---
title: アップグレードでのストレージレイアウトの衝突 (Storage Layout Collision on Upgrade)
id: SCWE-099
alias: storage-layout-collision
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [664]
status: new
---

## 関連
- CWE-664: Improper Control of a Resource Through its Lifetime  
  [https://cwe.mitre.org/data/definitions/664.html](https://cwe.mitre.org/data/definitions/664.html)

## 説明
アップグレード可能なコントラクトは安定したストレージスロットに依存しています。バージョン間での状態変数の順序変更、削除、挿入 (あるいは継承順序の変更) は、プロキシが同じスロットを再使用する際にストレージの衝突を生じ、残高、ロール、または設定を破損します。

## 対策
- 変数の順序を固定し、新しい変数は末尾追加のみとします。
- 将来的な拡張に備えてギャップ (`uint256[50] private __gap;`) を確保します。
- 自動化されたストレージレイアウトの差分比較を使用し、アップグレード安全なパターン (OZ Upgradeable ツールなど) に従います。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract V1 {
    address public owner;   // slot 0
    uint256 public balance; // slot 1
}

contract V2 is V1 {
    uint256 public balance; // reuses slot 1, corrupts state
    address public treasury;
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract V1 {
    address public owner;   // slot 0
    uint256 public balance; // slot 1
    uint256[48] private __gap;
}

contract V2 is V1 {
    uint256 public treasuryFee; // slot 2 (after gap)
}
```
