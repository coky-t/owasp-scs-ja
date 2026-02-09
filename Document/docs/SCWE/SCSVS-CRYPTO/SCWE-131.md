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
BLS/aggregate signature schemes that omit domain separation (e.g., chain id, contract, purpose) allow signature replay across domains. A valid signature for one network or message type can be reused elsewhere to authorize unintended actions.

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
