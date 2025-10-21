---
title: 時間を代理としたブロック値 (Block Values as a Proxy for Time)
id: SCWE-065
alias: block-values-as-proxy-for-time
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-BLOCK]
  scsvs-scg: [SCSVS-BLOCK-2]
  cwe: [20]
status: new
---

## 関連
- CWE-20: Improper Input Validation
  [https://cwe.mitre.org/data/definitions/20.html](https://cwe.mitre.org/data/definitions/20.html) (参考: JVN iPedia [CWE-20 不適切な入力確認](https://jvndb.jvn.jp/ja/cwe/CWE-20.html))

## 説明
Ethereum スマートコントラクトにおいて、ブロック値 (`block.timestamp`, `block.number`, `block.difficulty` など) を時間の代わりとして使用すると、問題となる可能性があります。ブロック値はマイナーによって決定され、一定の範囲で操作できるため、時間的制約のあるロジックには信頼できません。締め切りや有効期限などの重要な決定においてこれらの値に依存すると、マイナーによる操作や意図しないコントラクト状態など、予期しない動作をもたらす可能性があります。

## 対策
To mitigate this vulnerability, avoid using block values as a direct proxy for time-based logic. Instead, consider using external oracles that provide reliable and tamper-proof time data or incorporate additional checks to prevent miner manipulation. Where necessary, combine block values with other data points to reduce the risk of exploitation.

### 脆弱なコントラクトの例
```solidity
contract TimeSensitive {
    uint public deadline;

    constructor(uint _deadline) {
        deadline = _deadline;
    }

    function hasExpired() public view returns (bool) {
        return block.timestamp > deadline;  // Relies on block.timestamp
    }
}
```

### 修正したコントラクトの例
```solidity
contract TimeSensitive {
    uint public deadline;
    address public oracle;

    constructor(uint _deadline, address _oracle) {
        deadline = _deadline;
        oracle = _oracle;
    }

    function hasExpired() public view returns (bool) {
        // Use a trusted oracle for time verification
        uint currentTime = Oracle(oracle).getCurrentTime();
        return currentTime > deadline;
    }
}
```
