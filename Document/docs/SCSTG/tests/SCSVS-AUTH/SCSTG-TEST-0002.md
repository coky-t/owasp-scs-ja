---
id: SCSTG-TEST-0002
scsvs_cg_id:
- SCSVS-AUTH
scsvs_scg_id:
- SCSVS-AUTH-1
platform: []
title: アイデンティティ検証のテスト (Identity Verification Test)
scsvs_cg_levels:
- L2
tests: SCSTG-TEST-0002
---

予期しないアドレスが、特に、これらのアドレスが同じプロトコル内のコントラクトを参照している場合、意図しない動作を引き起こさないことを検証します。

- コントラクトとやり取りする際、機密性の高い操作を実行する前に予期しないアドレスが適切に検証されていることを確認します。
```solidity
require(address(contract) != address(0), "Invalid address");
```

予期しない ecrecover 出力から生じる脆弱性を回避するために、ecrecover などの関数がすべての潜在的な null アドレスを適切に処理することを検証します。

- ecrecover が空または null アドレスを処理しないことを確認します。
```solidity
address recovered = ecrecover(messageHash, v, r, s);
require(recovered != address(0), "Invalid signature");
```
