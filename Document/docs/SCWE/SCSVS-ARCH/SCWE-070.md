---
title: 不正なコンストラクタ名 (Incorrect Constructor Name)
id: SCWE-070
alias: incorrect-constructor-name
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [1001]
status: new
---

## 関連
- CWE-1001: Variable Shadowing  
  [https://cwe.mitre.org/data/definitions/1001.html](https://cwe.mitre.org/data/definitions/1001.html)

## 説明
Solidity では、コンストラクタは、デプロイ時にコントラクトの状態変数を初期化するために使用される特別な関数です。コンストラクタは正しく名前付けされていないと、期待通りに機能せず、状態変数の初期化に失敗したり、予期しない動作をトリガーすることにつながります。コンストラクタはコントラクトの正確な名前を持ち、戻り型がないようにしなければなりません。

コンストラクタ名が正しくない場合、意図したとおりに実行されず、コントラクトが期待通りに動作しない可能性があり、初期化されていない状態や一貫性のない状態のままになる可能性があります。

## 対策
Ensure that the constructor has the correct name, which must match the contract name and contain no return type. In newer versions of Solidity (0.4.22 and later), the constructor keyword is used instead of the contract name for constructor functions.

### 脆弱なコントラクトの例
```solidity
contract Example {
    uint public value;

    // Incorrect constructor name (for Solidity <0.4.22)
    function Example() public {  // Constructor name must match the contract name in older Solidity versions
        value = 10;
    }
}
```

### 修正したコントラクトの例
```solidity
contract Example {
    uint public value;

    // Correct constructor definition (Solidity >=0.4.22)
    constructor() public {  // Use "constructor" instead of the contract name
        value = 10;
    }
}
```
