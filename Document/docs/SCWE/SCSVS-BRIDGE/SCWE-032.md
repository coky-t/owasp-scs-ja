---
title: ブロックガス制限への依存 (Dependency on Block Gas Limit)
id: SCWE-032
alias: dependency-on-block-gas-limit
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-2]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption  
  [CWE-400 Link](https://cwe.mitre.org/data/definitions/400.html)

## 説明
ブロックガス制限への依存はコントラクト操作において Ethereum ブロックガス制限に依存することを指し、以下につながる可能性があります。
- ガス制限を超えた場合のトランザクションの失敗。
- ガスを大量消費する操作における脆弱性の悪用。
- 資産やデータの喪失。

## 対策
- **ガス使用を最適化する:** コントラクト操作におけるガス消費を最小限に抑えます。
- **無制限のループを回避する:** ループに固定の上限があることを確保します。
- **徹底的にテストする:** 広範なテストを実施して、操作がガス制限内に収まるように確保します。

## 事例
- **ガスを大量消費する操作 - 大規模な動的データの保存**
    ```solidity
    pragma solidity ^0.8.0;

    contract GasIntensive {
        uint[] public largeArray;

        function appendData(uint[] memory data) public {
            for (uint i = 0; i < data.length; i++) {
                largeArray.push(data[i]); // Storing a large dataset in a single transaction
            }
        }
    }
    ```
- このコントラクトは単一のトランザクションで大きな配列を追加しますが、ブロックガス制限を超えると失敗します。
- 攻撃者はこれを悪用し、大量のガス消費を強制して、DoS 攻撃 (ストレージを非効率に満たすなど) を引き起こす可能性があります。

- **最適化したガス使用 - チャンク処理とガス効率**
    ```solidity
    pragma solidity ^0.8.0;

    contract GasOptimized {
        uint[] public largeArray;

        function appendData(uint[] memory data, uint start, uint end) public {
            require(end <= data.length, "Invalid range");
            for (uint i = start; i < end; i++) {
                largeArray.push(data[i]); // Process in controlled batches
            }
        }
    }
    ```

なぜこれがベターなのか？
- start と end パラメータは、単一呼び出しですべてを処理するのではなく、バッチ処理を可能にします。
- 複数のトランザクションに作業を分割することで、ブロックガス制限に到達することを防ぎます。
---
