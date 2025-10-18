---
title: エッジケースの処理の失敗 (Failure to Handle Edge Cases)
id: SCWE-083
alias: failure-to-handle-edge-cases
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMP]
  scsvs-scg: [SCSVS-COMP-2]
  cwe: [754]
status: new
---

## 関連
- CWE-754: Improper Check for Unusual or Exceptional Conditions
  [https://cwe.mitre.org/data/definitions/754.html](https://cwe.mitre.org/data/definitions/754.html)

## 説明
エッジケースを考慮しないと、予期しない動作、セキュリティ脆弱性、意図しないコントラクト障害をもたらす可能性があります。エッジケースは、開発者が見落としがちな、通常とは異なる可能性がある入力や状態であり、正しくないロジック、リバート、さらにエクスプロイトにつながります。

### 一般的なシナリオは以下のとおりです:

- 算術演算の問題 (例: ゼロ除算、整数オーバーフロー/アンダーフロー、精度の損失)。
- 境界条件 (例: 空配列の処理、最小値/最大値)。
- 状態依存のバグ (例: 未初期化変数のチェックの失敗、無効な状態遷移)。
- ガス関連の障害 (例: 無制限ループでの過剰なガス消費)。

## 対策
Ensure that all edge cases are properly considered and handled during the development of the contract. Utilize `require` and `assert` to ensure proper validation of inputs, outputs, and states.
- Implement explicit validation for all possible edge cases.
- Use Solidity's built-in safety features (e.g., SafeMath, Solidity 0.8 overflow checks).
- Use require/assert statements to enforce input constraints and expected states.
- Test contracts thoroughly, including stress tests, fuzzing, and invariant testing.



### 脆弱なコントラクトの例 - (算術演算の問題)
```solidity
contract Example {
    function divide(uint256 _numerator, uint256 _denominator) public pure returns (uint256) {
        return _numerator / _denominator; // Division by zero is not checked
    }
}
```
### 修正したコントラクトの例
- Solution: Use `require` to check for division by zero.
```solidity
contract Example {
    function divide(uint256 _numerator, uint256 _denominator) public pure returns (uint256) {
        require(_denominator != 0, "Cannot divide by zero");
        return _numerator / _denominator;
    }
}
```

### 空の配列の処理
- Issue: A function assumes that the array is never empty, leading to an out-of-bounds error.

```solidity
contract Example {
    uint256[] public data;

    function getLast() public view returns (uint256) {
        return data[data.length - 1]; // ❌ Reverts if array is empty
    }
}
```

### 修正したコード:
-  Solution: Validate that the array is not empty before accessing elements.
```solidity
contract Example {
    uint256[] public data;

    function getLast() public view returns (uint256) {
        require(data.length > 0, "Array is empty");
        return data[data.length - 1];
    }
}
```
