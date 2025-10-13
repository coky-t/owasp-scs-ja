---
title: 適切なガス管理の欠如 (Lack of Proper Gas Management)
id: SCWE-082
alias: lack-of-proper-gas-management
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-1]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)

## 説明
Gas management is crucial in smart contracts to ensure that they do not run out of gas or cause excessive consumption. If gas consumption is not properly controlled, a contract can fail to execute or can be exploited by attackers to cause denial of service (DoS).

## 対策
Properly estimate the gas required for functions and set appropriate gas limits. Use `require` or other mechanisms to handle gas consumption failures and ensure that gas usage remains within acceptable bounds.

### 脆弱なコントラクトの例
```solidity
contract Example {
    function execute() public {
        while (true) { 
            // Excessive gas consumption, no limit set
        }
    }
}
```

### 修正したコントラクトの例
```solidity
contract Example {
    uint public counter;

    function execute(uint _iterations) public {
        require(_iterations <= 100, "Too many iterations"); // Limit iterations to avoid excessive gas usage
        for (uint i = 0; i < _iterations; i++) {
            counter++;
        }
    }
}
```
