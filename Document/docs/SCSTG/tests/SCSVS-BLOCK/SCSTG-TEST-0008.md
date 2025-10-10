---
id: SCSTG-TEST-0008
scsvs_cg_id:
- SCSVS-BLOCK
scsvs_scg_id:
- SCSVS-BLOCK-1
platform: []
title: 効率的なループと関数の設計 (Efficient Loop and Function Design)
scsvs_cg_levels:
- L1
tests: SCSTG-TEST-0008
---

### **説明**
スマートコントラクトにおける非効率なループと関数の設計は過剰なガス消費をもたらし、ガス不足エラーやサービス拒否 (DoS) 脆弱性につながる可能性があります。スマートコントラクトのセキュリティ監査担当者は、すべてのループと関数が Ethereum のブロックガス制限内で動作するように最適化されていることを確認する必要があります。

### **テスト 1: 無制限ループを検出する**

#### **目的**
コントラクトコードでの無制限のループの使用は、過剰なガス使用に脆弱であるため、識別して緩和します。

#### 脆弱なコード:
```solidity
pragma solidity ^0.8.0;

contract UnboundedLoopExample {
    uint[] public data;

    function processAll() public {
        for (uint i = 0; i < data.length; i++) {
            // This operation scales with the size of the array
            data[i] = data[i] + 1;
        }
    }
}
```

#### **なぜ脆弱なのか**

ループは、無限に大きくなる可能性のある、`data` 配列全体を反復処理します。
- 大規模な配列では、ガス消費量がブロックガス制限を超え、トランザクションが失敗する可能性があります。
- 攻撃者は配列を埋めることでこれを悪用し、正当なユーザーがこの関数を使用できなくなる可能性があります。

#### **チェック方法**
- **コードレビュー:** サイズ制約のない動的配列やマッピングに対して動作する `for` ループや `while` ループを探します。
- **動的入力テスト:** 大規模なデータセットで関数をテストし、ガス制限付近での動作をシミュレートします。
- **ドキュメントのレビュー:** コントラクトがデータ増加と関数使用に関する制約を指定していることを確認します。


#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract BoundedLoopExample {
    uint[] public data;

    function processBatch(uint start, uint end) public {
        require(end <= data.length, "End index out of bounds");
        for (uint i = start; i < end; i++) {
            data[i] = data[i] + 1;
        }
    }
}
```

---

### **テスト 2: 非効率なネストされたループを特定する**

#### **目的**
- ガス消費量を指数関数的に増加する非効率なネストされたループを検出して対処します。


#### 脆弱なコード:

```solidity
pragma solidity ^0.8.0;

contract NestedLoopExample {
    uint[][] public matrix;

    function processMatrix() public {
        for (uint i = 0; i < matrix.length; i++) {
            for (uint j = 0; j < matrix[i].length; j++) {
                matrix[i][j] = matrix[i][j] * 2;
            }
        }
    }
}
```

#### **なぜ脆弱なのか**

ネストされたループは操作の複雑さを増し、入力サイズの増加に伴いガスコストの高騰につながります。
- 大規模なデータセットはガス制限内で関数を使用できなくなり、DoS 状態を発生する可能性があります。

#### **チェック方法**
- **コードレビュー:** コード内のネストされたループを調べ、入力サイズに対するガス消費量を評価します。
- **ガスプロファイリング:** `eth-gas-reporter` などのツールを使用して、テスト時のガス使用量を解析します。
- **動的テスト:** 大きな `matrix` サイズでのシナリオをシミュレートし、ストレス下でのコントラクトの動作を観察します。

```solidity
pragma solidity ^0.8.0;

contract OptimizedNestedLoopExample {
    uint[][] public matrix;

    function processMatrixBatch(uint startRow, uint endRow) public {
        require(endRow <= matrix.length, "End row exceeds matrix size");
        for (uint i = startRow; i < endRow; i++) {
            for (uint j = 0; j < matrix[i].length; j++) {
                matrix[i][j] = matrix[i][j] * 2;
            }
        }
    }
}
```
