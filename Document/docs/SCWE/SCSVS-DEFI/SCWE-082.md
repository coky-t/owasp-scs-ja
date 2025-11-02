---
title: 適切なガス管理の欠如 (Lack of Proper Gas Management)
id: SCWE-082
alias: lack-of-proper-gas-management
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
ガス管理は、スマートコントラクトにおいて、ガス不足や過剰消費にならないようにするために極めて重要です。ガス消費が適切に管理されていない場合、コントラクトは実行に失敗したり、攻撃者に悪用されてサービス拒否 (DoS) を引き起こす可能性があります。

## 対策
関数に必要なガスを適切に推定して、適切なガス制限を設定します。`require` などのメカニズムを使用して、ガス消費の失敗に対処し、ガス使用が許容範囲内に収まるようにします。

### 脆弱なコントラクトの例
```solidity
contract Example {
    function execute() public {
        while (true) { 
            // Excessive gas consumption, no limit set
        }
    }
}
```

### 修正したコントラクトの例
```solidity
contract Example {
    uint public counter;

    function execute(uint _iterations) public {
        require(_iterations <= 100, "Too many iterations"); // Limit iterations to avoid excessive gas usage
        for (uint i = 0; i < _iterations; i++) {
            counter++;
        }
    }
}
```
