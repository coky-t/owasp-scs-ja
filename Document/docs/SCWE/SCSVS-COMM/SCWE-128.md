---
title: 安全でないマルチコールコンテキスト転送 (Insecure Multicall Context Forwarding)
id: SCWE-128
alias: insecure-multicall-context
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
Multicall-style aggregators that forward calls without guarding against reentrancy or context changes let attackers reorder actions within one tx (e.g., deposit then withdraw) or impersonate `msg.sender` when inner calls use `tx.origin` or cached sender state.

## 対策
- Apply reentrancy guards around multicall entrypoints.
- Avoid caching `msg.sender` across calls; pass explicit sender/context to internal functions.
- Restrict callable selectors/targets or enforce allowlists.

## 事例

### 脆弱
```solidity
function multicall(bytes[] calldata data) external {
    for (uint i; i < data.length; i++) {
        (bool ok, ) = address(this).delegatecall(data[i]);
        require(ok, "fail");
    }
}
```

### 修正済み
```solidity
function multicall(bytes[] calldata data) external nonReentrant {
    for (uint i; i < data.length; i++) {
        _dispatch(msg.sender, data[i]); // explicit context, no delegatecall loops
    }
}
```
