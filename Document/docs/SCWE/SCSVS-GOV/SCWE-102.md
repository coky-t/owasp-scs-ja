---
title: Checks-Effects-Interactions パターンの欠如 (Missing Checks-Effects-Interactions Pattern)
id: SCWE-102
alias: missing-checks-effects-interactions
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [841]
status: new
---

## 関連
- CWE-841: Improper Enforcement of Behavioral Workflow  
  [https://cwe.mitre.org/data/definitions/841.html](https://cwe.mitre.org/data/definitions/841.html)

## 説明
Calling external contracts before updating local state breaks the checks-effects-interactions (CEI) pattern. Attackers can reenter or observe stale state to perform double-withdrawals, bypass limits, or corrupt accounting.

## 対策
- Perform input validation and state updates before external calls.
- Use reentrancy guards on functions that transfer value or call untrusted contracts.
- Prefer pull-based withdrawals to avoid pushing funds inside complex flows.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Payout {
    mapping(address => uint256) public balances;

    function withdraw() external {
        (bool ok, ) = msg.sender.call{value: balances[msg.sender]}("");
        require(ok, "send failed");       // external call first
        balances[msg.sender] = 0;         // state updated after
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/security/ReentrancyGuard.sol"; // OZ 5.x: utils/ReentrancyGuard.sol

contract Payout is ReentrancyGuard {
    mapping(address => uint256) public balances;

    function withdraw() external nonReentrant {
        uint256 amount = balances[msg.sender];
        balances[msg.sender] = 0;         // effects first
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "send failed");       // interaction last
    }
}
```
