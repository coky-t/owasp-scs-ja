---
title: デッドコード (Dead Code)
id: SCWE-062
alias: code-with-no-effects
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-1]
  cwe: [561]
status: new
---

## 関連
- CWE-561: Dead Code
  [https://cwe.mitre.org/data/definitions/561.html](https://cwe.mitre.org/data/definitions/561.html)

## 説明
Code with no effects or Dead Code- refers to segments of a smart contract that are never executed or do not alter the contract's state or output. This can lead to unnecessary gas consumption, complicate the contract’s logic, and potentially confuse developers. Dead code often appears as leftover code from previous iterations or functions that are no longer in use but have not been removed. Removing such code improves contract efficiency and readability.

## 対策
To mitigate this vulnerability, ensure that all functions, variables, and logic in the smart contract have a purpose and contribute to the contract’s behavior. Unused code should be removed to reduce complexity, save gas, and improve maintainability.

### 脆弱なコントラクトの例
```solidity
contract Vulnerable {
    uint public value;

    // Dead code, never called or used
    function unusedFunction() public {
        uint x = 5;
        uint y = 10;
        uint result = x + y;
    }

    function setValue(uint _value) public {
        value = _value;
    }
}
```

### 修正したコントラクトの例
```solidity
contract Fixed {
    uint public value;

    function setValue(uint _value) public {
        value = _value;
    }
}
```
