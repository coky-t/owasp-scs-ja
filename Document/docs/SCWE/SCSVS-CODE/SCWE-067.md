---
title: アサートバイオレーション (Assert Violation)
id: SCWE-067
alias: assert-violation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-2]
  cwe: [754]
status: new
---

## 関連
- CWE-754: Improper Check for Unusual or Exceptional Conditions
  [https://cwe.mitre.org/data/definitions/754.html](https://cwe.mitre.org/data/definitions/754.html)

## 説明
In Ethereum smart contracts, assertions (`assert`) are commonly used for internal consistency checks and to prevent erroneous states. However, if not used correctly, they can lead to unexpected behavior. When an `assert` statement fails, it reverts the entire transaction, and all changes made in the transaction are reverted. This can be problematic if it is used for conditions that can be expected during normal contract execution.

Using `assert` for conditions that can occur under normal circumstances (e.g., user inputs or contract logic) may cause unnecessary reverts and loss of gas. It’s important to reserve `assert` for critical errors, such as invariants and internal state checks that should never fail.

## 対策
To avoid unnecessary assertion failures, use `require` for input validation and conditions that can reasonably fail. `assert` should only be used for internal error detection and state validation that should never happen, as it signals a critical issue if violated.

### 脆弱なコントラクトの例
```solidity
contract Vulnerable {
    uint public balance;

    constructor() {
        balance = 100;
    }

    function withdraw(uint amount) public {
        assert(balance >= amount);  // Assert violation on user input
        balance -= amount;
    }
}
```

### 修正したコントラクトの例
```solidity
contract Secure {
    uint public balance;

    constructor() {
        balance = 100;
    }

    function withdraw(uint amount) public {
        require(balance >= amount, "Insufficient balance");  // Use require for expected conditions
        balance -= amount;
    }
}
```
