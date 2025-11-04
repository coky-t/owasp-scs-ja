---
title: 通信の真正性の欠如 (Lack of Communication Authenticity)
id: SCWE-023
alias: lack-of-communication-authenticity
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
通信の真正性の欠如は、メッセージやトランザクションの真正性を検証できないことを指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **署名を使用する:** 重要なアクションには署名付きメッセージを要求します。
- **入力を検証する:** 処理前にすべてのメッセージが適切に検証されていることを確認します。
- **安全な通信を実装する:** 通信委は安全なプロトコルとライブラリを使用します。

## 事例
- **真正性の欠如**
    ```solidity
    pragma solidity ^0.8.0;

    contract NoAuthenticity {
        function processMessage(bytes memory message) public {
            // Process message without authenticity check
        }
    }
    ```

- **真正の通信**
    ```solidity
    pragma solidity ^0.8.0;

    import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

    contract AuthenticCommunication {
        using ECDSA for bytes32;

        function processMessage(bytes memory message, bytes memory signature) public {
            bytes32 messageHash = keccak256(message);
            address signer = messageHash.recover(signature);
            require(signer == msg.sender, "Invalid signature");
            // Process message
        }
    }
    ```

---
