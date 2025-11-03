---
title: メッセージリプレイの脆弱性 (Message Replay Vulnerabilities)
id: SCWE-022
alias: message-replay-vulnerabilities
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [294]
status: new
---

## 関連
- CWE-294: Authentication Bypass by Capture-replay  
  [CWE-294 Link](https://cwe.mitre.org/data/definitions/294.html)

## 説明
メッセージリプレイの脆弱性は、攻撃者が有効なメッセージやトランザクションを再使用して不正なアクションを実行できる場合に発生します。これは以下につながる可能性があります。
- 機密機能への不正アクセス。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **Use nonces:** Include a unique nonce in each message to prevent reuse.
- **Validate timestamps:** Ensure messages are only valid for a limited time.
- **Implement replay protection:** Use established libraries or mechanisms to prevent replay attacks.

## 事例
- **Vulnerable to Replay Attacks**
    ```solidity
    pragma solidity ^0.8.0;

    contract ReplayVulnerable {
        function processMessage(bytes memory message) public {
            // Process message without replay protection
        }
    }
    ```

- **Protected Against Replay Attacks**
    ```solidity
    pragma solidity ^0.8.0;

    contract ReplayProtected {
        mapping(bytes32 => bool) public usedMessages;

        function processMessage(bytes memory message, uint nonce, uint chainId) public {
            bytes32 messageHash = keccak256(abi.encodePacked(message, nonce, chainId));
            require(!usedMessages[messageHash], "Message already used");
            usedMessages[messageHash] = true;
            // Process message
        }
    }
    ```

---
