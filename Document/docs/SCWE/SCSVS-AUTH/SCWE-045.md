---
title: Modifier の安全でない使用 (Insecure Use of Modifiers)
id: SCWE-045
alias: insecure-use-of-modifiers
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [732]
status: new
---

## 関連
- CWE-732: Incorrect Permission Assignment for Critical Resource  
  [CWE-732 Link](https://cwe.mitre.org/data/definitions/732.html)

## 説明
modifier の安全でない使用は修飾子が不適切に使用された場合に生じる脆弱性を指します。これは以下につながる可能性があります。
- 悪意のある人物による不正行為。
- 資金やデータの喪失。
- コントラクトロジックの脆弱性の悪用。

## 対策
- **アクセスを制限する:** 認可されたアドレスのみが modifier を使用できることを確保します。
- **入力を検証する:** modifier へのすべての入力が適切に検証されていることを確保します。
- **徹底的にテストする:** 広範なテストを実施して、modifier が安全であることを確認します。

## 事例
- **Insecure Modifier Usage (With Storage Bug)**
    ```solidity
    pragma solidity ^0.8.0;

    contract InsecureModifier {
        address public admin;

        constructor(address _admin) {
            admin = _admin; // ❌ Storage bug: Allows setting admin to zero address.
        }

        modifier onlyAdmin() {
            require(admin == address(0), "Unauthorized"); // ❌ Wrong condition, only works if admin is zero!
            _;
        }

        function updateAdmin(address newAdmin) public onlyAdmin {
            admin = newAdmin; // ❌ Can set admin to zero address, breaking access control.
        }

        function updateBalance(uint newBalance) public onlyAdmin {
            // Update balance
        }
    }
    ```


**Why is this Insecure?**
- Wrong Condition in `onlyAdmin()`
    - `require(admin == address(0), "Unauthorized");` only allows function execution when admin is zero.
    - This means no valid admin can ever execute admin functions!
    - If admin is ever non-zero, all `onlyAdmin` functions become unusable.
    
- Loss of Control
    - If admin is accidentally set to `address(0)`, anyone can now execute admin functions, breaking security

- **Secure Modifier Usage**
    ```solidity
    pragma solidity ^0.8.0;

    contract SecureModifier {
        address public admin;

        constructor(address _admin) {
            require(_admin != address(0), "Invalid admin address"); // ✅ Proper validation
            admin = _admin;
        }

        modifier onlyAdmin() {
            require(msg.sender == admin, "Unauthorized"); // ✅ Correctly restricts access
            _;
        }

        function updateAdmin(address newAdmin) public onlyAdmin {
            require(newAdmin != address(0), "New admin cannot be zero address"); // ✅ Prevents admin loss
            admin = newAdmin;
        }

        function updateBalance(uint newBalance) public onlyAdmin {
            // Update balance securely
        }
    }
    ```

**Why is this Secure?**
- Proper access control in `onlyAdmin()`
    - `require(msg.sender == admin, "Unauthorized");` ensures only the correct admin can execute admin functions.
        - Prevents privilege escalation
- `updateAdmin()` prevents setting `admin = address(0)`, avoiding unintended loss of access.

---
