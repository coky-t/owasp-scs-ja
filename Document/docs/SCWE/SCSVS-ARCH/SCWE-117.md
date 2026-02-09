---
title: プロキシ実装の自己破壊の露出 (Proxy Implementation Selfdestruct Exposure)
id: SCWE-117
alias: proxy-implementation-selfdestruct
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [284]
status: new
---

## 関連
- CWE-284: Improper Access Control  
  [https://cwe.mitre.org/data/definitions/284.html](https://cwe.mitre.org/data/definitions/284.html)

## 説明
If the proxy’s implementation contract exposes `selfdestruct` (or `SELFDESTRUCT` reachable through a function), an attacker or careless admin can destroy the implementation. The proxy then points to a non-existent code address, bricking upgrades or locking funds.

## 対策
- Remove or disable `selfdestruct` in implementations; use `disableInitializers()` patterns.
- Gate any destruct-like functionality behind timelock + multisig and migration plans.
- Monitor implementation addresses and block upgrades that reduce code size to zero.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Impl {
    function kill() external {
        selfdestruct(payable(msg.sender));
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Impl {
    // no selfdestruct path; migrations use new proxy with state copy
}
```
