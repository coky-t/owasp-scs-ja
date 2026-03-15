---
title: トークン残高ドリフトのリベース (Rebase Token Balance Drift)
id: SCWE-111
alias: rebase-token-drift
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMP]
  scsvs-scg: [SCSVS-COMP-1]
  cwe: [682]
status: new
---

## 関連
- CWE-682: Incorrect Calculation  
  [https://cwe.mitre.org/data/definitions/682.html](https://cwe.mitre.org/data/definitions/682.html)

## 説明
トークンのリベースは時間の経過とともにユーザー残高を変化します。預け入れ金額を (株式ではなく) 固定単位で追跡するプロトコルは、正または負のリベース後に破綻したり、ユーザーが正当な割合を超えて引き落とす可能性があります。

## 対策
- 総供給量でスケールする株式ベースの会計処理を使用して、ユーザーのポジションを追跡します。
- 取引ごとに残高を正規化するか、リベースを行わないインタフェース (例: wstETH) を提示するラッパーを使用します。
- 供給量が変動するトークンとの統合を検証し、サポートされていない資産を文書化します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Savings {
    IERC20 public rebasingToken;
    mapping(address => uint256) public deposits;

    function deposit(uint256 amount) external {
        rebasingToken.transferFrom(msg.sender, address(this), amount);
        deposits[msg.sender] += amount; // assumes static balance
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Savings {
    IERC20 public rebasingToken;
    mapping(address => uint256) public shares;
    uint256 public totalShares;

    function deposit(uint256 amount) external {
        uint256 supply = rebasingToken.totalSupply();
        uint256 share = totalShares == 0 ? amount : amount * totalShares / supply;
        shares[msg.sender] += share;
        totalShares += share;
        rebasingToken.transferFrom(msg.sender, address(this), amount);
    }
}
```
