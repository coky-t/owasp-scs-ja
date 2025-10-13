---
title: 不十分な認可チェック (Insufficient Authorization Checks)
id: SCWE-016
alias: insufficient-authorization-checks
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [862]
status: new
---

## 関連
- CWE-862: Missing Authorization
  [https://cwe.mitre.org/data/definitions/862.html](https://cwe.mitre.org/data/definitions/862.html)

## 説明
Insufficient authorization occurs when the contract does not verify whether a user has the appropriate permissions to perform specific actions. This vulnerability arises when there are missing or improperly implemented authorization checks, allowing unauthorized users to access functions they should not have access to.

Common causes of insufficient authorization include:
- Missing access control modifiers or incorrect configuration.
- Failure to validate roles before granting access to sensitive functions.
- Poor handling of user permissions, which could lead to privilege escalation.

## 対策
- **Implement role-based access control (RBAC):** Define roles such as `admin`, `user`, etc., and assign permissions accordingly to ensure only authorized users can call restricted functions.
- **Use access control modifiers:** Protect sensitive functions with appropriate modifiers (e.g., `onlyOwner`, `onlyAdmin`) to restrict access.
- **Principle of least privilege:** Ensure each user only has access to the minimal set of actions they need to perform their role.
- **Thorough testing:** Test for authorization vulnerabilities to ensure all restricted functions are properly secured.

## 事例

### 不十分な認可の例

```solidity
pragma solidity ^0.8.0;

contract InsufficientAuthorization {
    uint public balance;

    function withdraw(uint amount) public {
        // No checks for user roles, any address can withdraw funds
        balance -= amount;
    }
}
```

### 修正した認可の例
```solidity
pragma solidity ^0.8.0;

contract FixedAuthorization {
    uint public balance;
    address public owner;

    constructor() public {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }

    function withdraw(uint amount) public onlyOwner {
        balance -= amount;
    }
}

```
In the fixed example, the `onlyOwner` modifier ensures that only the contract owner can withdraw funds, thus preventing unauthorized users from performing sensitive operations like withdrawing assets.
