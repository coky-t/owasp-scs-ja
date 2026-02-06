---
title: 不正な型変換 (Incorrect Type Conversion)
id: SCWE-080
alias: incorrect-type-conversion
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-2]
  cwe: [704]
status: new
---

## 関連
- **CWE-704: Incorrect Type Conversion or Cast**  
  [https://cwe.mitre.org/data/definitions/704.html](https://cwe.mitre.org/data/definitions/704.html)

## 説明
不正な型変換は、互換性のない型間でキャストまたは暗黙的に変換される場合に発生し、精度低下、予期しない動作、セキュリティ脆弱性につながる可能性があります。Solidity は特定の暗黙的な変換 (例: `uint256` から `uint8` へ) を許可しており、データの暗黙的な切り捨てにつながる可能性があります。さらに、`address` と `uint` などの型間のキャストは予期しないセキュリティリスクにつながる可能性があります。

## 対策
- 明示的に要求されない限り、大きい型から小さい型への安全でないダウンキャストを避けます。
- 数値型間で変換する前に常に範囲を検証します。
- 適用できる場合には明示的な変換関数を使用し、エッジケースの適切な処理を確保します。

### 脆弱なコントラクトの例
```solidity
pragma solidity ^0.8.0;

contract Example {
    function unsafeDowncast(uint256 value) public pure returns (uint8) {
        return uint8(value);  // ❌ Truncates value if > 255
    }

    function unsafeAddressToUint(address addr) public pure returns (uint256) {
        return uint256(uint160(addr));  // ❌ May lead to unexpected behaviors
    }
}
```
**なぜこれが脆弱なのか？**

- `uint8(value)` は 255 を超える値を暗黙的に切り捨て、いとしないデータの損失につながります。
- `uint256(uint160(addr))` は算術演算で誤って使用される可能性があり、アドレス操作を許可する可能性があります。

### 修正したコントラクトの例

```solidity
pragma solidity ^0.8.0;

contract SecureExample {
    function safeDowncast(uint256 value) public pure returns (uint8) {
        require(value <= type(uint8).max, "Value exceeds uint8 range");  // ✅ Ensure valid range
        return uint8(value);
    }

    function safeAddressToUint(address addr) public pure returns (uint160) {
        return uint160(addr);  // ✅ Restrict conversion to valid 160-bit range
    }
}
```
**なぜこれが安全なのか？**

- ダウンキャスト前に値が許容範囲を超えないことを確保しています。
- 型変換を制限し、不適切な算術演算からのセキュリティリスクを避けています。
- 意図しないコントラクトの動作につながる可能性のある暗黙的なデータ損失を防いでいます。

**安全な型変換を強制することで、開発者はコントラクトロジックが信頼性を維持し、予期しない切り捨ての問題を回避できます。**
