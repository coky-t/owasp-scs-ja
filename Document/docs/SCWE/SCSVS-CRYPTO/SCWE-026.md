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
- **Validate hashes:** Ensure all hashes are properly verified before processing.
- **Use secure libraries:** Leverage well-audited libraries for hash verification.
- **Test thoroughly:** Conduct extensive testing to ensure hash verification works as intended.

## 事例
- **Insufficient Hash Verification**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsufficientHashVerification {
        function processHash(bytes32 hash) public {
            // Process hash without verification
        }
    }
    ```

- **Sufficient Hash Verification**
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
