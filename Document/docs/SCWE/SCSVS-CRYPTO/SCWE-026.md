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
Insufficient hash verification refers to the failure to properly verify hashes, such as message hashes or transaction hashes. This can lead to:
- Unauthorized actions by malicious actors.
- Loss of funds or data.
- Exploitation of the contract's logic.

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
