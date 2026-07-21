---
title: アップグレード可能なコントラクトでの初期化のフロントランニング (Initialization Front-Running in Upgradeable Contracts)
id: SCWE-098
alias: initializer-front-running
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
プロキシベースのコントラクトはコンストラクタなしで出荷します。初期化子が任意のものから呼び出し可能である場合、攻撃者はデプロイメントをフロントランして所有権を奪い、信頼できるロールを設定したり、アップグレードをロックできます。これはプロキシの背後にある実装を恒久的に侵害します。

## 対策
- Protect initializer functions with access control and `initializer`/`reinitializer` guards.
- Invoke initialization atomically during deployment or via a deploy-time call data payload.
- Use `disableInitializers()` on the implementation contract to prevent accidental takeover.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

contract UpgradeableVault is Initializable {
    address public owner;

    function initialize(address _owner) public initializer {
        owner = _owner; // anyone can front-run and become owner
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

contract UpgradeableVault is Initializable {
    address public owner;

    function initialize(address _owner, address deployer) public initializer {
        require(msg.sender == deployer, "only deployer");
        owner = _owner;
    }
}
```
Deployer (or factory) deploys the proxy and calls `initialize(owner, address(this))` in the same transaction — no window for front-running.
