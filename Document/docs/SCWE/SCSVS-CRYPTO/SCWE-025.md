---
title: 不適切な暗号鍵管理 (Improper Cryptographic Key Management)
id: SCWE-025
alias: improper-cryptographic-key-management
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-1]
  cwe: [310]
status: new
---

## 関連
- CWE-310: Cryptographic Issues  
  [CWE-310 Link](https://cwe.mitre.org/data/definitions/310.html) (参考: JVN iPedia [CWE-310 暗号の問題](https://jvndb.jvn.jp/ja/cwe/CWE-310.html))

## 説明
不適切な暗号鍵管理は暗号鍵を安全に生成、保管、または使用できないことを指します。これは以下につながる可能性があります。
- 機密データへの不正アクセス。
- コントラクトのロジックの悪用。
- 資金やデータの喪失。

## 対策
- **Use secure key management:** Leverage secure key management systems or libraries.
- **Avoid hardcoding keys:** Never hardcode cryptographic keys in the contract.
- **Regularly rotate keys:** Periodically update cryptographic keys to reduce risks.

## 事例
- **Improper Key Management**
    ```solidity
    pragma solidity ^0.8.0;

    contract ImproperKeyManagement {
        bytes32 private key = keccak256("insecure-key"); // Hardcoded key
    }
    ```

- **Proper Key Management**
    ```solidity
    pragma solidity ^0.8.0;

    contract ProperKeyManagement {
        bytes32 private key;

        constructor(bytes32 _key) {
            key = _key; // Configurable key
        }
    }
    ```
