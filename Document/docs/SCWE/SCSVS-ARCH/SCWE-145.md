---
title: 検証されていないコンストラクタパラメータ (Unvalidated Constructor Parameters)
id: SCWE-145
alias: missing-constructor-input-validation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
重要なパラメータ (所有者、オラクル、手数料受取人、トークンアドレス) をバリデーションなしで受け入れるコンストラクタは、不備のある状態や安全でない状態でコントラクトをデプロイする恐れがあります。ゼロアドレス、無効な値、矛盾した設定 (手数料が 100% を超えるなど) は、セッターやアップグレードのパスがなければ、デプロイメント後に修正することが不可能になるかもしれません。

## 対策
- Validate all constructor parameters: zero address checks, range checks (e.g., fee <= 100%), and consistency checks.
- Use `require` or custom errors to revert deployment with a clear message when validation fails.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Staking {
    address public owner;
    address public rewardToken;
    uint256 public feeBps;

    constructor(address _owner, address _rewardToken, uint256 _feeBps) {
        owner = _owner;           // No validation
        rewardToken = _rewardToken;
        feeBps = _feeBps;         // Could be > 10000
    }
}
```

### 修正済み
```solidity
constructor(address _owner, address _rewardToken, uint256 _feeBps) {
    require(_owner != address(0), "Invalid owner");
    require(_rewardToken != address(0), "Invalid token");
    require(_feeBps <= 10000, "Fee too high");
    owner = _owner;
    rewardToken = _rewardToken;
    feeBps = _feeBps;
}
```
