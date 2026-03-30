---
title: ECDSA Nonce 再使用 (ECDSA Nonce Reuse)
id: SCWE-114
alias: ecdsa-nonce-reuse
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-1]
  cwe: [323]
status: new
---

## 関連
- CWE-323: Reusing a Nonce, Key Pair in Encryption  
  [https://cwe.mitre.org/data/definitions/323.html](https://cwe.mitre.org/data/definitions/323.html)

## 説明
署名間で同じ ECDSA nonce (`k`) を再使用 (または予測可能な nonce を使用) すると、秘密鍵を漏洩します。許可証、メタトランザクション、ガバナンスにオフチェーン署名を受け入れるコントラクトは、署名インフラストラクチャが nonce を誤って管理する場合、侵害される可能性があります。

## 対策
- Use battle-tested libraries/wallets that generate unique, random or RFC6979 deterministic nonces per message.
- Monitor and rotate keys if nonce reuse is suspected; support key revocation on-chain.
- Avoid custom signing code or manual nonce management in scripts.

## 事例

### 脆弱
```solidity
// Off-chain signer reuses k for two messages:
// sig1 = (r, s1) with k
// sig2 = (r, s2) with same k => private key can be recovered
```

### 修正済み
```solidity
// Off-chain: Use libraries/wallets that follow RFC6979; never reuse k.
// On-chain: Support key rotation so compromised keys can be revoked.
mapping(address => bool) public revokedSigners;

function execute(bytes calldata payload, bytes calldata sig) external {
    address signer = _recoverSigner(payload, sig);
    require(!revokedSigners[signer], "key revoked");
    // ...
}
```
