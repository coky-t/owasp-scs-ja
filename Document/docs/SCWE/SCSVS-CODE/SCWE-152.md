---
title: 情報漏洩や間違った元に戻す動作につながるカスタムエラーの誤用 (Misuse of Custom Errors Leading to Information Leakage or Wrong Revert Behavior)
id: SCWE-152
alias: incorrect-custom-errors-usage
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-CODE]
  scsvs-scg: [SCSVS-CODE-1]
  cwe: [209]
status: new
---

## 関連
- CWE-209: Generation of Error Message Containing Sensitive Information  
  [https://cwe.mitre.org/data/definitions/209.html](https://cwe.mitre.org/data/definitions/209.html)

## 説明
Custom errors (Solidity 0.8.4+) save gas but can expose internal state if parameters include sensitive data (e.g., balances, private keys, internal addresses). Using the wrong error in a revert can also mislead integrators or hide the actual failure reason. Custom errors are part of the revert payload and can be decoded off-chain.

## 対策
- Avoid including sensitive data in custom error parameters.
- Use generic error messages for external-facing reverts when the internal reason is confidential.
- Ensure error selection matches the actual failure condition.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Vault {
    error InsufficientBalance(uint256 requested, uint256 available);

    function withdraw(uint256 amount) external {
        uint256 balance = balances[msg.sender];
        if (amount > balance) {
            revert InsufficientBalance(amount, balance);  // Exposes user balance
        }
        balances[msg.sender] -= amount;
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "Transfer failed");
    }
}
```

### 修正済み
```solidity
error InsufficientBalance();

function withdraw(uint256 amount) external {
    if (amount > balances[msg.sender]) {
        revert InsufficientBalance();
    }
    // ...
}
```
