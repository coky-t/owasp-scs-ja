---
title: 最小出力バリデーションなしでの流動性の追加/削除 (Add/Remove Liquidity Without Minimum Output Validation)
id: SCWE-151
alias: missing-slippage-protection-liquidity-operations
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation  
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
AMM (Uniswap, Curve など) において、LP トークンまたは原資産の最低受領額を規定せずに流動性を追加または削除すると、ユーザーはサンドウィッチ攻撃や不利な執行にさらされる可能性があります。スワップスリッページ (SCWE-090) と同様に、LP オペレーションはフロントランとなる可能性があります。攻撃者はユーザーの追加/削除の前にプールを操作して、その後反転します。`amountMin` や同等のチェックなしで、ユーザーは予想よりはるかに少ないものしか受けられない可能性があります。

## 対策
- Accept `amountMin` (or `minLPTokens`, `minAmounts`) from users and enforce it when adding/removing liquidity.
- Use deadline parameters (SCWE-141) in addition to slippage protection.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

interface IUniswapV2Router {
    function addLiquidity(address tokenA, address tokenB, uint256 amountADesired, uint256 amountBDesired, uint256 amountAMin, uint256 amountBMin, address to, uint256 deadline) external returns (uint256 amountA, uint256 amountB, uint256 liquidity);
}

contract LiquidityManager {
    function addLiquidity(address tokenA, address tokenB, uint256 amountA, uint256 amountB) external {
        IUniswapV2Router(router).addLiquidity(
            tokenA, tokenB,
            amountA, amountB,
            0, 0,  // amountAMin, amountBMin = 0: no slippage protection
            msg.sender,
            block.timestamp
        );
    }
}
```

### 修正済み
```solidity
function addLiquidity(address tokenA, address tokenB, uint256 amountA, uint256 amountB, uint256 amountAMin, uint256 amountBMin, uint256 deadline) external {
    require(block.timestamp <= deadline, "Expired");
    IUniswapV2Router(router).addLiquidity(tokenA, tokenB, amountA, amountB, amountAMin, amountBMin, msg.sender, deadline);
}
```
