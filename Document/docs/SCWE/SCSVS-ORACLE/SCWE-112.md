---
title: 低流動性のスポット価格への依存 (Reliance on Low-Liquidity Spot Prices)
id: SCWE-112
alias: low-liquidity-spot-price
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ORACLE]
  scsvs-scg: [SCSVS-ORACLE-2]
  cwe: [346]
status: new
---

## 関連
- CWE-346: Origin Validation Error  
  [https://cwe.mitre.org/data/definitions/346.html](https://cwe.mitre.org/data/definitions/346.html)

## 説明
非流動性のプールから単一のオンチェーン DEX スポット価格を使用すると、攻撃者は少額の取引やフラッシュローンで価格を操作し、それから、過大評価/過小評価された評価額を悪用して貸付、清算、またはスワップを行います。

## 対策
- Require minimum liquidity thresholds and sanity bounds before trusting a pool.
- Use robust oracles (Chainlink, TWAP, median of multiple feeds) instead of raw spot prices.
- Apply deviation checks against reference feeds and revert when deviation exceeds limits.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Lending {
    IUniswapV2Pair public pair;

    function getPrice() public view returns (uint256) {
        (uint112 r0, uint112 r1,) = pair.getReserves();
        return uint256(r1) * 1e18 / r0; // trusts small pool
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Lending {
    IAggregatorV3 public chainlink;

    function getPrice() public view returns (uint256) {
        (, int256 price,,,) = chainlink.latestRoundData();
        require(price > 0, "stale");
        return uint256(price);
    }
}
```
