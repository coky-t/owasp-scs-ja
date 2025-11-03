---
title: 安全でないデータ転送 (Unsecured Data Transmission)
id: SCWE-021
alias: unsecured-data-transmission
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [319]
status: new
---

## 関連
- CWE-319: Cleartext Transmission of Sensitive Information  
  [CWE-319 Link](https://cwe.mitre.org/data/definitions/319.html)

## 説明
安全でないデータ転送は、秘密鍵やユーザーデータなどの機密情報を暗号化なしで転送することを指します。これは以下につながる可能性があります。
- 悪意のある人物による機密データの傍受。
- コントラクトの脆弱性の悪用。
- 資金やデータの喪失。

## 対策
- **暗号化を使用する:** 転送前に機密データを暗号化します。
- **安全なプロトコルを活用する:** HTTPS またはその他の安全な通信プロトコルを使用します。
- **機密データの転送を避ける:** 可能な限り、機密データの転送を最小限に抑えます。

## 事例
- **Unsecured Data Transmission**
    ```solidity
    pragma solidity ^0.8.0;

    contract UnsecuredData {
        function transmitData(bytes memory data) public {
            // Transmit data without encryption
        }
    }
    ```

- **Secured Data Transmission**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecuredData {
        function transmitData(bytes memory encryptedData) public {
            // Transmit encrypted data
        }
    }
    ```

---
