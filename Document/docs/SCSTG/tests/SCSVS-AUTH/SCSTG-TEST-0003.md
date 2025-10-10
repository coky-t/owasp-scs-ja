---
id: SCSTG-TEST-0003
scsvs_cg_id:
- SCSVS-AUTH
scsvs_scg_id:
- SCSVS-AUTH-1
platform: []
title: 最小権限の原則のテスト (Least Privilege Principle Test)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0003
---

悪意のあるコントラクトからの潜在的な悪用を避けるため、認可には tx.origin ではなく msg.sender を使用します。送信者が EDA であることを確認するために require(tx.origin == msg.sender) などのチェックを含めます。

- tx.origin は悪意のあるコントラクトによって悪用され、システムを騙して、何も知らないユーザーの代わりにアクションを実行する可能性があります。メッセージの直接の送信者を参照するため、msg.sender が推奨されます。
```solidity
require(msg.sender == owner, "Not the owner");
require(tx.origin == msg.sender, "Only EOA can execute");
```

特定のアドレス (LUSD など) はトークンの受信からブロックまたは制限されているかもしれません。アドレス制限が適切に管理および検証されていることを確認します。

- 特定のアドレス (LUSD など) がトークンの受信からブロックされる必要がある場合、これらのアドレスを制限するために実施されるチェックがあることを確認します。
```solidity
address restrictedAddress = 0x123...;  // Example of a restricted address
require(msg.sender != restrictedAddress, "Restricted address cannot perform this operation");
```


重要なセキュリティチェックを実施するために Guard のフック (checkTransaction(), checkAfterExecution() など) が実行されていることを確認します。

- Guard コントラクトを使用している場合、checkTransaction() や checkAfterExecution() などのフックが適切に実装され、セキュリティ条件が適用されるようにします。
```solidity
function checkTransaction() internal {
    // Add conditions to verify transaction before execution
}

function checkAfterExecution() internal {
    // Add conditions to verify transaction after execution
}
```
