---
title: 認証されていないメタトランザクション (Unauthenticated Meta-Transactions)
id: SCWE-106
alias: unauthenticated-meta-transactions
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
署名、nonce、チェーン、有効期限を検証せずに呼び出しを中継するメタトランザクションフォワーダーは、リレイヤーが被害者に代わって任意のアクションを実行可能になります。リプレイ保護の欠如は同じ署名付きリクエストが複数回実行されることを可能にします。

## 対策
- 宣言された署名者に対して、EIP-712 形式のデータ署名を検証します。
- 署名者ごとの nonce を追跡および更新し、有効期限と chainId を強制します。
- 信頼されたフォワーダーを制限し、後続の呼び出しで `msg.sender`/`msg.data` の前提条件をサニタイズします。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Forwarder {
    function relay(address target, bytes calldata data) external {
        (bool ok, ) = target.call(data); // no signature or nonce check
        require(ok, "call failed");
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Forwarder {
    mapping(address => uint256) public nonces;

    function relay(address target, bytes calldata data, uint256 nonce, uint256 deadline, bytes calldata sig) external {
        require(block.timestamp <= deadline, "expired");
        bytes32 digest = keccak256(abi.encode(target, data, nonce, block.chainid, deadline));
        address signer = ECDSA.recover(digest, sig);
        require(nonce == nonces[signer]++, "bad nonce");
        (bool ok, ) = target.call(data);
        require(ok, "call failed");
    }
}
```
