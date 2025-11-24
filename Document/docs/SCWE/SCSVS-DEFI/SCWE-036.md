---
title: 不適切なガス制限処理 (Inadequate Gas Limit Handling)
id: SCWE-036
alias: inadequate-gas-limit-handling
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-1]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption  
  [CWE-400 Link](https://cwe.mitre.org/data/definitions/400.html)

## 説明
不適切なガス制限処理は、コントラクトがガス制約を効率的に管理できない場合に発生し、パフォーマンスのボトルネックやサービス拒否 (DoS) のリスクにつながります。不十分なガス管理は以下につながる可能性があります。
- 最適化されていない実行: 不要なガス不可の高い計算がコストを増加します。
- DoS 脆弱性: 過剰なガス使用によりトランザクションが失敗し、操作をブロックします。
- 非効率なバッチ処理: 過負荷のループやストレージ更新が out-of-gas (OOG) エラーを引き起こします。

プロトコルレベルのブロックガス制限に焦点を当てた **SCWE-032** とは異なり、この問題はスマートコントラクトレベルでの不十分なガス管理により発生します。


## 対策
- **ガス使用を最適化する:** コントラクト操作でのガス消費を最小限に抑えます。
- **境界のないループを避ける:** ループが固定の上限を持つようにします。
- **徹底的にテストする:** 広範なテストを実施して、操作がガス制限内に収まるようにします。

## 事例
- **不適切なガス処理**
    ```solidity
    pragma solidity ^0.8.0;

    contract InefficientProcessing {
        mapping(address => uint) public balances;

        function batchTransfer(address[] memory recipients, uint amount) public {
            for (uint i = 0; i < recipients.length; i++) {
                balances[recipients[i]] += amount; // Gas-intensive operation
            }
        }
    }
    ```

**問題点:** 単一のトランザクションで大きな配列を処理するため、out-of-gas エラーにより失敗する可能性があります。

なぜこれが問題なのか？
- recipients.length が大きすぎる場合、トランザクションは失敗します。
- 攻撃者はこれを悪用し、大きな受信者リストを送信して、DoS 攻撃を引き起こす可能性があります。

- **適切なガス処理**
    ```solidity
    pragma solidity ^0.8.0;

    contract GasOptimizedProcessing {
        mapping(address => uint) public balances;

        function batchTransfer(address[] memory recipients, uint amount) public {
            uint i = 0;
            while (i < recipients.length && gasleft() > 50000) { // Stop before out of gas
                balances[recipients[i]] += amount;
                i++;
            }
        }
    }
    ```
✔️ 修正内容: gasleft() を使用して、ガスがなくなる前に適切に終了し、一部の転送が完了するようにしています。  
なぜこれが適切なのか？  
✅ ガスが許容する回数分だけ反復処理することで、トランザクションの完全な失敗を防ぎます。  
✅ すべてを元に戻すのではなく、部分的な実行を許容することで DoS リスクを軽減します。  
✅ 複数の呼び出しにわたってすべての操作を完了するための再試行を可能にします。

---
