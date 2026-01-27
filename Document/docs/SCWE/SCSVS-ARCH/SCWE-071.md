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

**Why is this vulnerable?**
- `uninitializedPointer` is a storage pointer but is not assigned a reference to valid storage.
- Writing to `uninitializedPointer.push(10);` could overwrite unintended storage locations.

**Potential outcomes:**
- Corrupting storage layout (e.g., overwriting contract variables).
- Unexpected behaviors due to storage reallocation.

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

**Why is this safe?**
- `initializedPointer` explicitly references `data`, ensuring that it does not point to an arbitrary storage slot.
- Prevents unintended overwrites of contract state.
- Ensures expected behavior and protects contract integrity.
