---
title: 安全でないアップグレード可能なプロキシ設計 (Insecure Upgradeable Proxy Design)
id: SCWE-005
alias: insecure-upgradeable-proxy-design
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-2]
  cwe: [668]
status: new
---

## 関連
- CWE-668: Exposure of Resource to Wrong Sphere  
  [https://cwe.mitre.org/data/definitions/668.html](https://cwe.mitre.org/data/definitions/668.html)

## 説明
全でないアップグレード可能なプロキシ設計は、スマートコントラクトがアップグレードを適切に保護または検証せずにアップグレード可能なプロキシパターンを実装する場合に発生します。これは認可されていないアクターがコントラクトのロジックを変更し、悪意のある動作を引き起こす可能性があります。これは、アップグレード機能に適切なアクセス制御がない場合や、アップグレードを遅らせるためのタイムロックがない場合によく発生し、悪意のあるアクターがコントラクトを悪用する機会を与えます。

この脆弱性は、コントラクト呼び出しを悪意のあるロジックにリダイレクトしたり、コントラクトの完全性を損なう不正な更新を行うなど、重大な障害につながる可能性があります。

## 対策
- **アクセス制御:** 信頼できる関係者 (コントラクト所有者、マルチシグウォレットなど) のみがアップグレードを実行できるようにします。
- **タイムロックメカニズム:** アップグレードを遅らせ、透明性を提供するためにタイムロックを実装します。
- **透過プロキシパターン:** 透過プロキシパターンなど、不正なコントラクトロジックの変更を防ぐパターンを使用します。
- **プロキシロジックを定期的に監査する:** 定期的に監査を実施して、アップグレードメカニズムが安全であり、ベストプラクティスに準拠していることを確認します。

## 事例

### 脆弱なプロキシコントラクト

```solidity
pragma solidity ^0.4.0;

contract VulnerableProxy {
    address public implementation;
    address public owner;

    function setImplementation(address _implementation) public {
        require(msg.sender == owner, "Only owner can set implementation");
        implementation = _implementation;
    }

    function () public payable {
        address _impl = implementation;
        require(_impl != address(0), "Implementation address is zero");
        assembly {
            let result := delegatecall(gas, _impl, add(msg.data, 0x20), mload(msg.data), 0, 0)
            let size := returndatasize
            let ptr := mload(0x40)
            return(ptr, size)
        }
    }
}
```
In this example, the `VulnerableProxy` contract allows the owner to update the implementation contract. If the owner is compromised, they can point the proxy to a malicious implementation, allowing the attacker to control the contract's logic.

### 安全なアップグレードメカニズムを備えたプロキシコントラクトに修正したもの

```solidity

pragma solidity ^0.4.0;

contract SecureProxy {
    address public implementation;
    address public owner;
    uint public lastUpgradeTime;
    uint public upgradeDelay = 1 days;  // 24 hours delay before upgrade

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can perform this action");
        _;
    }

    modifier upgradeable() {
        require(now >= lastUpgradeTime + upgradeDelay, "Upgrade not allowed yet");
        _;
    }

    function setImplementation(address _implementation) public onlyOwner upgradeable {
        implementation = _implementation;
        lastUpgradeTime = now; // Update the last upgrade time
    }

    function () public payable {
        address _impl = implementation;
        require(_impl != address(0), "Implementation address is zero");
        assembly {
            let result := delegatecall(gas, _impl, add(msg.data, 0x20), mload(msg.data), 0, 0)
            let size := returndatasize
            let ptr := mload(0x40)
            return(ptr, size)
        }
    }
}
```

In the fixed `SecureProxy` contract, the following changes have been made:

- Access Control: The `onlyOwner` modifier ensures that only the contract owner can update the proxy’s implementation.
- Timelock Mechanism: The `upgradeable` modifier adds a delay (set to 1 day in this example) to prevent rapid contract upgrades. The contract cannot be upgraded until the specified time has passed since the last upgrade.
- Secure Upgrade Logic: The proxy logic is updated only after passing the necessary access control and timelock checks.
