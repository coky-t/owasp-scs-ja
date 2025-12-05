---
title: 脆弱な暗号アルゴリズム (Vulnerable Cryptographic Algorithms)
id: SCWE-027
alias: vulnerable-cryptographic-algorithms
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CRYPTO]
  scsvs-scg: [SCSVS-CRYPTO-2]
  cwe: [327]
status: new
---

## 関連
- **CWE-327:** Use of a Broken or Risky Cryptographic Algorithm  
  [CWE-327 Link](https://cwe.mitre.org/data/definitions/327.html)

## 説明
脆弱な暗号アルゴリズムは、MD5 や SHA-1 などの、古くなったまたは安全でない暗号アルゴリズムの使用を指します。これは以下につながる可能性があります。
- コントラクトのロジックの悪用。
- 資金やデータの喪失。
- コントラクトのセキュリティでの信頼低下。

## 対策
- **Use secure algorithms:** Leverage modern cryptographic algorithms like SHA-256 or Keccak-256.
- **Avoid deprecated algorithms:** Do not use algorithms known to be insecure.
- **Test thoroughly:** Conduct extensive testing to ensure cryptographic security.

## 事例
- **Vulnerable Algorithm**
    ```solidity
    pragma solidity ^0.8.0;

    contract VulnerableAlgorithm {
        function hashData(bytes memory data) public pure returns (bytes32) {
            return sha256(data); // Insecure algorithm
        }
    }
    ```

- **Secure Algorithm**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureAlgorithm {
        function hashData(bytes memory data) public pure returns (bytes32) {
            return keccak256(data); // Secure algorithm
        }
    }
    ```

---
