---
title: 安全でないオラクルデータ更新 (Insecure Oracle Data Updates)
id: SCWE-030
alias: insecure-oracle-data-updates
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ORACLE]
  scsvs-scg: [SCSVS-ORACLE-1]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
安全でないオラクルデータ更新は、オラクルデータの更新を適切に検証または保護していないことを指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトのロジックの悪用。

## 対策
- **Validate updates:** Ensure all oracle data updates are properly validated.
- **Restrict access:** Restrict update permissions to trusted addresses.
- **Implement timelocks:** Add delays to oracle updates to allow for review.

## 事例
- **Insecure Oracle Updates**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureOracleUpdates {
        function updatePrice(address oracle, uint newPrice) public {
            Oracle(oracle).updatePrice(newPrice); // No validation
        }
    }
    ```

- **Secure Oracle Updates**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureOracleUpdates {
        address public admin;

        constructor(address _admin) {
            admin = _admin;
        }

        modifier onlyAdmin() {
            require(msg.sender == admin, "Unauthorized");
            _;
        }

        function updatePrice(address oracle, uint newPrice) public onlyAdmin {
            Oracle(oracle).updatePrice(newPrice); // Restricted to admin
        }
    }
    ```
