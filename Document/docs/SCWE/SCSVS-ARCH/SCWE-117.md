---
title: プロキシ実装の自己破壊の露出 (Proxy Implementation Selfdestruct Exposure)
id: SCWE-117
alias: proxy-implementation-selfdestruct
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-ARCH]
  scsvs-scg: [SCSVS-ARCH-1]
  cwe: [284]
status: new
---

## 関連
- CWE-284: Improper Access Control  
  [https://cwe.mitre.org/data/definitions/284.html](https://cwe.mitre.org/data/definitions/284.html)

## 説明
プロキシの実装コントラクトが `selfdestruct` (または関数を通じて到達可能な `SELFDESTRUCT`) を公開している場合、攻撃者や不注意な管理者がその実装を破壊できる可能性があります。そのため、プロキシは存在しないコードアドレスを指すことになり、アップグレードをできなくなったり、資金をロックします。

## 対策
- 実装の `selfdestruct` を削除または無効化し、`disableInitializers()` パターンを使用します。
- デストラクトのような機能にはタイムロック、マルチシグ、移行計画によるゲートを設けます。
- 実装アドレスを監視し、コードサイズをゼロにするようなアップグレードをブロックします。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract Impl {
    function kill() external {
        selfdestruct(payable(msg.sender));
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract Impl {
    // no selfdestruct path; migrations use new proxy with state copy
}
```
