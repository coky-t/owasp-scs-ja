---
title: 状態変数のデフォルトの可視性 (State Variable Default Visibility)
id: SCWE-068
alias: state-variable-default-visibility
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-1]
  cwe: [704]
status: new
---

## 関連
- CWE-704: Incorrect Type Conversion or Cast
  [https://cwe.mitre.org/data/definitions/704.html](https://cwe.mitre.org/data/definitions/704.html)


## 説明
In Solidity, state variables have a default visibility of `internal`, which means they can only be accessed within the contract or derived contracts. However, developers might unintentionally leave state variables with incorrect or unspecified visibility, leading to unintended access. In some cases, this could allow external contracts or users to interact with the variable, potentially leading to security vulnerabilities, such as unauthorized access or manipulation of the contract's state.

It is essential to explicitly define the visibility of state variables to ensure they behave as intended and are protected from unintended access.

## 対策
Always explicitly specify the visibility of state variables. The possible visibility options are:
- `public`: Accessible by anyone, both externally and internally.
- `internal`: Accessible only within the contract or derived contracts (default).
- `private`: Accessible only within the contract.

### 脆弱なコントラクトの例
```solidity
contract Vulnerable {
    uint publicBalance;  // Default visibility is internal, but it is still publicly accessible

    constructor() {
        publicBalance = 100;
    }

    function updateBalance(uint amount) public {
        publicBalance = amount;  // Access to internal variable, but external access is possible
    }
}
```

### 修正したコントラクトの例
```solidity
contract Secure {
    uint private balance;  // Explicitly set visibility to private

    constructor() {
        balance = 100;
    }

    function updateBalance(uint amount) public {
        balance = amount;  // No external access to the variable
    }
}
```
