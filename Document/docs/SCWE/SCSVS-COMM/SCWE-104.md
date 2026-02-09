---
title: 保護されていない ERC777 トークンフック (Unprotected ERC777 Token Hooks)
id: SCWE-104
alias: unprotected-erc777-hooks
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [841]
status: new
---

## 関連
- CWE-841: Improper Enforcement of Behavioral Workflow  
  [https://cwe.mitre.org/data/definitions/841.html](https://cwe.mitre.org/data/definitions/841.html)

## 説明
ERC777 tokens trigger `tokensReceived` hooks on recipients. Contracts that accept tokens without reentrancy protection or without blocking ERC777 hooks can be reentered during token transfers, enabling double-withdrawals or bypassing invariants.

## 対策
- Add `nonReentrant` guards around token transfer handlers and withdrawals.
- Block unexpected token types or disable ERC777 acceptance by rejecting the ERC1820 interface.
- Use pull-based withdrawals and avoid performing sensitive logic inside token receive hooks.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;
interface IERC777 { function send(address,uint256,bytes calldata) external; }

contract Rewards {
    uint256 public totalPaid;

    function claim(address token, uint256 amount) external {
        IERC777(token).send(msg.sender, amount, ""); // ERC777 hook can reenter
        totalPaid += amount;
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/security/ReentrancyGuard.sol"; // OZ 5.x: utils/ReentrancyGuard.sol

interface IERC20 {
    function transfer(address to, uint256 amount) external returns (bool);
}

contract Rewards is ReentrancyGuard {
    mapping(address => bool) public allowedTokens; // only non-ERC777 tokens
    uint256 public totalPaid;

    function claim(address token, uint256 amount) external nonReentrant {
        require(allowedTokens[token], "token not allowed"); // block ERC777
        totalPaid += amount;
        IERC20(token).transfer(msg.sender, amount);
    }
}
```
