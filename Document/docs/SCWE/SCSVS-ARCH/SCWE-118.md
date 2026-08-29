---
title: 認証されていないビーコンのアップグレード (Unauthenticated Beacon Upgrade)
id: SCWE-118
alias: unauthenticated-beacon-upgrade
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [306]
status: new
---

## 関連
- CWE-306: Missing Authentication for Critical Function  
  [https://cwe.mitre.org/data/definitions/306.html](https://cwe.mitre.org/data/definitions/306.html)

## 説明
ビーコンプロキシは実装を決定するビーコンアドレスに依存しています。ビーコンのアップグレード機能に適切なアクセス制御やタイムロックがない場合、攻撃者はすべてのプロキシを悪意のあるコードに向けて、状態と資金を乗っ取ることができます。

## 対策
- Restrict beacon upgrades to multisig+timelock and emit events on change.
- Validate new implementation bytecode (e.g., initializer disabled, interfaces intact).
- Monitor beacon address changes on-chain with alerts.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Beacon {
    address public impl;
    function upgradeTo(address newImpl) external { // no auth
        impl = newImpl;
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Beacon {
    address public impl;
    address public admin;
    function upgradeTo(address newImpl) external {
        require(msg.sender == admin, "not admin");
        impl = newImpl;
    }
}
```
