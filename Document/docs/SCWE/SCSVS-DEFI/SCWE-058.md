---
title: ブロックガス制限での DoS (DoS with Block Gas Limit)
id: SCWE-058
alias: dos-block-gas-limit
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
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)

## 説明
ブロックガス制限でのサービス拒否 (DoS) は、コントラクトの実行が大量のガスに依存するように設計されており、ブロックガス制限を超える可能性がある場合に発生します。これは、トランザクションが失敗し、コントラクトが利用不可能または使用不可能になる可能性があります。攻撃者は、過剰なガスを消費するトランザクションを作成することでこの脆弱性を悪用し、実質的にコントラクトをロックしたり、正常な操作を妨害する可能性があります。

## 対策
To mitigate this vulnerability, ensure that operations that depend on gas consumption are efficient and that gas limits are taken into account when designing contract logic. Avoid functions that require large amounts of gas to complete, and consider implementing features like batching or chunking operations to spread the gas usage across multiple transactions.

### 脆弱なコントラクトの例
```solidity
contract GasLimitDoS {
    uint256[] public data;

    function addData(uint256[] memory newData) public {
        for (uint256 i = 0; i < newData.length; i++) {
            data.push(newData[i]);  // Can consume a large amount of gas if the array is large
        }
    }
}
```
### 修正したコントラクトの例
```solidity
contract GasLimitSafe {
    uint256[] public data;

    function addData(uint256[] memory newData) public {
        uint256 batchSize = 100;  // Limit the batch size to avoid excessive gas usage
        for (uint256 i = 0; i < newData.length && i < batchSize; i++) {
            data.push(newData[i]);
        }
    }
}
```
