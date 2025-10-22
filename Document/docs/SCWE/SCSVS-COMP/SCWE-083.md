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
コントラクトの開発時にすべてのエッジケースが適切に考慮され、処理されていることを確保します。`require` と `assert` を利用して、入力、出力、状態の適切なバリデーションを確保します。
- 考えられるすべてのエッジケースに対して明示的なバリデーションを実装します。
- Solidity に組み込まれている安全機能 (SafeMath、Solidity 0.8 オーバーフローチェックなど) を使用します。
- require/assert ステートメントを使用して、入力制約と想定される状態を強制します。
- ストレステスト、ファジング、不変テストなど、コントラクトを徹底的にテストします。



### 脆弱なコントラクトの例 - (算術演算の問題)
```solidity
contract Example {
    function divide(uint256 _numerator, uint256 _denominator) public pure returns (uint256) {
        return _numerator / _denominator; // Division by zero is not checked
    }
}
```
### 修正したコントラクトの例
- 解決策: `require` を使用して、ゼロ除算をチェックします。
```solidity
contract Example {
    function divide(uint256 _numerator, uint256 _denominator) public pure returns (uint256) {
        require(_denominator != 0, "Cannot divide by zero");
        return _numerator / _denominator;
    }
}
```

### 空の配列の処理
- 問題: 関数は配列が空ではないことを想定しており、out-of-bounds エラーにつながります。

```solidity
contract Example {
    uint256[] public data;

    function getLast() public view returns (uint256) {
        return data[data.length - 1]; // ❌ Reverts if array is empty
    }
}
```

### 修正したコード:
- 解決策: 要素にアクセスする前に、配列が空でないことを検証します。
```solidity
contract Example {
    uint256[] public data;

    function getLast() public view returns (uint256) {
        require(data.length > 0, "Array is empty");
        return data[data.length - 1];
    }
}
```
