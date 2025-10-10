---
id: SCSTG-TEST-0004
scsvs_cg_id:
- SCSVS-AUTH
scsvs_scg_id:
- SCSVS-AUTH-1
platform: []
title: 重要な機能でのアクセス制御のテスト (Test Access Control on Critical Functions)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0004
---

アクセス制御が正しく実装され、特定の機能を誰が使用できるかを判断し、不正な変更や引き落としを回避していることを確認します。

- 特定のロールやパーミッションを必要とする関数が onlyOwner またはロールベースのチェックを使用して適切に制限されていることを確認します。
```solidity
    modifier onlyOwner() {
        require(msg.sender == owner, "Caller is not the owner");
        _;
    }

    function withdraw() external onlyOwner {
        // Only the owner can withdraw
    }
```
