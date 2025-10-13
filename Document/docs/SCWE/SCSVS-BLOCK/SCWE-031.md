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
In blockchain networks like Ethereum, block variables `(block.timestamp, block.number, block.difficulty, etc.)` provide information about the current state of the blockchain. However, these values are not fully deterministic and can be manipulated by miners, leading to vulnerabilities in smart contracts.

Block timestamps are not guaranteed to be accurate or consistent, and miners can influence them within a certain range. This can cause issues when contracts depend on precise timing for critical functionality, such as token distribution, access control, or other time-sensitive events.

Potential issues that arise from insecure timestamp usage include:

- Timestamp Manipulation: Miners can slightly alter `block.timestamp` to influence time-sensitive logic (e.g., auctions, token distributions, staking rewards).
- Predictable Randomness: Using `block.number` or `block.difficulty` as a source of randomness allows attackers to predict and manipulate outcomes.
- Exploitable Access Control: Contracts that rely on block timestamps for permissions or actions may be bypassed if timestamps are adjusted.

## 対策
- **Avoid timestamp-based conditions**: Where possible, use block numbers instead of timestamps. Block numbers are more reliable and less subject to manipulation.
- **Use Oracles**: For time-sensitive contracts, consider using trusted oracles to provide external time data.

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

In the above example, the `now` keyword retrieves the block's timestamp to compare with the deadline. This creates a potential vulnerability as miners can manipulate the block timestamp within a predefined window.

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
