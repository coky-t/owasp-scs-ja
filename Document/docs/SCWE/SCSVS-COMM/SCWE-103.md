---
title: ERC20 承認二重支払い (支給競合) (ERC20 Approval Double-Spend (Allowance Race))
id: SCWE-103
alias: erc20-approval-race
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [362]
status: new
---

## 関連
- CWE-362: Concurrent Execution using Shared Resource with Improper Synchronization (Race Condition)  
  [https://cwe.mitre.org/data/definitions/362.html](https://cwe.mitre.org/data/definitions/362.html) (参考: JVN iPedia [CWE-362 競合状態](https://jvndb.jvn.jp/ja/cwe/CWE-362.html))

## 説明
単一の `approve` 呼び出しで ERC20 allowance を値 `X` から `Y` に変更すると、spender はフロントランを行い、変更前に `X` を支払い、それから後に `Y` を支払うことが可能となり、実質的に二重支払いになります。`increaseAllowance`/`decreaseAllowance` を使用しない実装はこれにさらされます。

## 対策
- allowance リセットパターンに従います。allowance を `0` に設定し、それから新しい値に設定します。
- `increaseAllowance`/`decreaseAllowance` または nonce を用いる EIP-2612 `permit` を優先します。
- 重要なフローには、allowance の更新を検証した後に `transferFrom` を介してトークンを引き出します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;
interface IERC20 { function approve(address,uint256) external returns (bool); }

contract DApp {
    IERC20 public token;

    function changeSpender(address spender, uint256 newAmount) external {
        token.approve(spender, newAmount); // can be front-run
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

contract DApp {
    using SafeERC20 for IERC20;
    IERC20 public token;

    function changeSpender(address spender, uint256 newAmount) external {
        token.safeApprove(spender, 0);      // reset first; SafeERC20 handles non-standard tokens (e.g. USDT)
        token.safeApprove(spender, newAmount);
    }
}
```
