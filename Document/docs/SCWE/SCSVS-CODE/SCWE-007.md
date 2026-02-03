---
title: 未使用変数の存在 (Presence of Unused Variables)
id: SCWE-007
alias: unused-variables
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-2]
  cwe: [563]
status: new
---

## 関連
- CWE-563: Assignment to Variable with No Effect  
  [https://cwe.mitre.org/data/definitions/563.html](https://cwe.mitre.org/data/definitions/563.html)

## 説明
スマートコントラクトにおける未使用変数の存在は、宣言されているもののコントラクトロジックで利用されることのない変数を指します。これらの変数はストレージやメモリを不必要に消費し、デプロイ時や実行時にガスを無駄にする可能性があります。この状況は、不完全なコード、忘れられた変数、将来使用することを想定していたものの実装されていないコードにより発生することがよくあります。そのような変数の存在は、コントラクトの理解を困難にすることにより攻撃対象領域を拡大し、潜在的な脆弱性を生み出します。

よくあるリスクには以下があります。
- ストレージやメモリの消費によるガスの浪費。
- 複雑さが増し、コントラクトの動作を理解することが困難になる。
- 監査担当者や将来コントラクトに取り組む開発者に混乱を招く可能性がある。

未使用変数は、ロジックエラーを隠したり、コントラクトの一部が意図したとおりに機能していないことを示唆することもあります。

## 対策
- **Remove Unused Variables:** Ensure that any variables that are not required for the contract’s functionality are removed.
- **Code Review and Refactoring:** Regularly review and refactor the code to eliminate dead or unnecessary variables.
- **Automated Static Analysis Tools:** Use static analysis tools to detect unused variables and other unnecessary code patterns.

## 事例

### 未使用変数のあるコントラクト

```solidity
pragma solidity ^0.4.0;

contract UnusedVariables {
    uint public balance;
    uint public unusedVariable; // This variable is not used anywhere

    function deposit(uint amount) public {
        balance += amount;
    }
    
    function withdraw(uint amount) public {
        balance -= amount;
    }
}
```

In the example above, the `unusedVariable` is declared but never used within the contract. This is a waste of storage space and can confuse anyone reading or auditing the code.


### 未使用変数を削除した修正済みコード
```solidity
pragma solidity ^0.4.0;

contract FixedUnusedVariables {
    uint public balance;

    function deposit(uint amount) public {
        balance += amount;
    }
    
    function withdraw(uint amount) public {
        balance -= amount;
    }
}

```
The improved contract removes the unnecessary `unusedVariable`, reducing the complexity and improving gas efficiency.
