---
title: 外部呼び出しでの無制限のループによるガス消耗 (Gas Exhaustion via Unbounded Loops with External Calls)
id: SCWE-148
alias: unbounded-external-call-loops
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-DEFI]
  scsvs-scg: [SCSVS-DEFI-1]
  cwe: [400]
status: new
---

## 関連
- CWE-400: Uncontrolled Resource Consumption  
  [https://cwe.mitre.org/data/definitions/400.html](https://cwe.mitre.org/data/definitions/400.html)

## 説明
ユーザーが制御する配列を反復処理し、各反復処理で外部呼び出し (転送、承認、その他のコントラクト呼び出し) を実行するループは、配列が大きい場合にガスを枯渇する可能性があります。各外部呼び出しはガスを消費します。無制限のループはトランザクションがブロックガス制限にヒットして元に戻し、サービス拒否を引き起こします。SCWE-109 は無制限ループ全般をカバーしていますが、この弱点は外部呼び出しを実行するループに焦点を当てています。

## 対策
- Impose an upper bound on loop iterations or batch size.
- Use pull-based patterns: let users claim individually instead of pushing to many addresses in one tx.
- Process in chunks with pagination or checkpoints.

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Airdrop {
    function distribute(address[] calldata recipients, uint256 amount) external {
        for (uint256 i = 0; i < recipients.length; i++) {
            (bool ok, ) = recipients[i].call{value: amount}("");
            require(ok, "Transfer failed");
        }
    }
}
```
**Why vulnerable:** Large `recipients.length` causes out-of-gas; one failed transfer reverts the entire batch.

### 修正済み
```solidity
uint256 public constant MAX_BATCH = 100;

function distribute(address[] calldata recipients, uint256 amount) external {
    require(recipients.length <= MAX_BATCH, "Batch too large");
    for (uint256 i = 0; i < recipients.length; i++) {
        (bool ok, ) = recipients[i].call{value: amount}("");
        require(ok, "Transfer failed");
    }
}
```
