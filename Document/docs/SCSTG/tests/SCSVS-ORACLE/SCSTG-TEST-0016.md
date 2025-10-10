---
id: SCSTG-TEST-0016
scsvs_cg_id:
- SCSVS-ORACLE
scsvs_scg_id:
- SCSVS-ORACLE-1
platform: []
title: 算術と論理のセキュリティのテスト (Testing Arithmetic and Logic Security)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0016
---

### **説明**
算術オーバーフローとアンダーフローの防止は、スマートコントラクトの完全性とセキュリティを維持するために不可欠です。コントラクトが適切な保護なしで算術演算を実行すると、オーバーフローとアンダーフローの問題に対して脆弱になり、攻撃者がこれを悪用してコントラクトの動作を不正操作し、予期しない結果や資金の損失を引き起こす可能性があります。

---

### **テスト 1: 安全な数学演算が使用されていることを確認する**

#### 脆弱なコード:

```solidity
pragma solidity ^0.8.0;

contract UnsafeMath {
    uint256 public balance;

    function addFunds(uint256 _amount) public {
        balance += _amount;  // Possible overflow if balance + _amount exceeds max uint256 value
    }
}
```
#### **なぜ脆弱なのか**
このコントラクトは安全な数学ライブラリを使用せずに算術演算を実行しています。残高が uint256 の最大値 (2^256 - 1) を超えると、オーバーフローが発生します。
これは、予期しないコントラクトの動作や残高のリセットをもたらし、攻撃者がコントラクトを不正操作できる可能性があります。

#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract SafeMath {
    using SafeMath for uint256;
    uint256 public balance;

    function addFunds(uint256 _amount) public {
        balance = balance.add(_amount);  // Safe addition using SafeMath to prevent overflow
    }
}
```

#### **チェック方法**
- **コードレビュー:** すべての算術演算で SafeMath (古いバージョンの場合) などのライブラリを使用しているか、Solidity 0.8 で組み込まれたオーバーフローチェックに依存していることをチェックします。
- **テスト:** 大きな値を追加するなどのエッジケースを使用して、コントラクトがオーバーフローを防いでいるかどうかをチェックします。

---

### **テスト 2: 固定小数点算術の正しい使用を検証する**


#### 脆弱なコード:

```solidity
pragma solidity ^0.8.0;

contract FixedPointExample {
    uint256 public totalSupply;

    function calculateReward(uint256 _rewardPercentage) public view returns (uint256) {
        return totalSupply * _rewardPercentage / 100;  // Simple calculation without considering fixed-point precision
    }
}
```


#### **なぜ脆弱なのか**
この関数は固定小数点を考慮せずに除算を実行します。これは、特に小さなパーセンテージ値では、丸め誤差をもたらします。
整数除算を使用すると直ちに切り捨てにつながり、報酬計算に不正確さをもたらします。

#### 修正されたコード:

```solidity
pragma solidity ^0.8.0;

contract FixedPointExample {
    uint256 public totalSupply;
    uint256 constant FIXED_POINT = 1e18;  // Set a scaling factor for fixed-point precision

    function calculateReward(uint256 _rewardPercentage) public view returns (uint256) {
        return (totalSupply * _rewardPercentage * FIXED_POINT) / 100 / FIXED_POINT;  // Proper fixed-point arithmetic
    }
}

```

#### **チェック方法**
- **コードレビュー:** 固定小数点算術演算が適切なスケーリング係数 (1e18 など) を使用して実装され、精度の低下を回避していることを検証します。
- **動的テスト:** `_rewardPercentage` に小さな値で関数をテストし、結果が正確であることをチェックし、予期しない丸め動作がないことを検証します。
