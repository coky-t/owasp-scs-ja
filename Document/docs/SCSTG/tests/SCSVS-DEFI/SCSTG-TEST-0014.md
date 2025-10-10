---
id: SCSTG-TEST-0014
scsvs_cg_id:
- SCSVS-DEFI
scsvs_scg_id:
- SCSVS-DEFI-1
platform: []
title: ループ内のガス使用のテスト (Test Gas Usage in Loops)
scsvs_cg_levels:
- L2
tests:
- SCSTG-TEST-0014
---

### **説明**
効率的なガス使用は、スマートコントラクト開発において、トランザクションが費用対効果を高め、ブロックガス制限を超えないようにするために不可欠です。最適化が不十分なコントラクトは、トランザクションコストの上昇、トランザクションの失敗、潜在的なサービス拒否 (DoS) 攻撃をもたらす可能性があります。ガス使用を最適化することは、スマートコントラクトがスケーラブルであり、費用対効果を高め、分散型アプリケーション (dApps) 全体のセキュリティ維持に役立ちます。このテストは、ガス使用が適切に最適化され、コントラクトが Ethereum のガス制限の制約内で効率的に動作するように設計されていることを確認することに重点を置いています。

---

### **テスト 1: ループ内のガス使用を検証する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.8.0;

contract GasInefficient {
    uint256[] public data;

    function addData(uint256[] memory newData) public {
        for (uint i = 0; i < newData.length; i++) {
            data.push(newData[i]);  // Inefficient loop causing high gas costs
        }
    }
}
```

#### **なぜ脆弱なのか**
- このコントラクトはループを使用して入力配列を反復処理し、データを動的配列にプッシュします。
- ガスコストは入力配列のサイズに比例して増加します。大規模なデータセットでは、過剰なガス使用につながり、トランザクションがブロックガス制限を超えてしまう可能性があります。
- 非効率なループで動的に増加するスマートコントラクトは、大規模なデータセットとやり取りする際に、管理不能となる可能性があります。


#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract OptimizedGas {
    uint256[] public data;

    function addData(uint256[] memory newData) public {
        uint256 length = newData.length;
        for (uint i = 0; i < length; i++) {
            data.push(newData[i]);  // Optimized by storing array length outside the loop
        }
    }
}
```

#### **チェック方法**
- **コードレビュー:** 動的なデータ構造を扱うループや関数がガス使用に最適化されていることを確認します。単一トランザクション内の不要な状態変更や過剰な反復処理を探します。
- **ガス推定:** eth-gas-reporter や Remix IDE などのツールを使用して、最適化前後のガス使用を推定します。
- **動的テスト:** さまざまな入力サイズでコントラクトをテストし、適切なガス制限内で動作することをチェックして、ブロックガス制限を超えないことを確認します。
