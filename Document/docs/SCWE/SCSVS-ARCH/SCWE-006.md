---
title: 一貫性のない継承階層 (Inconsistent Inheritance Hierarchy)
id: SCWE-006
alias: inconsistent-inheritance-hierarchy
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-3]
  cwe: [710]
status: new
---

## 関連
- CWE-710: Improper Adherence to Coding Standards  
  [https://cwe.mitre.org/data/definitions/710.html](https://cwe.mitre.org/data/definitions/710.html)

## 説明
一貫性のない継承階層は、コントラクトが複数のコントラクトまたはライブラリから一貫性のないあるいは矛盾した方法で継承する場合に発生します。これは、関数実装、状態変数、アクセス制御の競合により、予期しない動作やエラーにつながります。適切で一貫性のある継承階層は明確なロジックと予測可能なコントラクト実行に不可欠です。

継承階層のよくある問題には以下を含みます。
- 曖昧な関数オーバーライド。
- 変数定義の競合。
- 多重継承の誤用。ダイヤモンド問題につながる可能性があります。

一貫性のない継承は、特に類似した関数名を持つ複数のコントラクトまたはライブラリが使用されている場合、コントラクトの機能とセキュリティを大幅に損なう可能性があります。

## 対策
- **継承構造を明確にする:** 継承構造が論理的に構成されており、機能が重複したり、状態変数が競合することがないことを確認します。
- **明示的なオーバーライドを使用する:** 必要に応じて関数を明確にオーバーライドして曖昧さを避け、意図しない関数マスキングがないようにします。
- **単一継承または制御された多重継承を優先する:** 過度に複雑な継承構造を避けます。多重継承が必要な場合は、Diamond Problem Resolver などの設計パターン (インタフェースや抽象コントラクトなど) の使用を検討します。
- **定期的に監査する:** 継承構造を定期的に監査し、一貫性のなさを早期に特定します。

## 事例

### 一貫性のない継承での脆弱なコントラクト

```solidity
pragma solidity ^0.4.0;

contract ParentA {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}

contract ParentB {
    uint public value;

    function setValue(uint _value) public {
        value = _value * 2;
    }
}

contract Child is ParentA, ParentB {
    function setValue(uint _value) public {
        // Ambiguity: which `setValue` should be called?
        ParentA.setValue(_value);
    }
}
```
In the above example, the `Child` contract inherits from both `ParentA` and `ParentB`. Both parent contracts define the `setValue` function. There is no clear indication of which function should be called, leading to ambiguity and potential bugs or undesired behavior.

### 一貫した継承でのコントラクトに修正したもの
```solidity
pragma solidity ^0.4.0;

contract ParentA {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}

contract ParentB {
    uint public value;

    function setValue(uint _value) public {
        value = _value * 2;
    }
}

contract Child is ParentA {
    // Clear function override, only inherits from one parent
    function setValue(uint _value) public {
        ParentA.setValue(_value);
    }
}
```
In the fixed version of the contract, the `Child` contract only inherits from one parent (`ParentA`), which resolves the ambiguity. If both `ParentA` and `ParentB` were needed, explicit overriding or more careful contract design would be required to ensure the correct functionality.
