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
  [CWE-20 Link](https://cwe.mitre.org/data/definitions/20.html)

## 説明
Insecure oracle data updates refer to the failure to properly validate or secure updates to oracle data. This can lead to:
- Unauthorized actions by malicious actors.
- Loss of funds or data.
- Exploitation of the contract's logic.

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
