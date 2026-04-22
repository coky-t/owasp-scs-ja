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
- プールを信頼する前に、最低限の流動性閾値と健全性境界を要求します。
- 生のスポット価格ではなく、堅牢なオラクル (Chainlink、TWAP、複数フィードの中央値) を使用します。
- 参照フィードに対する逸脱チェックを適用し、逸脱が制限を超えた場合に元に戻します。

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
