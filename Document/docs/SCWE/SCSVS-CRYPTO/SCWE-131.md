---
title: 集約署名でのドメイン分離の欠如 (Missing Domain Separation in Aggregate Signatures)
id: SCWE-131
alias: missing-domain-separation-agg-sigs
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-2]
  cwe: [347]
status: new
---

## 関連
- CWE-347: Improper Verification of Cryptographic Signature  
  [https://cwe.mitre.org/data/definitions/347.html](https://cwe.mitre.org/data/definitions/347.html)

## 説明
ドメイン分離 (チェーン ID、コントラクト、目的など) を省略する BLS/集約署名スキームはドメイン間での署名リプレイを可能にします。あるネットワークやメッセージタイプで有効な署名が他の場所で再使用され、意図しないアクションを認可する可能性があります。

## 対策
- Include explicit domain separation tags in the signed message for every aggregate signature.
- Verify domains on-chain and reject signatures from unknown domains.
- Use well-reviewed libraries and EIP-712 style typed data for aggregate schemes.

## 事例

### 脆弱
```solidity
bytes32 message = keccak256(abi.encode(data)); // no domain tag
```

### 修正済み
```solidity
bytes32 message = keccak256(abi.encode(DOMAIN_SEPARATOR, keccak256(data)));
```
