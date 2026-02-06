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
Ethereum スマートコントラクトにおいて、ブロック値 (`block.timestamp`, `block.number`, `block.difficulty` / `block.prevrandao` post-merge など) を時間の代わりとして使用すると、問題となる可能性があります。ブロック値はバリデータ (または PoW チェーン上のマイナー) によって決定され、一定の範囲で操作できるため、時間的制約のあるロジックには信頼できません。締め切りや有効期限などの重要な決定においてこれらの値に依存すると、バリデータ (または PoW チェーン上のマイナー) による操作や意図しないコントラクト状態など、予期しない動作をもたらす可能性があります。

## 対策
この脆弱性を緩和するには、ブロック値の操作範囲を考慮します。`block.timestamp` はブロックごとにおよそ 15 秒の誤差が生じる可能性があります。期限と時間のチェックはこの許容範囲を考慮して設計します。時間オラクルは一般的ではありません。標準的なアプローチは、適切なバッファで `block.timestamp` を使用することです。精度が重要な場合には、commit-reveal または複数ブロックの確認を検討します。

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
pragma solidity ^0.8.0;

contract TimeSensitive {
    uint public constant DEADLINE_BUFFER = 15; // seconds — accounts for block.timestamp manipulation
    uint public deadline;

    constructor(uint _deadline) {
        // Set deadline with buffer so expiry is robust to ~15s timestamp variance
        deadline = _deadline + DEADLINE_BUFFER;
    }

    function hasExpired() public view returns (bool) {
        return block.timestamp > deadline;
    }
}
```
**緩和策:** 期限を設定する際に `block.timestamp` の操作ウィンドウがおよそ 15 秒であることを考慮してバッファを追加します。一分未満の精度に依存しないようにします。
