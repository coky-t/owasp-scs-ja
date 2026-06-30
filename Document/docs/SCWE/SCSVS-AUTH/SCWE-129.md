---
title: ローテーションなしでの単一の EOA 管理者 (Single EOA Admin Without Rotation)
id: SCWE-129
alias: single-admin-eoa
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-1]
  cwe: [284]
status: new
---

## 関連
- CWE-284: Improper Access Control  
  [https://cwe.mitre.org/data/definitions/284.html](https://cwe.mitre.org/data/definitions/284.html)

## 説明
単一の外部所有アカウント (EOA) をコントラクト管理者として依存すると単一障害点を生み出します。鍵の侵害、紛失、強制は、不可逆なアップグレード、一時停止、あるいは回復できない資金の流出につながる可能性があります。

## 対策
- Use multisig (>=2-of-3) with hardware keys and timelocks for admin roles.
- Document and test key rotation and recovery procedures.
- Limit admin powers by role (upgrade vs. ops) and enforce separation of duties.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Gov {
    address public admin; // single EOA — key loss or compromise = no recovery

    modifier onlyAdmin() { require(msg.sender == admin, "not admin"); _; }
    function upgrade(address impl) external onlyAdmin { /* ... */ }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Gov {
    IMultisig public admin;     // 2-of-3 multisig with hardware keys
    ITimelock public timelock;  // 48h delay for upgrades

    function proposeUpgrade(address impl) external {
        require(admin.isOwner(msg.sender), "not owner");
        timelock.schedule(impl);
    }
    function executeUpgrade() external {
        timelock.execute(); // only after delay; supports key rotation
    }
}
```
