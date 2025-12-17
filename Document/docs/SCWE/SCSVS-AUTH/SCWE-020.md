---
title: 時間ロック機能の欠如 (Absence of Time-Locked Functions)
id: SCWE-020
alias: absence-time-locked-functions
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-AUTH]
  scsvs-scg: [SCSVS-AUTH-2]
  cwe: [840]
status: new
---

## 関連
- CWE-840: Business Logic Errors  
  [https://cwe.mitre.org/data/definitions/840.html](https://cwe.mitre.org/data/definitions/840.html)

## 説明
時間ロック機能の欠如はスマートコントラクトが時間条件に基づいて特定の重要な機能やアクションを遅延するメカニズムを実装していない場合に発生します。これは、緊急資金引き落としや機密性の高い管理アクションなど、一定期間の制限や延期されるべきアクションをコントラクトが許可することにつながる可能性があります。時間ロックメカニズムなしでは、悪意のある人物や欠陥のあるロジックがこれらのアクションを不適切な時にトリガーし、望ましくない結果につながる可能性があります。

時間ロックは以下のようなシナリオでよく使用されます。
- 管理者または所有者の機能を遅らせて、即時の不正使用を防ぐ。
- 事前定義された遅延後にのみ、引き落としや資金移動が発生するようにする。
- 特定の時間後または特定の条件下でのみ、緊急アクションを有効にする。

## 対策
- **Implement time-locked functions:** Ensure that critical functions, especially those related to fund transfers or administrative actions, are protected by a time lock, delaying their execution until an appropriate time has passed.
- **Use block timestamps:** Leverage block timestamps or block numbers to control the execution of functions.
- **Limit emergency access:** If implementing emergency mechanisms, ensure that there are time delays before they can be invoked, to reduce the chance of misuse.
- **Review time-based logic regularly:** Verify the correctness of time-based logic to ensure that the system behaves as intended.

## 事例

### 時間ロック機能の欠如

```solidity
pragma solidity ^0.8.0;

contract NoTimeLock {
    address public owner;
    uint public funds;

    constructor() public {
        owner = msg.sender;
        funds = 1000;
    }

    // Critical function without time-lock
    function withdrawFunds(uint amount) public {
        require(msg.sender == owner, "Not the owner");
        funds -= amount;
    }
}
```
In this example, the contract lacks any time-lock mechanism, allowing the owner to withdraw funds at any time, which could be dangerous if mishandled. 

### 時間ロック機能の実装
```solidity
pragma solidity ^0.8.0;

contract TimeLockExample {
    address public owner;
    uint public funds;
    uint public lastWithdrawalTime;
    uint public withdrawalDelay = 1 weeks;

    constructor() public {
        owner = msg.sender;
        funds = 1000;
        lastWithdrawalTime = now;
    }

    // Critical function with time-lock
    function withdrawFunds(uint amount) public {
        require(msg.sender == owner, "Not the owner");
        require(now >= lastWithdrawalTime + withdrawalDelay, "Time lock not expired");
        
        lastWithdrawalTime = now;
        funds -= amount;
    }
}
```
In the fixed version, a time-lock mechanism is implemented, ensuring that the owner can only withdraw funds after a certain delay (e.g., one week). This provides an added layer of security, particularly in cases where emergency withdrawals or administrative actions are necessary but should not be immediate.
