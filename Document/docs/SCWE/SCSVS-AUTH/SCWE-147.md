---
title: 有効期限なしでの許可またはメタトランザクション署名 (Permit or Meta-Transaction Signatures Without Expiration)
id: SCWE-147
alias: missing-signature-expiration
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [347]
status: new
---

## 関連
- CWE-347: Improper Verification of Cryptographic Signature  
  [https://cwe.mitre.org/data/definitions/347.html](https://cwe.mitre.org/data/definitions/347.html)

## 説明
`deadline` や `expiry` パラメータを省略した Permit (EIP-2612) やメタトランザクションの署名は、一度取得されると無期限にリプレイされる可能性があります。有効な署名を (フィッシングや侵害されたフロントエンドなどを介して) 捕獲した攻撃者は、それを将来の任意の時点で送信できます。SCWE-105 ではドメインセパレータや nonce をカバーしますが、この弱点は特に有効期限の欠如を扱います。

## 対策
- 署名付きメッセージに `deadline` (または `expiry`) を含め、署名を処理する際に `require(block.timestamp <= deadline, "Expired")` を適用します。
- deadline フィールドを備えた EIP-712 構造化データを使用します；

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract MetaTx {
    function execute(address signer, bytes32 hash, uint8 v, bytes32 r, bytes32 s) external {
        require(ecrecover(hash, v, r, s) == signer, "Bad sig");
        // No deadline: signature valid forever
        _execute(signer);
    }
}
```

### 修正済み
```solidity
function execute(address signer, uint256 deadline, bytes32 hash, uint8 v, bytes32 r, bytes32 s) external {
    require(block.timestamp <= deadline, "Expired");
    require(ecrecover(hash, v, r, s) == signer, "Bad sig");
    _execute(signer);
}
```
**Note:** The `hash` must be computed from EIP-712 structured data that includes the `deadline` field so the signature cannot be replayed after expiry.
