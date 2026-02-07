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
- **未使用変数を削除する:** コントラクトの機能に必要ではないすべての変数を削除します。
- **コードレビューとリファクタリング:** 定期的にコードをレビューしてリファクタし、デッド変数や不要な変数を排除します。
- **自動静的解析ツール:** 静的解析ツールを使用して、未使用変数やその他の不要なコードパターンを検出します。

## 事例

### 未使用変数のあるコントラクト

```solidity
pragma solidity ^0.4.0;

contract UnusedVariables {
    uint public balance;
    uint public unusedVariable; // This variable is not used anywhere

    function deposit(uint amount) public payable {
        require(msg.value == amount, "ETH amount mismatch");
        balance += amount;
    }
    
    function withdraw(uint amount) public {
        balance -= amount;
    }
}
```

上記の例では、`unusedVariable` が宣言されていますが、コントラクト内で使用されることはありません。これはストレージスペースの無駄であり、コードを読んだり監査する人を混乱させる可能性があります。


### 未使用変数を削除した修正済みコード
```solidity
pragma solidity ^0.4.0;

contract FixedUnusedVariables {
    uint public balance;

    function deposit(uint amount) public payable {
        require(msg.value == amount, "ETH amount mismatch");
        balance += amount;
    }
    
    function withdraw(uint amount) public {
        require(balance >= amount, "Insufficient balance");
        balance -= amount;
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success, "Transfer failed");
    }
}

```
改良したコントラクトは不要な `unusedVariable` を削除し、複雑さを軽減してガス効率を改善します。
