---
title: Selfdestruct の安全でない使用 (Insecure Use of Selfdestruct)
id: SCWE-038
alias: insecure-use-of-selfdestruct
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [732]
status: new
---

## 関連
- CWE-732: Incorrect Permission Assignment for Critical Resource  
  [CWE-732 Link](https://cwe.mitre.org/data/definitions/732.html)

## 説明
selfdestruct の安全でない使用は `selfdestruct` 関数が適切な安全策なしで使用される場合に生じる脆弱性を指します。これは以下につながる可能性があります。
- コントラクトの不正な破棄。
- 資金やデータの喪失。
- コントラクトロジックの脆弱性の悪用。

## 対策
- **Restrict access:** Ensure only authorized addresses can call `selfdestruct`.
- **Implement circuit breakers:** Add mechanisms to halt operations in case of suspicious activity.
- **Test thoroughly:** Conduct extensive testing to ensure `selfdestruct` is used securely.

## 事例
- **Insecure Selfdestruct Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureSelfdestruct {
        function destroy() public {
            selfdestruct(payable(msg.sender)); // No access control
        }
    }
    ```

- **Secure Selfdestruct Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureSelfdestruct {
        address public admin;

        constructor(address _admin) {
            admin = _admin;
        }

        modifier onlyAdmin() {
            require(msg.sender == admin, "Unauthorized");
            _;
        }

        function destroy() public onlyAdmin {
            selfdestruct(payable(admin)); // Restricted to admin
        }
    }
    ```

---
