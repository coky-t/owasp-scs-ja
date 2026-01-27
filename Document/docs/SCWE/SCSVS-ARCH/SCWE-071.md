---
title: 初期化されていないストレージポインタ (Uninitialized Storage Pointer)
id: SCWE-071
alias: uninitialized-storage-pointer
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [457]
status: new
---

## 関連
- CWE-457: Use of Uninitialized Variable  
  [https://cwe.mitre.org/data/definitions/457.html](https://cwe.mitre.org/data/definitions/457.html)

## 説明
Solidity での初期化されていないストレージポインタは、宣言されているが、使用前に値を割り当てられていない変数を指します。この変数はコントラクトのストレージ内の意図しない場所を指す可能性があるため、予測できない動作を引き起こす可能性があり、その結果、機密データをさらしたり、攻撃者が未初期化ポインタを悪用できる恐れがあります。これは重大な問題です。Solidity はストレージ変数を自動的に初期化しないため、安全でない可能性のあるデフォルト値が残ってしまうためです。

## 対策
ストレージポインタを常に初期化して、潜在的な脆弱性を避けます。すべての変数、特にストレージポインタ、は使用前に適切に値を割り当てられていることを確保します。これはコントラクトのストレージから初期化されていないデータやゴミデータへのアクセスを防ぎます。

### 脆弱なコントラクトの例
```solidity
contract Example {
    uint[] public data;

    // Uninitialized storage pointer, could lead to unexpected behavior
    function addData(uint _value) public {
        data.push(_value);
        uint[] storage uninitializedPointer;  // Pointer is uninitialized
        uninitializedPointer.push(10);  // Accessing uninitialized storage pointer
    }
}
```

**なぜこれが脆弱なのか？**
- `uninitializedPointer` はストレージポインタですが、有効なストレージへの参照を割り当てられていません。
- `uninitializedPointer.push(10);` への書き込みは意図しないストレージ位置を上書きする可能性があります。

**潜在的な結果:**
- ストレージレイアウトの破損 (例: コントラクト変数の上書き)。
- ストレージの再割り当てによる予期しない動作。

### 修正したコントラクトの例
```solidity
  contract Example {
      uint[] public data;

      function addData(uint _value) public {
          data.push(_value);
          uint[] storage initializedPointer = data;  // ✅ Properly initialized storage pointer
          initializedPointer.push(10);  // ✅ Safely interacts with the intended storage
      }
  }
```

**なぜこれが安全なのか？**
- `initializedPointer` は `data` を明示的に参照し、任意のストレージスロットを指し示すことはありません。
- コントラクト状態の意図しない上書きを防ぎます。
- 期待される動作を確保し、コントラクトの完全性を保護します。
