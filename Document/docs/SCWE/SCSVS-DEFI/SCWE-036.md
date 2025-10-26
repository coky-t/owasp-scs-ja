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
- **徹底的にテストする:** 広範なテストを実施し、操作がガス制限内に収まるようにします。

## 事例
- **Inadequate Gas Handling**
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

**Issue:** Processes large arrays in a single transaction, which can fail due to out-of-gas errors.

Why is this a problem?
- If recipients.length is too large, the transaction fails.
- Attackers can exploit this by submitting large recipient lists, causing a DoS attack.

- **Adequate Gas Handling**
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
✔️ Fix: Uses gasleft() to gracefully exit before running out of gas, ensuring some transfers complete.
Why is this better?
✅ Prevents complete transaction failure by handling only as many iterations as gas allows.
✅ Reduces DoS risk by allowing partial execution instead of reverting everything.
✅ Enables retrying to complete all operations over multiple calls.

---
