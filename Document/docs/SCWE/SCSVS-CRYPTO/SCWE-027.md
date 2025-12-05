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
- **安全なアルゴリズムを使用する:** SHA-256 や Keccak-256 などの最新の暗号アルゴリズムを活用します。
- **非推奨のアルゴリズムを避ける:** 安全でないことが知られているアルゴリズムを使用してはいけません。
- **徹底的にテストする:** 広範なテストを実施して、暗号セキュリティを確保します。

## 事例
- **脆弱なアルゴリズム**
    ```solidity
    pragma solidity ^0.8.0;

    contract VulnerableAlgorithm {
        function hashData(bytes memory data) public pure returns (bytes32) {
            return sha256(data); // Insecure algorithm
        }
    }
    ```

- **安全なアルゴリズム**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureAlgorithm {
        function hashData(bytes memory data) public pure returns (bytes32) {
            return keccak256(data); // Secure algorithm
        }
    }
    ```

---
