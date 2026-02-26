---
title: Fee-On-Transfer トークンの勘定ミス (Fee-On-Transfer Token Misaccounting)
id: SCWE-110
alias: fee-on-transfer-misaccounting
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMP]
  scsvs-scg: [SCSVS-COMP-1]
  cwe: [682]
status: new
---

## 関連
- CWE-682: Incorrect Calculation  
  [https://cwe.mitre.org/data/definitions/682.html](https://cwe.mitre.org/data/definitions/682.html)

## 説明
ERC20 送金で要求額全額を移動すると想定すると、fee-on-transfer やデフレトークンでは失敗します。受領額ではなく要求額をユーザーにクレジットするプロトコルでは残高を流出したり勘定ミスする可能性があります。

## 対策
- Measure token balances before and after transfers to calculate the actual received amount.
- Maintain allowlists/blocks for incompatible tokens or handle fee-on-transfer explicitly.
- Validate that accounting uses the net amount when updating shares or debts.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Vault {
    IERC20 public token;
    mapping(address => uint256) public deposits;

    function deposit(uint256 amount) external {
        token.transferFrom(msg.sender, address(this), amount);
        deposits[msg.sender] += amount; // credits full amount even if fee applied
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Vault {
    IERC20 public token;
    mapping(address => uint256) public deposits;

    function deposit(uint256 amount) external {
        uint256 beforeBal = token.balanceOf(address(this));
        token.transferFrom(msg.sender, address(this), amount);
        uint256 received = token.balanceOf(address(this)) - beforeBal;
        deposits[msg.sender] += received; // credit net amount
    }
}
```
