---
title: Bridge Lane ごとのリプレイ Nonce の欠如 (Missing Replay Nonce per Bridge Lane)
id: SCWE-133
alias: missing-bridge-nonce
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BRIDGE]
  scsvs-scg: [SCSVS-BRIDGE-1]
  cwe: [294]
status: new
---

## 関連
- CWE-294: Authentication Bypass by Capture-replay  
  [https://cwe.mitre.org/data/definitions/294.html](https://cwe.mitre.org/data/definitions/294.html)

## 説明
ソースチェーン/送信者ごとにノンスを追跡しないブリッジ受信者は同じメッセージのリプレイを許可し、重複したミントや引き落としにつながります。レーンが分離されていない場合、フォークされたチェーンも履歴メッセージをリプレイする可能性があります。

## 対策
- Maintain monotonically increasing nonces per (sourceChain, sourceSender).
- Reject messages with reused or out-of-order nonces.
- Bind nonces into signed payloads or proofs to prevent tampering.

## 事例

### 脆弱
```solidity
function receive(bytes calldata payload) external {
    _execute(payload); // no nonce tracking
}
```

### 修正済み
```solidity
mapping(uint256 => mapping(address => uint256)) public nonce;

function receive(uint256 srcChain, address src, uint256 n, bytes calldata payload) external {
    require(n == nonce[srcChain][src]++, "replay");
    _execute(payload);
}
```
