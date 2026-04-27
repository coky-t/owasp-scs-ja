---
title: 不十分な TWAP ウィンドウまたは単一観測 (Insufficient TWAP Window or Single Observation)
id: SCWE-113
alias: insufficient-twap-window
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
非常に短いウィンドウや単一の累積観測値を使用する TWAP オラクルは、ブロック内または数回のトレード内で操作される可能性があります。攻撃者は平均価格を一時的に変動して、有利なレートで借入、清算、発行できます。

## 対策
- Use sufficiently long averaging windows and multiple observations with minimum elapsed time.
- Enforce maximum per-block updates to prevent same-block manipulation.
- Cross-check TWAP output against external reference feeds or deviation thresholds.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

interface IUniswapV3Pool {
    function observe(uint32[] calldata secondsAgos)
        external view returns (int56[] memory tickCumulatives, uint160[] memory);
}

contract PriceFeed {
    IUniswapV3Pool public pool;

    function twap() external view returns (int56) {
        uint32[] memory secondsAgos = new uint32[](1);
        secondsAgos[0] = 0; // single observation at current block — manipulatable in same block
        (int56[] memory tickCumulatives,) = pool.observe(secondsAgos);
        return tickCumulatives[0];
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

interface IUniswapV3Pool {
    function observe(uint32[] calldata secondsAgos)
        external view returns (int56[] memory tickCumulatives, uint160[] memory);
}

contract PriceFeed {
    IUniswapV3Pool public pool;
    uint32 public constant TWAP_WINDOW = 1 hours;

    function twap() external view returns (int24) {
        uint32[] memory secondsAgos = new uint32[](2);
        secondsAgos[0] = TWAP_WINDOW; // start of window
        secondsAgos[1] = 0;           // current block
        (int56[] memory tickCumulatives,) = pool.observe(secondsAgos);
        int56 tickDelta = tickCumulatives[1] - tickCumulatives[0];
        return int24(tickDelta / int56(uint56(TWAP_WINDOW)));
    }
}
```
