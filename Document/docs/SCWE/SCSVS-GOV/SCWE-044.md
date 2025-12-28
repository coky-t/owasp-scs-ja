---
title: ストレージの安全でない使用 (Insecure Use of Storage)
id: SCWE-044
alias: insecure-use-of-storage
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-1]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
ストレージの安全でない使用はストレージ変数が不適切に管理される場合に発生する脆弱性を指します。これは以下につながる可能性があります。
- 機密データへの不正アクセス。
- 資金やデータの喪失。
- コントラクトロジックの脆弱性の悪用。

## 対策
- **Encrypt sensitive data:** Encrypt sensitive data before storing it.
- **Validate inputs:** Ensure all storage updates are properly validated.
- **Test thoroughly:** Conduct extensive testing to ensure storage is secure.

## 事例
- **Insecure Storage Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureStorage {
        uint public balance;

        function updateBalance(uint newBalance) public {
            balance = newBalance; // No validation
        }
    }
    ```

- **Secure Storage Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureStorage {
        uint public balance;

        function updateBalance(uint newBalance) public {
            require(newBalance > 0, "Invalid balance"); // Input validation
            balance = newBalance;
        }
    }
    ```

---
