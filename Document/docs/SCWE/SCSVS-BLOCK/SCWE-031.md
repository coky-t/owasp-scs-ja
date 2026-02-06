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
Ethereum のようなブロックチェーンネットワークでは、ブロック変数 `(block.timestamp, block.number, block.difficulty` / `block.prevrandao` post-merge など) がブロックチェーンの現在の状態に関する情報を提供します。しかし、これらの値は完全に決定論的ではなく、バリデータ (または PoW チェーン上のマイナー) によって操作される可能性があり、スマートコントラクトの脆弱性につながります。

ブロックのタイムスタンプは正確性や一貫性があることを保証されておらず、バリデータ (または PoW チェーン上のマイナー) は一定の範囲で影響を及ぼすことができます。トークン配布、アクセス制御、その他の時間的制約のあるイベントなど、重要な機能の正確なタイミングにコントラクトが依存している場合、これは問題を引き起こす可能性があります。

安全でないタイムスタンプの使用から生じる潜在的な問題には以下があります。

- タイムスタンプ操作: バリデータ (または PoW チェーン上のマイナー) は `block.timestamp` をわずかに改変して、時間的制約のあるロジック (オークション、トークン配布、ステーキング報酬など) に影響を及ぼす可能性があります。
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
        if (block.timestamp > deadline) {
            return "Deadline passed";
        } else {
            return "Deadline not passed";
        }
    }
}
```

上記の例では、`block.timestamp` キーワードはブロックのタイムスタンプを取得し、期限と比較しています。これは、マイナーが事前に定義された枠内でブロックのタイムスタンプを操作できるため、潜在的な脆弱性が生じます。

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
この修正版では、コントラクトは `now` ではなく `block.number` を使用します。これは、ブロック番号が信頼性が高く、一貫性があるため、コントラクトがタイムスタンプ操作の対象になりにくくなります。

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
        (bool ok, ) = payable(players[index]).call{value: address(this).balance}("");
        require(ok, "Transfer failed");
    }
}
```
問題点:
- 予測可能性: `block.timestamp` は狭い範囲内で操作可能であるため、バリデータ (または PoW チェーン上のマイナー) は勝者の選択に影響を及ぼす可能性があります。
- 攻撃ベクトル: バリデータ (または PoW チェーン上のマイナー) はトランザクションを並び替えて、特定の結果を確実にする可能性があります。

### 安全な代替手段 (Commit-Reveal)

```solidity
pragma solidity ^0.8.0;

contract SecureLottery {
    mapping(address => bytes32) public commits;
    mapping(address => bytes32) public revealed;
    address[] public players;

    function commit(bytes32 hash) external payable {
        require(msg.value > 0.01 ether, "Minimum ETH required");
        require(commits[msg.sender] == bytes32(0), "Already committed");
        commits[msg.sender] = hash;
        players.push(msg.sender);
    }

    function reveal(bytes32 secret) external {
        require(keccak256(abi.encodePacked(secret)) == commits[msg.sender], "Invalid reveal");
        revealed[msg.sender] = secret;
    }

    function pickWinner() external {
        bytes32 combined = bytes32(0);
        for (uint i = 0; i < players.length; i++) {
            require(revealed[players[i]] != bytes32(0), "All must reveal");
            combined = keccak256(abi.encodePacked(combined, revealed[players[i]]));
        }
        uint index = uint(combined) % players.length;
        (bool ok, ) = payable(players[index]).call{value: address(this).balance}("");
        require(ok, "Transfer failed");
    }
}
```
修正内容:
- commit-reveal を使用: 各プレイヤーはハッシュをコミットし、それからリビールします。シークレットの組み合わせで勝者を決定します。
- `block.prevrandao` と `block.timestamp` は value-at-stake のランダム性に関して **安全ではありません**。VRF (例: Chainlink) または commit-reveal を使用します。

---
