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
この例では、`VulnerableProxy` コントラクトは所有者が実装コントラクトを更新できます。所有者が侵害された場合、プロキシを悪意のある実装に向けることができ、攻撃者がコントラクトのロジックを制御できます。

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
        require(block.timestamp >= lastUpgradeTime + upgradeDelay, "Upgrade not allowed yet");
        _;
    }

    function setImplementation(address _implementation) public onlyOwner upgradeable {
        implementation = _implementation;
        lastUpgradeTime = block.timestamp; // Update the last upgrade time
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

修正した `SecureProxy` コントラクトでは、以下の変更が行われました。

- アクセス制御: `onlyOwner` 修飾子はコントラクト所有者のみがプロキシの実装を更新できるようにします。
- タイムロックメカニズム: `upgradeable` 修飾子は遅延を追加 (この例では 1 日に設定) して、急なコントラクトのアップグレードを防ぎます。このコントラクトは最後のアップグレードから所定の時間が経過されるまでアップグレードできません。
- 安全なアップグレードロジック: プロキシロジックは、必要なアクセス制御とタイムロックチェックを合格した後にのみ更新されます。
