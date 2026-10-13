---
title: クロスチェーンメッセージでのチェーン ID バリデーションの欠如 (Missing Chain ID Validation in Cross-Chain Messages)
id: SCWE-107
alias: missing-chainid-validation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-3]
  cwe: [345]
status: new
---

## 関連
- CWE-345: Insufficient Verification of Data Authenticity  
  [https://cwe.mitre.org/data/definitions/345.html](https://cwe.mitre.org/data/definitions/345.html)

## 説明
送信元の `chainId` やエンドポイントのドメインをチェックせずにペイロードを受け入れるクロスチェーンハンドラは、他のネットワークやフォークからのメッセージのリプレイを許します。攻撃者は、意図しないチェーン上での、送金、鋳造、ガバナンスアクションを再トリガーできます。

## 対策
- Bind every inbound message to an expected source chain/domain and trusted sender.
- Include `chainId`/domain separators in signed payloads and verify them before execution.
- Maintain replay protection (nonces) per (sourceChain, sourceSender).

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract BridgeReceiver {
    function receiveMessage(bytes calldata data) external {
        _execute(data); // no chainId/source validation
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract BridgeReceiver {
    mapping(uint256 => mapping(address => uint256)) public nonce; // per (chainId, sender)
    address public trustedSender;
    uint256 public trustedChainId;

    function receiveMessage(uint256 srcChainId, address src, uint256 n, bytes calldata data) external {
        require(srcChainId == trustedChainId && src == trustedSender, "unauthorized source");
        require(n == nonce[srcChainId][src]++, "replay");
        _execute(data);
    }
}
```
