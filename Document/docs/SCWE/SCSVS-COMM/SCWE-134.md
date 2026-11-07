---
title: 非コントラクトアドレスへの低レベル呼び出し (Low-Level Call to Non-Contract Address)
id: SCWE-134
alias: call-to-non-contract-address
platform: []
profiles: [L1]
mappings:
  scsvs-cg: [SCSVS-COMM]
  scsvs-scg: [SCSVS-COMM-1]
  cwe: [252]
status: new
---

## 関連
- CWE-252: Unchecked Return Value  
  [https://cwe.mitre.org/data/definitions/252.html](https://cwe.mitre.org/data/definitions/252.html)

## 説明
ユーザーが提供するアドレスや動的なアドレスに低レベルの `call`, `staticcall`, `delegatecall` を使用する場合、そのターゲットがコントラクトコードのない EOA (外部所有アカウント) である可能性があります。その場合には、呼び出しは `success = true` を返し、空の戻りデータを返しますが、コードは実行しません。ターゲットがロジック (コールバック、バリデーション、状態変更など) を実行していることを想定しているコントラクトは、誤った過程に基づいて処理される恐れがあり、チェックのバイパス、ロジックの未実行、不正確な会計処理につながります。

## 対策
- 低レベル呼び出しの前に、`extcodesize` (または Solidity 0.8.12 以降では `address.code.length`) を使用して、ターゲットアドレスがコードを含むことを検証します。
- 呼び出し可能なターゲットには、任意のアドレスを受け入れるのではなく、許可リストを使用します。
- ターゲットがコントラクトであることが知られている場合、高レベルインタフェース呼び出しを優先します。

## 事例

### 脆弱
```solidity
pragma solidity ^0.8.0;

contract PluginRegistry {
    function executeViaPlugin(address plugin, bytes calldata data) external {
        (bool ok, ) = plugin.call(data);
        require(ok, "plugin failed");
        // If plugin is an EOA, call "succeeds" but no code ran — logic may be bypassed
    }
}
```

### 修正済み
```solidity
pragma solidity ^0.8.0;

contract PluginRegistry {
    function executeViaPlugin(address plugin, bytes calldata data) external {
        require(plugin.code.length > 0, "target has no code");
        (bool ok, ) = plugin.call(data);
        require(ok, "plugin failed");
    }
}
```
