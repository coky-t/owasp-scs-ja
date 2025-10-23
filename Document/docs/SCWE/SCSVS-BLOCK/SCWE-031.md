---
title: ブロック変数の安全でない使用 (Insecure use of Block Variables)
id: SCWE-031
alias: insecure-block-timestamp-usage
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BLOCK]
  scsvs-scg: [SCSVS-BLOCK-2]
  cwe: [682]
status: new
---

## 関連
- **CWE-682: Incorrect Calculation**  
  [https://cwe.mitre.org/data/definitions/682.html](https://cwe.mitre.org/data/definitions/682.html)

## 説明
Ethereum のようなブロックチェーンネットワークでは、ブロック変数 `(block.timestamp, block.number, block.difficulty など)` がブロックチェーンの現在の状態に関する情報を提供します。しかし、これらの値は完全に決定論的ではなく、マイナーによって操作される可能性があり、スマートコントラクトの脆弱性につながります。

ブロックのタイムスタンプは正確性や一貫性があることを保証されておらず、マイナーは一定の範囲で影響を及ぼすことができます。トークン配布、アクセス制御、その他の時間的制約のあるイベントなど、重要な機能の正確なタイミングにコントラクトが依存している場合、これは問題を引き起こす可能性があります。

安全でないタイムスタンプの使用から生じる潜在的な問題には以下があります。

- タイムスタンプ操作: マイナーは `block.timestamp` をわずかに改変して、時間的制約のあるロジック (オークション、トークン配布、ステーキング報酬など) に影響を及ぼす可能性があります。
- 予測可能なランダム性: ランダム性のソースとして `block.number` や `block.difficulty` を使用すると、攻撃者は結果を予測し、操作できます。
- 悪用可能なアクセス制御: パーミッションやアクションについてブロックのタイムスタンプに頼るコントラクトは、タイムスタンプが調整されると、バイパスされる可能性があります。

## 対策
- **タイムスタンプベースの条件を避ける**: 可能な限り、タイムスタンプではなくブロック番号を使用します。ブロック番号は信頼性が高く、操作の対象にされにくくなります。
- **オラクルを使用する**: 時間的制約のあるコントラクトでは、外部の時間データを提供する、信頼できるオラクルを使用することを検討します。

## 事例

### 安全でないブロックタイムスタンプの使用 - タイムスタンプベースのデッドライン

```solidity
pragma solidity ^0.4.0;

contract TimestampExample {
    uint public deadline;

    function setDeadline(uint _deadline) public {
        deadline = _deadline;
    }

    function checkDeadline() public view returns (string) {
        if (now > deadline) {
            return "Deadline passed";
        } else {
            return "Deadline not passed";
        }
    }
}
```

上記の例では、`now` キーワードはブロックのタイムスタンプを取得し、期限と比較しています。これは、マイナーが事前に定義された枠内でブロックのタイムスタンプを操作できるため、潜在的な脆弱性が生じます。

### 修正したブロックタイムスタンプの使用
```solidity
pragma solidity ^0.4.0;

contract SafeTimestampExample {
    uint public deadline;
    uint public blockNumber;

    function setDeadline(uint _deadline) public {
        deadline = _deadline;
        blockNumber = block.number;
    }

    function checkDeadline() public view returns (string) {
        if (block.number > blockNumber + 1000) { // Assuming a reasonable number of blocks for a deadline
            return "Deadline passed";
        } else {
            return "Deadline not passed";
        }
    }
}
```
In this fixed version, the contract uses `block.number` instead of `now`. This makes the contract less susceptible to timestamp manipulation, as block numbers are more reliable and consistent.

### block.timestamp を使用した安全でない抽選

```solidity
pragma solidity ^0.8.0;

contract InsecureLottery {
    address[] public players;

    function enter() public payable {
        require(msg.value > 0.01 ether, "Minimum ETH required");

        players.push(msg.sender);
    }

    function pickWinner() public {
        uint index = uint(block.timestamp) % players.length; // Insecure: Predictable outcome
        payable(players[index]).transfer(address(this).balance);
    }
}
```
Issue:
- Predictability: Since `block.timestamp` is manipulable within a small range, miners can influence the winner selection.
- Attack Vector: A miner could reorder transactions to ensure a specific outcome

### 安全な代替手段

```solidity
pragma solidity ^0.8.0;

contract SecureLottery {
    address[] public players;

    function enter() public payable {
        require(msg.value > 0.01 ether, "Minimum ETH required");
        players.push(msg.sender);
    }

    function pickWinner() public {
        require(players.length > 0, "No players joined");
        uint index = uint(keccak256(abi.encodePacked(block.prevrandao, msg.sender, players.length))) % players.length;
        payable(players[index]).transfer(address(this).balance);
    }
}
```
Fixes:
- Uses block.prevrandao (EIP-4399) as an unpredictable source of randomness.
- Adds entropy from msg.sender and players.length to prevent miner manipulation

---
