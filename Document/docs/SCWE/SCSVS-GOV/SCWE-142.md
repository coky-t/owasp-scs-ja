---
title: 予測可能なトランザクション順序から抽出可能な値 (Extractable Value from Predictable Transaction Ordering)
id: SCWE-142
alias: mev-exposure-transaction-ordering
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-GOV]
  scsvs-scg: [SCSVS-GOV-3]
  cwe: [362]
status: new
---

## 関連
- CWE-362: Concurrent Execution using Shared Resource with Improper Synchronization ('Race Condition')  
  [https://cwe.mitre.org/data/definitions/362.html](https://cwe.mitre.org/data/definitions/362.html)

## 説明
Contracts that create predictable, orderable state changes expose value to block builders and searchers (MEV). Sandwich attacks, backrunning, and liquidation front-running are examples. When a user's transaction has a predictable effect (e.g., large swap moving price), an attacker can place transactions before and after to extract value. SCWE-037 covers front-running; this weakness addresses the broader MEV surface—design choices that make extraction easy.

## 対策
- Use private mempools or commit-reveal schemes for sensitive transactions.
- Implement slippage protection (SCWE-090) and deadline parameters (SCWE-141).
- Consider batch auctions, fair ordering, or MEV-aware design (e.g., CoW Protocol) where applicable.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract SimpleSwap {
    function swap(uint256 amountIn, uint256 minOut) external {
        // Predictable: searcher sees large amountIn, sandwiches with buy-before and sell-after
        uint256 out = getAmountOut(amountIn);
        require(out >= minOut, "Slippage");
        transferOut(msg.sender, out);
    }
}
```

### 修正済み
```solidity
function swap(uint256 amountIn, uint256 minOut, uint256 deadline) external {
    require(block.timestamp <= deadline, "Expired");
    uint256 out = getAmountOut(amountIn);
    require(out >= minOut, "Slippage");
    transferOut(msg.sender, out);
}
```
**Fix:** User-supplied `deadline` and `minOut` limit MEV extraction. Use DEX aggregators or private order flow for additional protection.
