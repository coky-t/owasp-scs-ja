---
title: 高価値ランダム性のための block.prevrandao への依存 (Reliance on block.prevrandao for High-Value Randomness)
id: SCWE-153
alias: unsafe-block-prevrandao-value-at-stake
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BLOCK]
  scsvs-scg: [SCSVS-BLOCK-1]
  cwe: [330]
status: new
---

## 関連
- CWE-330: Use of Insufficiently Random Values  
  [https://cwe.mitre.org/data/definitions/330.html](https://cwe.mitre.org/data/definitions/330.html)

## 説明
マージ後 (Ethereum PoS)、`block.difficulty` は `block.prevrandao` に置き換えられました。どちらもバリデータによって操作可能であり、プロトコルルール内で値に影響を与える可能性があります。`block.prevrandao` (または `block.difficulty`) を高価値ランダム性 (くじ、エアドロップ、当選者選出) に使用すると、バリデータや教養のあるアクターが結果を予測したりバイアスをかけることが可能になります。SCWE-024 と SCWE-084 はブロックハッシュ/タイムスタンプをカバーしており、この弱点は特に `block.prevrandao` に対処しています。

## 対策
- value-at-stake のランダム性には `block.prevrandao` や `block.difficulty` を使用してはいけません。
- Chainlink VRF、commit-reveal スキーム、またはその他の検証可能なランダム性ソースを使用します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Lottery {
    address[] public participants;

    function pickWinner() external view returns (uint256) {
        return uint256(keccak256(abi.encodePacked(block.prevrandao, block.timestamp))) % participants.length;
    }
}
```
**Why vulnerable:** `block.prevrandao` is manipulable by validators; they can influence the value to bias the outcome.

### 修正済み
Use Chainlink VRF V2 or a commit-reveal scheme where participants commit hashes before the random value is known. See SCWE-024 for a Chainlink VRF example.
