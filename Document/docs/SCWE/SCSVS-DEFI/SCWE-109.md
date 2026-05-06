---
title: 信頼できない入力での無制限のループ (Unbounded Loops on Untrusted Input)
id: SCWE-109
alias: unbounded-loops
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-1]
  cwe: [834]
status: new
---

## 関連
- CWE-834: Excessive Iteration  
  [https://cwe.mitre.org/data/definitions/834.html](https://cwe.mitre.org/data/definitions/834.html)

## 説明
ユーザーが制御する配列やマッピングを制限なしで反復処理することで、攻撃者は大量の入力を送信してガスを消費させ、サービス拒否攻撃を引き起こす可能性があります。バッチ出金、報酬分配、清算などの機能が永久的に使用不能になる恐れがあります。

## 対策
- ループ反復やバッチサイズに上限を設けます。
- ページネーション/反復処理をチェックポイントとともに使用します。
- 信頼できないリストに対するオンチェーンの反復を避け、検証可能な証明 (マークルリーフ) を用いたオフチェーン集約に頼ります。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Distributor {
    function airdrop(address[] calldata users) external {
        for (uint256 i = 0; i < users.length; i++) {
            (bool ok, ) = payable(users[i]).call{value: 1 ether}("");
            require(ok, "Transfer failed"); // can run out of gas with large arrays
        }
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Distributor {
    uint256 public last;

    function airdrop(address[] calldata users, uint256 max) external {
        uint256 end = last + max;
        if (end > users.length) end = users.length;
        for (uint256 i = last; i < end; i++) {
            (bool ok, ) = payable(users[i]).call{value: 1 ether}("");
            require(ok, "Transfer failed");
        }
        last = end;
    }
}
```
