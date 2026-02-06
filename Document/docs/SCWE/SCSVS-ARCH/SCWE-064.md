---
title: 不正な継承順位 (Incorrect Inheritance Order)
id: SCWE-064
alias: incorrect-inheritance-order
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-2]
  cwe: [1001]
status: new
---

## 関連
- CWE-1001: Variable Shadowing  
  [https://cwe.mitre.org/data/definitions/1001.html](https://cwe.mitre.org/data/definitions/1001.html)

## 説明
Solidity での不正な継承順位は、特に複数のベースコントラクトが類似の変数や関数を定義している場合、意図しない動作につながる可能性があります。Solidity の継承順位の線形化は、あるコントラクトが親コントラクトで定義された変数や関数を意図せず上書きまたはシャドウイングしてしまい、混乱、エラー、潜在的な脆弱性につながる可能性があります。

Solidity では、継承の順位が重要です。継承階層が適切に構成されていない場合、変数や関数の誤ったバージョンが呼び出され、バグやセキュリティ上の問題を引き起こす可能性があります。

## 対策
この脆弱性を緩和するには、継承構造を慎重にレビューして順位付けします。親コントラクトが論理的な順序で継承され、変数や関数が意図せずシャドウされたりオーバーライドされることがないことを確保します。明確で一貫性のある継承パターンに従うことを検討します。

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract BaseA {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}

contract BaseB {
    uint public value;

    function setValue(uint _value) public {
        value = _value + 1;  // Different implementation
    }
}

contract Child is BaseB, BaseA {
    function setValue(uint _value) public {
        value = _value + 2;  // Shadows value from BaseA or BaseB
    }
}
```

### 修正したコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract BaseA {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}

contract BaseB {
    uint public value;

    function setValue(uint _value) public {
        value = _value + 1;  // Different implementation
    }
}

contract Child is BaseA, BaseB {
    function setValue(uint _value) public {
        value = _value + 2;  // Calls setValue from BaseA or BaseB intentionally
    }
}
```
