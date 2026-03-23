---
title: 寄付による ERC4626 株価インフレーション (ERC4626 Share Inflation via Donations)
id: SCWE-135
alias: erc4626-donation-inflation
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMP]
  scsvs-scg: [SCSVS-COMP-1]
  cwe: [682]
status: new
---

## 関連
- CWE-682: Incorrect Calculation  
  [https://cwe.mitre.org/data/definitions/682.html](https://cwe.mitre.org/data/definitions/682.html)

## 説明
無償資産寄付に対しての対策がない ERC4626 vault は `totalAssets` と株価を歪める可能性があります。攻撃者は資産を寄付して株価を吊り上げ、正規化前に安価に株式を発行して、既存の保有者から利益を搾取できます。

## 対策
- 現在の `totalAssets` を使用して、預け入れ/発行ごとに株価を正規化します。
- 任意で、直接送金を元に戻したり、新規発行前に準備金を振り返ることで、不当な寄付をブロックします。
- 寄付と株価のエッジケースに対するテストを追加します。

## 事例

### 脆弱
```solidity
// totalAssets() uses balanceOf; donations inflate it and skew share price
function mint(uint256 shares) external {
    uint256 assets = previewMint(shares);
    asset.transferFrom(msg.sender, address(this), assets);
    _mint(msg.sender, shares);
}
```

### 修正済み
```solidity
uint256 private _accountedAssets; // internal balance; excludes direct transfers

function totalAssets() public view override returns (uint256) {
    return _accountedAssets; // donations do not affect share price
}

function deposit(uint256 assets, address receiver) public override returns (uint256 shares) {
    uint256 before = asset.balanceOf(address(this));
    shares = super.deposit(assets, receiver);
    _accountedAssets += (asset.balanceOf(address(this)) - before);
}

function withdraw(uint256 assets, address receiver, address owner) public override returns (uint256 shares) {
    shares = super.withdraw(assets, receiver, owner);
    _accountedAssets -= assets;
}
```
