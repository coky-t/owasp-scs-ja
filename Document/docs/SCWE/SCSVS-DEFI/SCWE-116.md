---
title: 供給上限の強制の欠如 (Missing Supply Cap Enforcement)
id: SCWE-116
alias: missing-supply-cap
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-2]
  cwe: [693]
status: new
---

## 関連
- CWE-693: Protection Mechanism Failure  
  [https://cwe.mitre.org/data/definitions/693.html](https://cwe.mitre.org/data/definitions/693.html)

## 説明
厳格な供給上限なしでのトークンやレンディングシステムは、特権アカウントや欠陥のあるロジックが無制限にユニットを生成することを可能にし、供給を膨らませ、担保されたプロトコルを枯渇します。また、上限の欠落は AMM やオラクルの価格想定を破壊します。

## 対策
- 不変の最大供給を定義し、すべてのミントパスでそれを強制します。
- マルチシグとタイムロックを用いてミントのロールを分離し、ミントの変更に関するイベントを発行します。
- アップグレードとクロスチェーンのミント/ブリッジのロジックに上限チェックを含めます。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Token {
    address public owner;
    uint256 public totalSupply;

    function mint(address to, uint256 amount) external {
        require(msg.sender == owner, "not owner");
        totalSupply += amount; // no cap
        // mint tokens...
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Token {
    address public owner;
    uint256 public totalSupply;
    uint256 public constant MAX_SUPPLY = 1_000_000 ether;

    function mint(address to, uint256 amount) external {
        require(msg.sender == owner, "not owner");
        require(totalSupply + amount <= MAX_SUPPLY, "cap exceeded");
        totalSupply += amount;
        // mint tokens...
    }
}
```
