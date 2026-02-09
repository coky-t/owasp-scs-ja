---
title: 共有プロキシ管理者とロジック所有者キー (Shared Proxy Admin and Logic Owner Key)
id: SCWE-119
alias: shared-admin-logic-owner
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
Using the same key to control both the proxy admin (upgrade rights) and logic contract owner concentrates power. A single key compromise allows hostile upgrades and privileged function abuse with no separation of duties.

## 対策
- Separate roles: proxy admin under multisig+timelock; logic owner under different multisig.
- Use role-based access (e.g., OZ AccessControl) and distinct keys for operational vs. upgrade actions.
- Document and monitor role boundaries; rotate keys periodically.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Proxy {
    address public admin; // same key for upgrades and logic owner
    address public implementation;

    modifier onlyAdmin() { require(msg.sender == admin, "not admin"); _; }

    function upgrade(address impl) external onlyAdmin { implementation = impl; }
    function setParam(uint256 x) external onlyAdmin { /* ... */ }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Proxy {
    address public proxyAdmin;   // timelock + multisig; upgrade rights only
    address public logicOwner;  // separate multisig; operational params only
    address public implementation;

    modifier onlyProxyAdmin() { require(msg.sender == proxyAdmin, "not proxy admin"); _; }
    modifier onlyLogicOwner() { require(msg.sender == logicOwner, "not logic owner"); _; }

    function upgrade(address impl) external onlyProxyAdmin { implementation = impl; }
    function setParam(uint256 x) external onlyLogicOwner { /* ... */ }
}
```
