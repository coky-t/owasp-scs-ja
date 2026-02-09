---
title: 資金を受け取ることができないアドレスへの送金 (Transfers to Addresses That Cannot Receive Funds)
id: SCWE-149
alias: lack-of-recipient-validation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-2]
  cwe: [755]
status: new
---

## 関連
- CWE-755: Improper Handling of Exceptional Conditions  
  [https://cwe.mitre.org/data/definitions/755.html](https://cwe.mitre.org/data/definitions/755.html)

## 説明
Sending ETH or tokens to addresses that cannot receive them (e.g., contracts with no `receive`/`fallback`, or that revert on receive) causes the transfer to fail. In push-based distributions (e.g., airdrops, reward payouts), if one recipient cannot receive, the entire transaction reverts—leading to denial of service for all recipients. Pull-based patterns avoid this by letting users claim individually.

## 対策
- Prefer pull-based over push-based distributions.
- If push-based is required, use `try/catch` to skip failing recipients and track failures, or validate recipients (e.g., reject contracts that don't implement a receiver interface).

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract RewardDistributor {
    function distribute(address[] calldata users, uint256[] calldata amounts) external {
        for (uint256 i = 0; i < users.length; i++) {
            (bool ok, ) = users[i].call{value: amounts[i]}("");
            require(ok, "Transfer failed");  // One failure reverts entire batch
        }
    }
}
```

### 修正済み (プルベース)
```solidity
mapping(address => uint256) public pendingRewards;

function claim() external {
    uint256 amount = pendingRewards[msg.sender];
    require(amount > 0, "Nothing to claim");
    pendingRewards[msg.sender] = 0;
    (bool ok, ) = msg.sender.call{value: amount}("");
    require(ok, "Transfer failed");
}
```
