---
title: 過度なコントラクトの複雑さ (Excessive Contract Complexity)
id: SCWE-002
alias: excessive-complexity
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [710]
status: new
---

## 関連
- CWE-710: Improper Adherence to Coding Standards  
  [https://cwe.mitre.org/data/definitions/710.html](https://cwe.mitre.org/data/definitions/710.html)

## 説明
過度なコントラクトの複雑さは、スマートコントラクトの設計または実装が過度に複雑になり、理解、監査、保守が困難になる状況を指します。そのような複雑さは開発時やテスト時には顕在化しないバグや脆弱性をもたらす可能性を高めます。多くの場合、以下の場合に発生します。

- 複雑な継承構造や外部ライブラリの過剰な使用。
- コントラクト内の多すぎる関数、相互依存関係、条件。
- 簡素化できるはずの不必要に複雑なロジック。

コードが複雑になるほど、特に将来の開発者や監査担当者がコントラクトとやり取りする必要がある場合に、ミスが発生する可能性が高くなります。複雑さは時間経過とともに悪用される可能性のある隠れた脆弱性につながる可能性があります。

## 対策
- **コードをリファクタして簡素化する**: 複雑な関数をより小さく理解しやすいコンポーネントに分割します。
- **継承の深さを制限する**: 継承を慎重に使用し、深い継承チェーンよりもコンポジションを優先します。
- **明確で説明的な名前を使用する**: 関数、変数、イベントはその目的を示す自己説明的な名前を付ける必要があります。
- **冗長なロジックを避ける**: 繰り返されるロジックを再使用可能な関数またはライブラリに統合します。
- **モジュール性を確保する**: コントラクトを特定のタスクに焦点を当てた管理しやすいモジュールに分割し、可読性と保守性を向上します。

## 事例

### 過度な複雑さ

```solidity
pragma solidity ^0.8.0;

contract ComplexContract {
    uint public balance;
    address public owner;

    function ComplexFunction1(uint value) public {
        // Complex logic with many conditions
        if (value > 10) {
            // do something
        }
        // Multiple nested functions and too many conditions
        for (uint i = 0; i < value; i++) {
            if (i % 2 == 0) {
                // nested loop
            }
        }
    }

    function ComplexFunction2(address addr) public {
        // Complex logic with external dependencies
        SomeLibrary.someFunction(addr);
    }
}
```

### 簡略版

```solidity
pragma solidity ^0.8.0;

contract SimpleContract {
    uint public balance;
    address public owner;

    function deposit(uint value) public {
        balance += value;
    }

    function withdraw(uint value) public {
        require(balance >= value, "Insufficient funds");
        balance -= value;
        // Note: Simplified example — internal accounting only. For ETH vaults, add payable(msg.sender).call{value: value}("") and require(success).
    }
}
```
