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
- Avoid unsafe downcasts from larger to smaller types unless explicitly required.  
- Always validate the range before converting between numerical types.  
- Use explicit conversion functions where applicable, ensuring proper handling of edge cases.  

### 脆弱なコントラクトの例
```solidity
contract Example {
    function unsafeDowncast(uint256 value) public pure returns (uint8) {
        return uint8(value);  // ❌ Truncates value if > 255
    }

    function unsafeAddressToUint(address addr) public pure returns (uint256) {
        return uint256(uint160(addr));  // ❌ May lead to unexpected behaviors
    }
}
```
**Why is this vulnerable?**

- `uint8(value)` will silently truncate values above 255, leading to unintended loss of data.
- `uint256(uint160(addr))` might be used incorrectly in arithmetic operations, potentially allowing address manipulation.

### 修正したコントラクトの例

```solidity
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
**Why is this safe?**

- Ensures that values do not exceed the allowed range before downcasting.
- Re- stricts type conversion to avoid security risks from improper arithmetic operations.
- Prevents silent data loss that could lead to unintended contract behavior.

**By enforcing safe type conversions, developers can ensure contract logic remains reliable and free from unexpected truncation issues.**
