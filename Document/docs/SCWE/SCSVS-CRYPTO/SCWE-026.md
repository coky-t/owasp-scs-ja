---
title: 不十分なハッシュ検証 (Insufficient Hash Verification)
id: SCWE-026
alias: insufficient-hash-verification
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
  [CWE-347 Link](https://cwe.mitre.org/data/definitions/347.html)

## 説明
不十分なハッシュ検証は、メッセージハッシュやトランザクションハッシュなどのハッシュを適切に検証していないことを指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **ハッシュを検証する:** 処理前にすべてのハッシュが適切に検証されていることを確保します。
- **安全なライブラリを使用する:** ハッシュ検証には十分に監査されたライブラリを活用します。
- **徹底的にテストする:** 広範なテストを実施して、ハッシュ検証が意図したとおりに機能することを確認します。

## 事例
- **不十分なハッシュ検証**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsufficientHashVerification {
        function processHash(bytes32 hash) public {
            // Process hash without verification
        }
    }
    ```

- **十分なハッシュ検証**
    ```solidity
    pragma solidity ^0.8.0;

    contract SufficientHashVerification {
        function processHash(bytes32 hash, bytes memory data) public {
            require(keccak256(data) == hash, "Invalid hash");
            // Process hash
        }
    }
    ```

---
